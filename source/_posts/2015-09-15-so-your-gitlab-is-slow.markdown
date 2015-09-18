---
layout: post
title: "So, your Gitlab is slow?"
date: 2015-09-15 22:00:55 +0900
comments: true
categories: [tips]
tags: git,Gitlab
---

Our develop team use Gitlab for a long time, recently I get into some performance issues while using it. In this post, I will share our problem and some tips to investigate and improve it.

### Problems

Our develop team has about 100 individual developers, mainly working on a single big repository(about 1GB). As Gitlab CE's [official document](http://doc.gitlab.com/ce/install/requirements.html#cpu) suggests:

> 2GB RAM is the recommended memory size and supports up to 100 users

In the beginning of the project, we don't have so much developers, and source code is not so big. We used an EC2 _m3.large_ instance(2 CPU, 7.5GB MEM), it was OK. But soon, as developers and source code size increase, we find that the Gitlab website sometimes can not response quickly, and our front Nginx returns _502 Bad Gateway_. We thought is was because our EC2 instance is not powerful enough, so we upgrade to _m3.xlarge_(4CPU, 15GB MEM), it was a quick solution and worked well for some time.

But it does not last long, as our project is preparing to go to release cycle, and every sprint has strict deadline. Our developers tend to submit a lot of merge requests near the date of development deadline. And at that time, our Gitlab server has big problem in handling the merge request page:

- When developer **create** Merge Request, the MR page would open and Gitlab start to **checking merge request**, this process cost long time and sometimes it just not response with the result
- When developer **accept** Merge Request, Gitlab start the merge, and show a spinner, but it takes so long time(sometimes more than 30mins), and sometimes it just never stop

Even though we try to upgrade to a more powerful EC2 instance, it still can not solve the problem. I started to investigate the problem from source code.

### How Gitlab handles Merge Request

You can find Gitlab backend architecture from the [official document](http://doc.gitlab.com/ce/development/architecture.html#system-layout). The key part happens in `gitlab-satellites`.

If login to your Gitlab server, you can find two places which stores the git repository:

`/home/git/repositories`

this is where the real git repository is stored, which is called as the `bare repository`

`/home/git/gitlab-satellites`

this is a clone of the real repository from above folder. And most git operation will be happen in this **satellite** folder.

#### Check merge request
First, let's see how does Gitlab check if a merge request is mergeable.

When new merge request is created, it will call this function in [`merge_request_controller.rb`](https://gitlab.com/gitlab-org/gitlab-ce/blob/d321305c00f934db9becac1aa9726c3e9b400df5/app/controllers/projects/merge_requests_controller.rb#L140):

````ruby
def automerge_check
  if @merge_request.unchecked?
    @merge_request.check_if_can_be_merged
  end

  closes_issues

  render partial: "projects/merge_requests/widget/show.html.haml", layout: false
end
````

in [`merge_request.rb`](https://gitlab.com/gitlab-org/gitlab-ce/blob/d321305c00f934db9becac1aa9726c3e9b400df5/app/models/merge_request.rb#L207)

````ruby
def check_if_can_be_merged
  if Gitlab::Satellite::MergeAction.new(self.author, self).can_be_merged?
    mark_as_mergeable
  else
    mark_as_unmergeable
  end
end
````

finally in [`merge_action.rb`](https://gitlab.com/gitlab-org/gitlab-ce/blob/d321305c00f934db9becac1aa9726c3e9b400df5/lib/gitlab/satellite/merge_action.rb#L13):

````ruby
def can_be_merged?
  in_locked_and_timed_satellite do |merge_repo|
    prepare_satellite!(merge_repo)
    merge_in_satellite!(merge_repo)
  end
end

def merge_in_satellite!(repo, message = nil)
  update_satellite_source_and_target!(repo)

  message ||= "Merge branch '#{merge_request.source_branch}' into '#{merge_request.target_branch}'"

  # merge the source branch into the satellite
  # will raise CommandFailed when merge fails
  repo.git.merge(default_options({ no_ff: true }), "-m#{message}", "source/#{merge_request.source_branch}")
rescue Grit::Git::CommandFailed => ex
  handle_exception(ex)
end

# Assumes a satellite exists that is a fresh clone of the projects repo, prepares satellite for merges, diffs etc
def update_satellite_source_and_target!(repo)
  repo.remote_add('source', merge_request.source_project.repository.path_to_repo)
  repo.remote_fetch('source')
  repo.git.checkout(default_options({ b: true }), merge_request.target_branch, "origin/#{merge_request.target_branch}")
rescue Grit::Git::CommandFailed => ex
  handle_exception(ex)
end
````

As you can see. The final call is `merge_in_satellite!` method, which will run the `git merge` command in your git repository's satellite folder.

#### Accept Merge Request
When user click the **Accept Merge Request** button, here is what happens:

in [`merge_request_controller`](https://gitlab.com/gitlab-org/gitlab-ce/blob/d321305c00f934db9becac1aa9726c3e9b400df5/app/controllers/projects/merge_requests_controller.rb#L150), the `automerge` method will be called:

````ruby
def automerge
  return access_denied! unless @merge_request.can_be_merged_by?(current_user)

  if @merge_request.automergeable?
    AutoMergeWorker.perform_async(@merge_request.id, current_user.id, params)
    @status = true
  else
    @status = false
  end
end
````

We can find that it use an worker to perform an async task, this is a [Sidekiq](http://sidekiq.org/) task:

[`auto_merge_worker.rb`](https://gitlab.com/gitlab-org/gitlab-ce/blob/d321305c00f934db9becac1aa9726c3e9b400df5/app/workers/auto_merge_worker.rb#L6)
````ruby
class AutoMergeWorker
  include Sidekiq::Worker

  sidekiq_options queue: :default

  def perform(merge_request_id, current_user_id, params)
    params = params.with_indifferent_access
    current_user = User.find(current_user_id)
    merge_request = MergeRequest.find(merge_request_id)
    merge_request.should_remove_source_branch = params[:should_remove_source_branch]
    merge_request.automerge!(current_user, params[:commit_message])
  end
end
````

and it call the [`merge_request.rb`](https://gitlab.com/gitlab-org/gitlab-ce/blob/d321305c00f934db9becac1aa9726c3e9b400df5/app/models/merge_request.rb#L223) **auto_merge!** method:

````ruby
def automerge!(current_user, commit_message = nil)
  return unless automergeable?

  MergeRequests::AutoMergeService.
    new(target_project, current_user).
    execute(self, commit_message)
end
````

then it calls the [`auto_merge_service.rb`](https://gitlab.com/gitlab-org/gitlab-ce/blob/d321305c00f934db9becac1aa9726c3e9b400df5/app/services/merge_requests/auto_merge_service.rb#L8) **execute** method:

````ruby
def execute(merge_request, commit_message)
    merge_request.lock_mr

    if Gitlab::Satellite::MergeAction.new(current_user, merge_request).merge!(commit_message)
      merge_request.merge

      create_merge_event(merge_request, current_user)
      create_note(merge_request)
      notification_service.merge_mr(merge_request, current_user)
      execute_hooks(merge_request, 'merge')

      true
    else
      merge_request.unlock_mr
      false
    end
....
````

Here, it use the same `MergeAction` class, but called a different method:

````ruby
def merge!(merge_commit_message = nil)
  in_locked_and_timed_satellite do |merge_repo|
    prepare_satellite!(merge_repo)
    if merge_in_satellite!(merge_repo, merge_commit_message)
      # push merge back to bare repo
      # will raise CommandFailed when push fails
      merge_repo.git.push(default_options, :origin, merge_request.target_branch)

      # remove source branch
      if merge_request.remove_source_branch?
        # will raise CommandFailed when push fails
        merge_repo.git.push(default_options, :origin, ":#{merge_request.source_branch}")
        merge_request.source_project.repository.expire_branch_names
      end
      # merge, push and branch removal successful
      true
    end
  end
rescue Grit::Git::CommandFailed => ex
  handle_exception(ex)
end
````

Now you see, the different of `Accept Merge Request` is that it will execute the git `push` command after it merge in the satellite folder, that is absolutely what `Accept` should do.

You may think that, the **Accept** process is asynchronous, **Check** is synchronous, so at least these two process can be done in parallel. But there is one more thing we should see, in both `can_be_merged?` and `merge!` methods, there is a `in_locked_and_timed_satellite` method, we can find the souce in `action.rb`, but the actual implementation is in [`satellite.rb`](https://gitlab.com/gitlab-org/gitlab-ce/blob/d321305c00f934db9becac1aa9726c3e9b400df5/lib/gitlab/satellite/satellite.rb#L59)

````ruby
# * Locks the satellite
# * Changes the current directory to the satellite's working dir
# * Yields
def lock
  project.ensure_satellite_exists

  File.open(lock_file, "w+") do |f|
    begin
      f.flock File::LOCK_EX
      yield
    ensure
      f.flock File::LOCK_UN
    end
  end
end
````

In every merge or check merge, it locks the satellite by [lock](http://ruby-doc.org/core-2.1.5/File.html#method-i-flock) the file. So even the accept process is asynchronous by Sidekiq, but it has to wait for the file lock if some other check or accept process is running. This whole strategy make all **check** and **accept** process running in sequential order. So when you have many developers Create/Accept merge request at same time, all process will be running in sequential, and that's why you have to wait long time for your merge request.

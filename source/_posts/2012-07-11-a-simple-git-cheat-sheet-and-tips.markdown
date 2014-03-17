---
layout: post
title: "A simple Git cheat sheet and tips"
date: 2012-07-11
comments: true
categories: [tips, cheetsheet]
---

Here is a simple Git cheat sheet and I will update this when I find new ones.

<!--more-->

## Cheat Sheet:

### config:

     $ git config --global user.name "John Doe"
     $ git config --global user.email johndoe@example.com
     
   this will store your identity in _~/.gitconfig_, use `--system` to store in _/etc/gitconfig_, use `--local` to specify a per-repository configuration, which stores in _.git/config_.

### log

    | git log                                           | view commit log                    |
    | git log -p -2                                     | show patch of last 2 commit        |
    | git log --stat                                    | show simple state                  |
    | git log --pretty=oneline                          | show in other formats              |
    | git log --graph --all --decorate --pretty=oneline | show all branch commits with graph |


### undo things

    | git commit --amend       | this will overwrite the last commit |
    | git reset HEAD~1         | discard last commit                 |
    | git reset HEAD filename  | unstage a file                      |
    | git checkout -- filename | undo modified file                  |

### remote

    | git remote -v                    | show remote repository  |
    | git remote add [shortname] [url] | add remote repository   |
    | git fetch [remote]               | fetch remote            |
    | git remote show [shortname]      | show remote infomation  |
    | git remote rename [from] [to]    | rename remote shortname |
    | git remote rm [remote]           | remove remote           |

## Tricks & Tips

### clone into an existing directory

clone a repo into an existing directory, 

    $ cd PATH/YOU/WANT/TO/PUT/GIT/FILES
    $ git init
    $ git remote add origin git://remote.repo.url.path
    $ git fetch
    $ git checkout -t origin/master

`fetch` will get all remote branches to local, but we still need the `checkout` command to switch to a specific branch,  `-t` implies `-b`, which tells git to create a local **master** branch and make it to track the remote master branch, which is **origin/master**.

### view the history of a single file

    $ git log -p filename
    $ git whatchanged -p filename

    $ git log -p --follow filename
    $ git blame filename
    
    $ gitk filename

### checkout a single file from another branch

    $ git checkout branch_1 -- path/to/file

### revert to previous commit/discard current commit
  if you doesn't push your commit:

    git reset --hard HEAD~1
  
  if you already push your commit:
  
    git revert HEAD

### rewrite last commit message

  see this[stackoverflow questions](http://stackoverflow.com/questions/179123/how-do-i-edit-an-incorrect-commit-message-in-git)

    git commit --amend -m "New commit message"

### discard all modify

    git checkout .

### working with remotes
   
  create remote branch
   
    $ git push REMOTENAME BRANCHNAME
    
  delete remote branch 

    $ git push REMOTENAME :BRANCHNAME

  pushing tags

    $ git push REMOTENAME TAGNAME
    $ git push REMOTENAME BRANCHNAME --tags

  push to multiple remotes at the same time

    $ vim .git/config
    [remote "all"]
    url = git@github.com:luoshupeng/commonuseppa.git
    url = git@gitcafe.com:chinesedragon/commonuseppa.git
    url = chinesedragon@gitcd.com:/commonuseppa
    $ git push all

this will push to all urls

### show/hide untracked files

    $ git status -u   #show only untracked files
    $ git status -uno #don't show untracked files
    $ git config status.showuntrackedfiles no

    # List git-ignored files
    $ git ls-files . --ignored --exclude-standard --others

    # List untracked files
    $ git ls-files . --exclude-standard --others

### checkout single file from stash

  see more [here](http://stackoverflow.com/questions/1105253/how-would-i-extract-a-single-file-or-changes-to-a-file-from-a-git-stash)

    $ git checkout stash@{0} -- <filename>

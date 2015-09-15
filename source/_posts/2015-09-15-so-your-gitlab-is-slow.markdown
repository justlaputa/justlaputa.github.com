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

First, we can find Gitlab backend architecture from the [official document](http://doc.gitlab.com/ce/development/architecture.html#system-layout). The key part happens in `gitlab-satellites`.

If login to your Gitlab server, you can find two places which stores the git repository:

`/home/git/repositories`

this is where the real git repository is stored, which is called as the `bare repository`

`/home/git/gitlab-satellites`

this is a clone of the real repository from above folder. And most git operation will be happen in this **satellite** folder.

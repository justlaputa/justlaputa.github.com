---
layout: post
title: "My first chrome extension"
date: 2013-06-26 21:02
comments: true
categories: [project]
---

Yesterday I just published my first chrome extension *Jenkins Monitor* in [Chrome Webstore](https://chrome.google.com/webstore/detail/jenkins-monitor/chhebdmlkojbdgadopnedfmoapbeiofi), finally.

I checker my [commit history](https://github.com/justlaputa/jenkins-monitor/commits/master) in [Github](https://github.com/justlaputa/jenkins-monitor), it was started at **Apr 24**, and after almost 2 months, I released and published the first version, though it only has very simple and limited features.

<!--more-->

The idea is from my daily work, my team use [Jenkins CI](http://jenkins-ci.org/) every day to build and test our project. Our Jenkins is hosted on [EC2](https://aws.amazon.com/ec2/), and the hardware is not so powerful(maybe micro instance, not sure). So sometimes it is very slow to load the Jenkins web page in our browser. Then I decide to find some way to solve this problem. Luckily, Jenkins has [REST API](https://wiki.jenkins-ci.org/display/JENKINS/Remote+access+API), and I'm doing front-end development for some time, making a chrome extension to use the REST API would be a good solution, and I was always thinking to try to develop a chrome extension. So I just started this project, easily.

Still, it surprised me at the first that Chrome Extension development is so easy, all the things I need is just front-end web development technology. HTML, CSS, JS, it is just like make web pages. Thanks to my job, I use them everyday. What's more, I have powerful tools to help me make this extension, like [jQuery](http://jquery.com/), [Bootstrap](http://twitter.github.io/bootstrap/), [Font Awesome](http://fortawesome.github.io/Font-Awesome/), [Handlebars](http://handlebarsjs.com/), [Gruntjs](http://gruntjs.com/), etc.

These tools saved my a lot of time, and makes the implementation simple. I just need to use jQuery [ajax](http://api.jquery.com/jQuery.ajax/) call to get all jobs data from jenkins API, then feed the data to handlebar compiled templates, then I have a jenkins jobs list rendered by properly by Bootstrap css:

![Jenkins Job List](https://lh3.googleusercontent.com/-NKsuyHoB4tc/UcrwpFDiI7I/AAAAAAAAA2g/gcsqCiIXSUw/w1117-h698-no/Screen+Shot+2013-06-24+at+10.36.30+PM.png "Jenkins job list")

Then I want to automatically update the jobs data from jenkins server, I need a timer. I found that I can use chrome extension [alarm](http://developer.chrome.com/extensions/alarms.html) API to achieve this.

Last feature before I can release is *Notification*, I want my jenkins monitor to notify me when there are jobs `added/removed/start/done`. Not surprisingly, chrome supports [Desktop Notification](http://developer.chrome.com/extensions/desktop_notifications.html), which is just like [HTML5 Notification API](http://www.html5rocks.com/en/tutorials/notifications/quick/), it is quite simple to use and I just use one after work night to implement this:

![Jenkins monitor notification](https://lh3.googleusercontent.com/-zOOMaQu8ZRo/Ucrwq0p8UfI/AAAAAAAAA2o/MPkDGcFo6lQ/w640-h400-no/Screen+Shot+2013-06-24+at+11.04.31+PM.png notification)

That's all I need for now! I already asked my colleague to try it and he says it is good. Though it does not have much features,  I still decide to publish it to the chrome webstore. I think making progress is good for me, and I already have plan to improve it in the [next version](https://github.com/justlaputa/jenkins-monitor/issues?milestone=1&page=1&state=open).

If you are interested to try, just install it from [chrome web store](https://chrome.google.com/webstore/detail/jenkins-monitor/chhebdmlkojbdgadopnedfmoapbeiofi), and of cause code contribution is welcomed in [github](https://github.com/justlaputa/jenkins-monitor/issues?milestone=&page=1&state=open).
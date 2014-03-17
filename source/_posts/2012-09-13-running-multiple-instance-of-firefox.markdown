---
layout: post
title: "Running multiple instance of firefox"
date: 2012-09-13
comments: true
categories: [tutorial, browser]
---

  I'm doing web development nowadays, using [Firefox](http://www.mozilla.org/en-US/firefox), still my favorite browser and develop platform, though I will also use [Chrome](http://www.google.com/chrome) some time.

<!--more-->
  
  As you may know that firefox does not allow multiple instance to run at the same time, which means if you try to open firefox again(with desktop shortcut or command line) when you already is running one, it will give a message saying:
  
>A copy of Firefox is already open. Only one copy of Firefox can be open at a time.

  A very long time before, I had wanted to run multiple fireboxes at the same time, but no lucky. Until yesterday I read a [blog post](http://www.callum-macdonald.com/about/faq/multiple-firefox-instances/) in my [google reader](www.google.com/reader), I see this feature again. And what's lucky that I decide to try it. What I get is more than I wanted.
  
  So, here is how to do it:
  
  First, we need to know something about firefox's command line options(running on Mac OSX with ff 15.0.1):
  
    $ firefox --help
    Firefox options
    -h or -help        Print this message.
    -v or -version     Print Firefox version.
    -P <profile>       Start with <profile>.
    -migration         Start with migration wizard.
    -ProfileManager    Start with ProfileManager.
    -no-remote         Do not accept or send remote commands; implies -new-instance.
    -new-instance      Open new instance, not a new window in running instance.
    -UILocale <locale> Start with <locale> resources as UI Locale.
    -safe-mode         Disables extensions and themes for this session.
    -jsconsole         Open the Error console.
    -browser           Open a browser window.
    -new-window  <url> Open <url> in a new window.
    -new-tab     <url> Open <url> in a new tab.
    -preferences       Open Preferences dialog.
    -search     <term> Search <term> with your default search engine.
    -private           Enable private browsing mode.
    -private-toggle    Toggle private browsing mode.
    -setDefaultBrowser Set this app as the default browser.
 
 but today we only need this three:
  
 * -no-remote  
   Enables [running multiple instances of the application with different profiles](http://kb.mozillazine.org/Opening_a_new_instance_of_your_Mozilla_application_with_another_profile); used with -P 

 * -ProfileManager  
   Starts with Profile Manager.

 * -P "&lt;profile name>"  
   Starts with a given profile name (profile name is case sensitive). 
   
first running firefox with `-ProfileManager`, let's create a separate profile:

    $ firefox -ProfileManager

this will open a dialog to let us create/delete/rename profiles, just create a new one, and name it. like `develop`.

  Then run firefox normally, like we always did after open the computer. Now firefox is running with the default profile, and if we open it again, it will give the normal `Only one instance` warning. Let's do it in command line way:

    $ firefox -no-remote -P "develop"

  Da da!! Now we have two firefox, in _**separate instance**_. Which also means **double memory use..**

  You may ask, what's the point? Why we need two instance of firefox? Two make two cups of [coffee](https://bugzilla.mozilla.org/show_bug.cgi?id=46647)? Well, let me tell you what this is worth a blog post.

  The point is not only another instance of firefox, but also a separate **Profile**. As I use firefox every day and keep it open from opening the computer till leaving office. I use it to read mails, review code, check new issues, search google, etc. And also, I use it to develop, test website, open firebug, debug javascript, etc. Currently I am doing this all in a single firefox window, and some times firebug makes my FF very slow, it influence my other work.

  Now, with this profile manager, I can make a new clean profile, with only develop add-ons installed, and open another firefox instance, only use it to do web development, doesn't this sounds awesome?! What's more, we can set up shortcuts for this _clean development_ firefox instance, and open it in another workspace, without influence my daily web works. I will do this in my [awesome](http://awesome.naquadah.org/) configuration file of Ubuntu 12.04 after I go to office tomorrow. It will be interesting, and later I will try [Firefox 17](www.mozilla.org/en-US/firefox/aurora/)'s new [web development tools](https://hacks.mozilla.org/2012/09/html-editing-and-other-improvements-in-firefox-17-developer-tools/).

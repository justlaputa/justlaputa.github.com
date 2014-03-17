---
layout: post
title: "Test MediaElement.js locally"
date: 2013-03-05 20:21
comments: true
categories: [tutorial]
---

Today I was trying to use [mediaelement.js](http://mediaelementjs.com/) to play mp3 audio in browsers. I thought it was easy, but still I've met problems, mainly with [Firefox](https://www.mozilla.org/en-US/firefox/).

<!--more-->

First, I want to use static local html file to test the library, which is fast to do. Here is the code:

```
$ tree mejs
mejs
├── css
│   ├── background.png
│   ├── bigplay.png
│   ├── bigplay.svg
│   ├── controls-ted.png
│   ├── controls-wmp-bg.png
│   ├── controls-wmp.png
│   ├── controls.png
│   ├── controls.svg
│   ├── loading.gif
│   └── mediaelementplayer.css
├── index.html
├── js
│   ├── app.js
│   ├── flashmediaelement.swf
│   ├── jquery.js
│   └── mediaelement-and-player.js
└── media
    └── music.mp3

3 directories, 16 files
```

**index.html** :

```
<!doctype html>
<html>
<head>
    <link type="text/css" rel="stylesheet" href="css/mediaelementplayer.css" />
    <script src="js/jquery.js"></script>
    <script src="js/mediaelement-and-player.js"></script>
</head>
<body>
    <audio type="audio/mp3" src="media/music.mp3" controls></audio>
    <script src="js/app.js"></script>
</body>
</html>
```

**app.js** :

```
$(function() {
    $('audio').mediaelementplayer();
});
```

As firefox does not support [MP3 format](https://developer.mozilla.org/en-US/docs/HTML/Supported_media_formats#compat-desktop), if we don't include the `app.js` file to use mediaelement.js, the browser will not show the audio bar, and gives an error in js console:

> HTTP "Content-Type" of "audio/mpeg" is not supported. Load of media resource file:///Users/hanxiao/workspace/test/mejs/media/music.mp3 failed. @ file:///Users/hanxiao/workspace/test/mejs/index.html

So we include `app.js` and call `mediaelementplayer()` function to let mediaelement.js use the flash fallback to play mp3 in firefox. But problems happens here, the audio bar shows, but no control icon, and the audio can not play.

The reason is that flash plugin has [security control](https://www.macromedia.com/support/documentation/en/flashplayer/help/settings_manager04.html#117502), which prevents flash plugin to access the local mp3 file. To allow this, open a flash video site like [youtube](https://www.youtube.com/) or [youku](http://www.youku.com/) if you can't. Open one video page, and wait the video to play, not we have a running flash plugin, right click on it and choose **Global Settings…->Advanced->Trusted Location Settings…** add the directory of the local site to it, then open `index.html` in firefox, now the mp3 plays like a charm.

Something intresting: though firefox declares not support mp3, but when I open the mp3 file directly in firefox, it plays well, and the inspector show this code:

```
<embed type="audio/mpeg" src="file:///Users/hanxiao/workspace/test/mejs/media/music.mp3" name="plugin" height="100%" width="100%">
```

Do you know why? Please comment if you do, cause currently I don't..

Update: Created a [github repo](git@github.com:justlaputa/mejs-test.git) about this code.

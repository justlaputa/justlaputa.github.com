---
layout: post
title: "FFmpeg Practice Commands"
date: 2012-08-26
comments: true
categories: Tips&Tricks
tags: [ffmpeg]
---

<!--more-->

## Convert video for HTC Desire by FFmpeg 
   
    ffmpeg -i inputfile.mpg \
    -aspect 16:9 -s 800x480 \
    -vcodec libx264 -b 480k -vpre slow_firstpass -vpre baseline -r 13 \
    -acodec libfaac -ab 128k -sameq \
    -pass 1 -f rawvideo -an -y /dev/null && \
    ffmpeg -i inputfile.mpg \
    -aspect 16:9 -s 800x480 \
    -vcodec libx264 -b 480k -vpre slow -vpre baseline -r 13 \
    -acodec libfaac -ab 128k -ac 2 -sameq \
    -pass 2 outputfile.mp4

    ffmpeg -y -i Pixar.Short.Films.Collection.2008.Presto.1080p.BluRay.x264.AC3-WiKi.mkv  -s 800x450 -aspect 16:9 -r 24000/1001 -b 480k -vcodec libx264 -pass 1 -vpre fast_firstpass -vpre baseline -an -f rawvideo /dev/null && ffmpeg -y -i Pixar.Short.Films.Collection.2008.Presto.1080p.BluRay.x264.AC3-WiKi.mkv -s 800x450 -aspect 16:9 -r 24000/1001 -b 480k -vcodec libx264  -vpre slow -vpre baseline -acodec libfaac -ar 32k -ab 128k -ac 2 -sameq -pass 2 out.mp4
    
    ffmpeg -y -i Pixar.Short.Films.Collection.2008.Presto.1080p.BluRay.x264.AC3-WiKi.mkv  -s 320x180 -vf pad=320:240:0:30 -r 24000/1001 -b 240k -vcodec libx264 -pass 1 -vpre fast_firstpass -vpre baseline -an -f rawvideo /dev/null && ffmpeg -y -i Pixar.Short.Films.Collection.2008.Presto.1080p.BluRay.x264.AC3-WiKi.mkv -s 320x180 -vf pad=320:240:0:30 -r 24000/1001 -b 240k -vcodec libx264  -vpre slow -vpre baseline -acodec libfaac -ar 32k -ab 128k -ac 2 -sameq -pass 2 out_320x240.mp4

## keep ratio
   
  ffmpeg -filters:
  
  Pad input image to width:height[:x:y[:color]] (default x and y: 0, default color: black). from 1920x1080 to 800x480

    ffmpeg -i in -s 800x450 -vf pad=800:480:0:15 out

## tricks

   - grab screen
    
        ffmpeg -f x11grab -r 25 -s wxga -i :0.0 /tmp/outputFile.mpg
   
   - extrac image
   
       1 image per second(default is 25 without -r)
     
        ffmpeg -i test.mpg -r 1 image%d.jpg

   - image to video

        ffmpeg -f image2 -i img%d.jpg /tmp/a.mpg

   - capture from cam and mic

        ffmpeg -f oss -i /dev/dsp -f video4linux2 -s 320x240 -i /dev/video0 out.mpg

## configure options

   - install:

      dependence: yasm faad2-devel faac-devel lame-devel opencore-amr-devel openjpeg-devel libtheora-devel libvpx-devel libvorbis-devel xvidcore-devel

        ./configure --prefix=/usr --disable-static --enable-shared --enable-gpl --enable-version3 --enable-nonfree --enable-postproc --enable-x11grab --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libfaac --enable-libmp3lame --enable-libopenjpeg --enable-libtheora --enable-libvorbis --enable-libvpx --enable-libx264 --enable-libxvid --disable-debug

        (optional) --enable-libvo-aacenc --enable-libvo-amrwbenc

        make && sudo make install

   - build & install libx264

        ./configure --prefix=/usr --enable-shared
        make && sudo make install

   - reconfigure ffmpeg add --enable-libx264
  
        make && make 

## install libvo-aacenc libvo-amrwbenc

   - get from opencore-amr sourceforge

   - install:

        $ autoreconf
        $ ./configure
        $ make && sudo make install
     
## output rtp

    ffmpeg -re -i ~/Videos/test/Ice.Age.Continental.Drift_CIF_352x288.mp4 \
    -an -vcodec libx264 -r 20 -g 45 -s 352x288 -deinterlace -f rtp rtp://127.0.0.1:9012 \
    -vn -acodec libfaac -ac 2 -ab 192k -ar 44100 -async 1 -flags +global_header -f rtp rtp://127.0.0.1:9014rtp://127.0.0.1:9014




  
   

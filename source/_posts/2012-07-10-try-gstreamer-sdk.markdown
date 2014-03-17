---
layout: post
title: "初探Gstreamer SDK"
date: 2012-07-10
comments: true
categories: [linux, tutorial]
---

[Gstreamer SDK](http://gstreamer.com/) 已经发布一段时间了，一直苦于没有Linux机器，也就没有尝试。现在有机会了，于是就跟着[官方教程](http://docs.gstreamer.com/display/GstSDK/Tutorials)来试试。

这里只介绍在Fedora Linux下如何安装以及使用Gstreamer SDK，其他发行版可以根据教程来，应该大同小异。

<!--more-->

## 安装SDK ##
首先把gstreamer的repo下载并放到yum的repo.d 文件夹中，然后就能安装 sdk 了：

    $ wget http://www.freedesktop.org/software/gstreamer-sdk/data/packages/2012.5/fedora/gstreamer-sdk.repo
    $ sudo cp gstreamer-sdk.repo /etc/yum.repo.d/
    $ sudo yum install gstreamer-sdk-devel
    
gstreamer 的 sdk 会被安装在 /opt/gstreamer-sdk 下，我们可以简单看看 sdk 帮我们安装了哪些东西。在 /opt/gstreamer-sdk 目录下，基本是一个完整的编译环境：

    $ ls /opt/gstreamer-sdk
    bin/  etc/  include/  lib/  libexec/  share/
    
**bin** 下面是一些帮助程序，包括了常用的 *gst-inspect* 和 *gst-launch*。**include** 目录下包含了编译 gstremer 程序所需要的头文件，包括 *gtk*, *glib*, 甚至很多解码库的头文件也有，比如 *FLAC*, *speex* 等，而 **lib** 目录下包含了众多静态和动态库文件也是可想而知的了。可见　SDK 只是提供了编译时的依赖，使得　gstreamer 程序能够顺利编译通过。

## 编译示例代码 ##

sdk 中已经包括了一些示例源代码，将/opt/gstreamer-sdk/share/gst-sdk/tutorials 复制到工作目录，或者直接从官方的 git 仓库　clone　一个：

    $ git clone git://anongit.freedesktop.org/gstreamer-sdk/gst-sdk-tutorials
    
进入到工作目录，根据教程的指示，在编译之前需要运行　sdk　提供的一个脚本配置好编译环境：

    $ cd ~/workspace/gst-sdk/tutorials
    $ /opt/gstreamer-sdk/bin/gst-sdk-shell
    
我们可以简单看看这个　shell　脚本做了什么：

    #!/bin/sh

    export XDG_CONFIG_DIRS="/opt/gstreamer-sdk/etc/xdg${XDG_CONFIG_DIRS:+:$XDG_CONFIG_DIRS}:/etc/xdg"
    export LDFLAGS="-L/opt/gstreamer-sdk/lib ${LDFLAGS}"
    export XDG_DATA_DIRS="/opt/gstreamer-sdk/share${XDG_DATA_DIRS:+:$XDG_DATA_DIRS}:/usr/local/share:/usr/share"
    export PYTHONPATH="/opt/gstreamer-sdk/lib/python2.7/site-packages${PYTHONPATH:+:$PYTHONPATH}"
    export GIO_EXTRA_MODULES="/opt/gstreamer-sdk/lib/gio/modules"
    export CXXFLAGS="-I/opt/gstreamer-sdk/include ${CXXFLAGS}"
    export GST_PLUGIN_SCANNER="/opt/gstreamer-sdk/libexec/gstreamer-0.10/gst-plugin-scanner"
    export CFLAGS="-I/opt/gstreamer-sdk/include ${CFLAGS}"
    export PKG_CONFIG_PATH="/opt/gstreamer-sdk/lib/pkgconfig:/opt/gstreamer-sdk/share/pkgconfig${PKG_CONFIG_PATH:+:$PKG_CONFIG_PATH}"
    export PATH="/opt/gstreamer-sdk/bin${PATH:+:$PATH}:/usr/local/bin:/usr/bin:/bin"
    export GST_REGISTRY="${HOME}/.gstreamer-0.10/gstreamer-sdk-registry"
    export LD_LIBRARY_PATH="/opt/gstreamer-sdk/lib${LD_LIBRARY_PATH:+:$LD_LIBRARY_PATH}"


    $SHELL "$@"

可以看到这个 shell　帮我们设置了很多环境变量，而这些环境变量几乎都是跟编译有关的，比较重要的几个是　*LDFLAGS*, *CXXFLAGS*, *CFLAGS*, *PATH*, 他们都把　/opt/gstreamer-sdk 下的相应目录添加到了这些变量中，为的就是在编译源码时能够引用到这些文件。

运行完这个脚本后，就可以开始编译示例代码了

    $ gcc basic-tutorial-1.c -o basic-tutorial-1 `pkg-config --cflags --libs gstreamer-0.10`
   
可以看看后面的　pkg-config 命令得到的是哪些参数：

    $ pkg-config --cflags --libs gstreamer-0.10
    -pthread -I/opt/gstreamer-sdk/include/gstreamer-0.10 -I/opt/gstreamer-sdk/include/glib-2.0 -I/opt/gstreamer-sdk/lib/glib-2.0/include -I/opt/gstreamer-sdk/include/libxml2  -pthread -L/opt/gstreamer-sdk/lib -lgstreamer-0.10 -lgobject-2.0 -lgmodule-2.0 -lgthread-2.0 -lrt -lxml2 -lglib-2.0
    
记得上面的　gst-sdk-shell 脚本中设置了　*PKG_CONFIG_PATH* 这个变量，于是这里在运行　pkg-config 命令的时候，也是使用的 sdk 目录下的　pkgconfig 文件。现在我们可以试着运行编译出的程序:

    $ ./basic-tutorial-1

    (basic-tutorial-1:2928): GStreamer-CRITICAL **: gst_element_set_state: assertion `GST_IS_ELEMENT (element)' failed

    (basic-tutorial-1:2928): GStreamer-CRITICAL **: gst_element_get_bus: assertion `GST_IS_ELEMENT (element)' failed

    (basic-tutorial-1:2928): GStreamer-CRITICAL **: gst_bus_timed_pop_filtered: assertion `GST_IS_BUS (bus)' failed

    (basic-tutorial-1:2928): GStreamer-CRITICAL **: gst_object_unref: assertion `object != NULL' failed

    (basic-tutorial-1:2928): GStreamer-CRITICAL **: gst_element_set_state: assertion `GST_IS_ELEMENT (element)' failed

    (basic-tutorial-1:2928): GStreamer-CRITICAL **: gst_object_unref: assertion `object != NULL' failed
    
发现并不能运行，原因是当前我们在 sdk-shell 所设置的环境中，而这个环境已经改变了　*LD_LIBRARY_PATH* 的值，在运行 basic-tutorial-1 的时候，gstreamer 会在 LD_LIBRARY_PATH 指定的目录中寻找所有插件的动态库，我们在这里可以尝试运行　gst-inspect:

    $ echo $LD_LIBRARY_PATH
    /opt/gstreamer-sdk/lib
    $ gst-inspect | less
    
可以发现在其中是找不到　*playbin2* 的，原因就是因为　sdk 的　lib 目录中没有　libgstplaybin.so　这个动态库。切换到另一个没有运行过 gst-sdk-shell　的终端，或者直接退出重新开终端，试着运行一下　basic-tutorial-1　这个程序，如果你的系统安装了　gstreamer　的话，这个程序应该是能跑起来的。


这时我们可以充分体会　sdk 只是提供了编译环境，而非运行环境了。实际上，只要你的系统安装了 gstreamer-devel，不需要运行上述的 sdk-shell　也能编译，并且能直接运行编译好的程序，但是这依赖于系统安装了 gstreamer　的编译以及运行环境，这也是以前开发gstreamer　应用的方式。sdk 的好处在于便于分发，以及统一跨平台的开发，类似　android 的开发环境，保证编译通过就能扔到手机上跑，所以还是很期待　gstreamer sdk　出　android 版本的。

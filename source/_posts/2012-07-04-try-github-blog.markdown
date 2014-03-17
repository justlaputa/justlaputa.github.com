---
layout: post
title: "在 Github 上写 Blog"
date: 2012-07-04
comments: true
categories: [tutorial]
---

我正在github上尝试用pelican写blog

<!--more-->

## 安装pelican ##

首先使用easy_install安装pip：

    sudo easy_install pip
  
然后使用pip安装pelican以及其他所需的包

    sudo pip install pelican
    sudo pip install Markdown

接下来就可以使用pelican生成html了。

## 使用pelican生成blog ##

新建文件夹，在其中编写markdown文档

    cd ~
    mkdir blog
    cd blog & mkdir code
    cd code & mkdir post
    vim settings.py
    
首先新建一个[配置文件](http://pelican.notmyidea.org/en/2.8/settings.html)，样例如下：

    # -*- coding: utf-8 -*-
    AUTHOR = u'Alexis Métaireau'
    SITENAME = u"Alexis' log"
    SITEURL = 'http://blog.notmyidea.org'
    TIMEZONE = "Europe/Paris"

    GITHUB_URL = 'http://github.com/ametaireau/'
    DISQUS_SITENAME = "blog-notmyidea"
    PDF_GENERATOR = False
    REVERSE_CATEGORY_ORDER = True
    LOCALE = ""
    DEFAULT_PAGINATION = 2

    FEED_RSS = 'feeds/all.rss.xml'
    CATEGORY_FEED_RSS = 'feeds/%s.rss.xml'

    LINKS = (('Biologeek', 'http://biologeek.org'),
    ('Filyb', "http://filyb.info/"),
    ('Libert-fr', "http://www.libert-fr.com"),
    ('N1k0', "http://prendreuncafe.com/blog/"),
    (u'Tarek Ziadé', "http://ziade.org/blog"),
    ('Zubin Mithra', "http://zubin71.wordpress.com/"),)

    SOCIAL = (('twitter', 'http://twitter.com/ametaireau'),
    ('lastfm', 'http://lastfm.com/user/akounet'),
    ('github', 'http://github.com/ametaireau'),)

    # global metadata to all the contents
    DEFAULT_METADATA = (('yeah', 'it is'),)

    # static paths will be copied under the same name
    STATIC_PATHS = ["pictures",]

    # A list of files to copy from the source to the destination
    FILES_TO_COPY = (('extra/robots.txt', 'robots.txt'),)

    # foobar will not be used, because it's not in caps. All configuration keys
    # have to be in caps
    foobar = "barbaz"

在post文件夹中新建md文件，并在其中编写如下内容

    Date: 2012-07-010
    Title: 在 Github 上写 Blog
    Category: Tutorial
    Slug: first-blog
    Lang: zh
    Author: username
    
    my first blog
    
然后使用pelican命令生成html：

    pelican -s settings.py post -o ..
    

---
layout: post
title: "Blog URL changed"
date: 2013-05-14 22:40
comments: true
categories: [tutorial]
---

今天我终于把2年半前注册的域名 [justlaputa.com](http://justlaputa.com) 成功重定向到 Github pages 了。

之前试过 DNS forward，但是URL不能完美显示，今天无意中想找找有没有好办法把域名重定向到 Github，意料之外找到了Github 的官方配置方法 [Setting up a custom domain with Pages](https://help.github.com/articles/setting-up-a-custom-domain-with-pages)，居然成功了！

<!--more-->

完全按照教程来就OK了：

1. 在github pages repository下面新建一个文件 [CNAME](https://github.com/justlaputa/justlaputa.github.com/blob/master/CNAME)，里面写上想要map的域名，这个文件会告诉github pages把 `*.github.io` 重定向到自己的域名（try [justlaputa.github.io](http://justlaputa.github.io)）
2. 修改域名的[A record](https://en.wikipedia.org/wiki/List_of_DNS_record_types) 为 github pages service ip：`204.232.175.78`

然后等待DNS修改生效就OK了！

Awesome Github!!

---
layout: post
title: "Eclipse & Emacs"
date: 2012-08-08
comments: true
categories: [tips]
---

This post is about how I tweak Eclipse into a Emacs like IDE, and now I'm very happy using Eclipse.


<!--more-->

## Emacs Key-bindings

  I just got to know that Eclipse has a built in [Emacs key-binding scheme](http://help.eclipse.org/juno/index.jsp?topic=%2Forg.eclipse.platform.doc.user%2Fconcepts%2Faccessibility%2Fkeyboardshortcuts.htm), just set it like this:

    Windows->Preference->General->Keys->Scheme->Emacs
    
  has most useful key-bindings, like _C-a_, _C-e_, _C-x C-s_, etc. But Set Mark does not work with _C-Space_
  
## Emacs+ plugin for Eclipse

  Install [Emacs+](http://marketplace.eclipse.org/content/emacs) from Eclipse Marketplace, and see the tutorial on [MulgaSoft](http://www.mulgasoft.com/)
  Note that Emacs+ does not support Eclipse Juno(4.2), so if you want to use Emacs+, you better change back to Indigo(3.7) like me...

  It's awesome, now Set Mark and Select Text works in Eclipse.

## Menu bar integration with Ubuntu Unity

  This is amazing, just do as this [blog post](http://elvisd.blogspot.hk/2012/01/enable-eclipse-global-menu-integration.html) says, and you can move the eclipse menu bar to Ubuntu global menu area.

    $ sudo vim /usr/lib/gtk-2.0/2.10.0/menuproxies/libappmenu.so
    /Eclipse
    # Replace 'E' with 'X'
    Eclipse -> Xclipse
    # Save && Exit
    $ sudo ldconfig

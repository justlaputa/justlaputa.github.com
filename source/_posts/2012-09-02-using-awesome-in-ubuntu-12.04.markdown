---
layout: post
title: "Using Awesome in Ubuntu 12.04"
date: 2012-09-02
comments: true
categories: [linux, tutorial]
---

I have used [Ubuntu](http://www.ubuntu.com/) [12.04](http://releases.ubuntu.com/12.04/) for about 1.5 months, and now I'm very happy to use it, as I started Linux by [Fedora](http://fedoraproject.org/), and tried many other major [distributions](http://distrowatch.com/), my last distribution was [Gentoo](http://www.gentoo.org/), and I like it very much, still now. After I graduate from school and start my first job, I'm very glad that my employer does not care what OS I use, so I can continue using Linux, and this time I choose to try Ubuntu, which I have not used too much before.

<!--more-->

Till now, I have used it for nearly two month, and I'm happy now. It is simple, [Unity](http://unity.ubuntu.com/) is well designed, and it does not influence my work experience, I like it. Of cause I did some [tweak to Ubuntu](http://justlaputa.github.com/2012/07/install-ubuntu-en.html) to fit my habit, like disable some keybindings.

But recently, I started to miss one thing, which is the [awesome](http://awesome.naquadah.org/) window manager, I miss the **`Win + n`** workspace switch shortcut, and I miss it so much. Especially after I have buy a [HHKB](http://justlaputa.github.com/2012/08/buy-hhkb.html) lately, and I have to press 4 keys(**`Fn + Ctrl + Alt + Arrow`**) to switch workspace in Ubuntu Unity, though I have practiced my finger, but I miss awesome so much and plan to use it in ubuntu. And luckily it is easy.

Here is my experience about install and config awesome in **Ubuntu 12.04**. 

## install awesome

awesome is already in Ubuntu's package source, install it by apt-get:

    $ sudo apt-get install awesome
    
so it is easy, isn't it? Let's try it.

logout from ubuntu, and you will get to the [lightdm](http://wiki.freedesktop.org/wiki/Software/LightDM) login screen, and click the cycle icon besides your use name, a ha, we get a list of all available sessions to login, and of cause awesome is in the list. So choose **awesome** as the session, and type your password to login. Now you can see the awesome windows manager, yes, it's cool.

But wait, let do some config to get it more beautiful.

## config awesome

There are many config examples in [awesome wiki](http://awesome.naquadah.org/wiki/User_Configuration_Files), and currently I'm using the [vicious](http://awesome.naquadah.org/wiki/Vicious) configuration files, you can get it from it's [git repo](http://git.sysphere.org/).

    $ cd ~/src
    $ git clone http://git.sysphere.org/awesome-configs
    $ git clone http://git.sysphere.org/vicious
    $ cp -r awesome-configs ~/.config/awesome
    $ cp -r vicious ~/.config/awesome/

Now we have set the config files, lets reload awesome to see it. **Right click** no the desktop, choose *restart* to let awesome reload the configuration files. Da da, now we are really in awesome. All the configuration is write in `~/.config/awesome/rc.lua`, and we can see all the key shortcut in it.

## tweak Ubuntu

We are not done yet, though we have a beautiful WM to use now, but there are still something we need to do. 

### disable desktop background

Try to open [Nautilus](https://live.gnome.org/Nautilus) by pressing **`Alt + F2`**, you will see the Ubuntu desktop background show up, and the top task bar is gone, so we need to fix this first.

Install [Gnome-tweak-tool](https://live.gnome.org/GnomeTweakTool):

    $ sudo apt-get install gnome-tweak-tool

and open it, disable **`Desktop->Have file manager handle the desktop`**, now restart awesome, this time open **Nautilus** is ok.

### auto start gnome-session

We need to use some gnome service in awesome, like [Network Manager](http://projects.gnome.org/NetworkManager/) and [Gnome keyring](https://live.gnome.org/GnomeKeyring), so let's add it to awesome's init file:

    $ vim ~/.config/awesome/rc.lua
    # add to bottom
    exec("gnome-keyring-daemon --daemonize --login")
    exec("gnome-session --session=ubuntu")

How do I know this? Re-choose **Ubuntu** session when you login, and using [htop](http://htop.sourceforge.net/) to see process tree, you will see what's running behind a gnome session, choose the one you like and add it to `rc.lua`.

### fix the maximize problem

Some program(like gnome-terminal) can not be really maximized when pressing **`Win + m`**, add this to `rc.lua` will fix this:

    size_hints_honor = false

## Finally

I'm having fun now. I'll add more tips if I find something cool.

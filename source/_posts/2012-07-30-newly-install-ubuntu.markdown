---
layout: post
title: "Newly Install Ubuntu"
date: 2012-07-30
comments: true
categories: [linux]
---

# Replace my work environment to Ubuntu

Just some notes for my newly installed Ubuntu Linux configuration, use it if this helps.

<!--more-->

## install ubuntu and upgrade

I installed [Ubuntu 12.04](http://www.ubuntu.com/) using live usb, createed by [LinuxLive USB Creator](http://www.linuxliveusb.com/), after install:

    sudo apt-get update
    sudo apt-get upgrade

## System config
### add user to sudo
Don't know why, in Ubuntu 12.04 you should add the username at the last line, otherwise it won't work

    sudo visudo
    # add at bottom line
    username  ALL=(ALL)NOPASSWD:ALL
    
### disable ALT key shortcut
Sometimes it is annoying to see the HUD window when pressing ALT key, disable like this: 

    Settings->keyboard->shortcuts->key to show the HUD->(press backspace to disable)
  
### disable ALT key on gnome-terminal
I use zsh and emacs key-binding, so I use alt key a lot, but this is bind to show Menu item in gnome-terminal, disable it like this:

    Open gnome-terminal:
    Edit->Keyboard Shortcuts->Uncheck menu access keys

### change ubuntu tooltip color
By default, the tooltip window color is white-on-black, which is not so cheerful, especially when you are using Eclipse. Change the color to black-on-yellow like this:

    vim /usr/share/themes/Ambiance/gtk-2.0/gtkrc
    gtk-color-scheme = "base_color:#ffffff\nfg_color:#4c4c4c\ntooltip_fg_color:#000000\nselected_bg_color:#f07746\nselected_fg_color:#FFFFFF\ntext_color:#3C3C3C\nbg_color:#F2F1F0\ntooltip_bg_color:#ffffaf\nlink_color:#DD4814"
    
Note that `Ambiance` is the default gtk color theme, if you are using other theme, edit the corresponding `gtkrc` file. Log out or restart to see the change.

### auto hide launcher

    Settings->Appearance->Behavior->turn on auto hide

## Software

### common:

    sudo apt-get install vim emacs emacs-goodies-el zsh tmux
    ## if you use zsh
    chsh -s /bin/zsh

### git
 
    sudo apt-get install git gitk
    git config --global user.name my_name
    git config --global user.email my_name@some.mail.com
    git config --global color.ui auto

I host my home config files on [github](https://github.com/justlaputa), so add this to my home directory
   
    cd ~
    git init
    git remote add origin git@github.com:justlaputa/home-config.git
    git fetch origin
    git checkout -t origin/master

### maven
    sudo apt-get install maven

### chrome
Download deb file from google [official site](https://www.google.com/chrome/), or if you want to use unstable release, download from [Chrome Release Channel](http://www.chromium.org/getting-involved/dev-channel/), double click the deb file, it will be installed automatically.


### java

   download from oracle [jdk site](http://www.oracle.com/technetwork/java/javase/downloads/index.html), see this [tutorial](https://help.ubuntu.com/community/Java#Oracle_.28Sun.29_Java_6) to config java
   
    sudo sh jdk-6u33-linux-i586.bin
    sudo mv jdk1.6.0_33 /usr/java
    sudo ln -s jdk1.6.0_33 default
   
 config environment variable in your bash config file(or zsh profile)
   
    vim ~/.baserc
    JAVA_HOME=/usr/java/default
    export JAVA_HOME
 
 config java
   
    sudo update-alternatives --install "/usr/bin/java" "java" "/usr/java/default/bin/java" 1
    sudo update-alternatives --config java
    (select 1)
   
 do the same to **javac**, **javasw**, **libjavaplugin.so**, see the above link, then check java version:
 
     java -version
     javac -version

### eclipse

 download from [official site](http://eclipse.org/downloads/)
 
    mkdir ~/dev
    cd ~/dev
    tar xzf ~/Downloads/eclipse-jee-juno-linux-gtk.tar.gz
    
#### eclipse plugins

 * [jetty wtp](http://wiki.eclipse.org/Jetty_WTP_Plugin/Jetty_WTP_Install)

## Other software

### fcitx - input method

    sudo apt-get install fcitx

open **Input Method Swithcher**, and select fcitx, re-login or restart.

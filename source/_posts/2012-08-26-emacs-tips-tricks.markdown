---
layout: post
title: "Emacs Tips&Tricks"
date: 2012-08-26
comments: true
categories: [tips, emacs]
---

<!--more-->

  delete ^M with emacs

    M-x replace-string <RET> C-q C-m <RET> <RET>

  save backup files elsewhere

    (defvar user-temporary-file-directory
      (concat temporary-file-directory user-login-name "/"))
    (make-directory user-temporary-file-directory t)
    (setq backup-by-copying t)
    (setq backup-directory-alist
          `(("." . ,user-temporary-file-directory)
            (,tramp-file-name-regexp nil)))
    (setq auto-save-list-file-prefix
          (concat user-temporary-file-directory ".auto-saves-"))
    (setq auto-save-file-name-transforms
          `((".*" ,user-temporary-file-directory t)))

  open file as root
   
  using tramp, see tramp manual
  
    C-x C-f /su::/etc/passwd

  replace with a newline
  
    M-x replace-string ; C-q C-j

  kill all buffers
 
    M-x kill-some-buffers

  save all buffers
 
    C-x s

  insert comment
 
  select region to comment:

     M-;

  [disable red underline](http://www.cs.ucf.edu/~leavens/emacs/ecb/html-help/Setting-up-semantic.html)

    (global-semantic-show-unmatched-syntax-mode -1)

  Swap caps lock & ctrl
 
    $ vim ~/.Xmodmap
    remove Lock = Caps_Lock
    remove Control = Control_L
    keysym Control_L = Caps_Lock
    keysym Caps_Lock = Control_L
    add Lock = Caps_Lock
    add Control = Control_L

  insert date
  
    C-u M-! date
    M-1 M-! date

  show whitespace
 
    M-x whitespace-mode

  keybord macros
  
    C-x (     ->start macro
    key sequence
    C-x )     ->stop macro
    C-x e     ->execute macro
    C-u N C-x e -> N times

  replace tabs with spaces

    M-x untabify
  
  vice versa:

    M-x tabify

  [multi-line edit](http://www.delorie.com/gnu/docs/emacs/emacs_68.html)
  
  mark a region then:
  
    C-x r t

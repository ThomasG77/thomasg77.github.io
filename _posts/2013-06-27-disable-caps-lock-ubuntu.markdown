---
published: true
layout: post
title: "Disable the annoying Caps Locks"
date: 2013-06-27T14:40:34.000Z
tags: sysadmin keyboard linux
categories: linux

---

I never use Caps Locks and sometimes I activate the key.

A small "googling" help me find how to stop the key to work.

You can do it with:

    xmodmap -e "remove lock = Caps_Lock"

If you want to reactivate it with:

    xmodmap -e "add lock = Caps_Lock"

The problem with this solution is you have to redo the process for each session

So the better way to do it is to create a file in ``~/.Xmodmap`` and add

    remove Lock = Caps_Lock
    keycode 66 = Escape

After, to apply immediatly the rule, make a

    xmodmap ~/.Xmodmap

At each startup, the ``~/.Xmodmap`` is automatically executed and you never have to care again Caps Locks key

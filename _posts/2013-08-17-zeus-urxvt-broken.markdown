---
layout: post
title:  "Zeus urxvt broken on archlinux"
date:   2013-08-17 19:00:00
categories: zeus urxvt archlinux
---

When running `sudo pacman -Syu` today, I found that both zeus and urxvt
broke. I use urxvt as my terminal, and at first I was at a loss as to how to
troubleshoot, till my coworker pointed out I could just run `xterm`.

zeus breaks with "exit status 1" and then just hanging.

urxvt breaks with "urxvt: can't initialize pseudo-tty, aborting"

In both cases, when I downgraded `glibc` to 2.17-6 the problem was fixed. I
needed to also downgrade `binutils` to 2.23.2-2.

Also, to fix zeus, the json gem needed to be upgraded to 1.8.0.

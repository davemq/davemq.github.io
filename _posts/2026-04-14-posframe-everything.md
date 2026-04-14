---
title: "Posframe for everything"
date: <span class="timestamp-wrapper"><span class="timestamp">&lt;2026-04-14 Tue&gt;</span></span>
layout: post
categories: 
tags: 
- emacs 
- posframe
---

An Emacser recently posted about [popterm](https://github.com/CsBigDataHub/popterm.el), which can use posframe to
toggle a terminal visible and invisible in Emacs. I tried it out, and
ran into problems with it, so abandoned it for now.

However, this got me thinking about other things that can use
[posframe](https://github.com/tumashu/posframe), which pops up a frame at point. I've seen other Emacsers use
posframe when they show off their configurations in meetups. I thought
about what I use often that might benefit from a posframe.

-   magit
-   vertico
-   which-key
-   company
-   flymake

Which of these has something I can use to enable posframes?

Of course, there are plenty of other packages that have add-on
packages to enable posframes.


## Magit

magit doesn't have anything directly, but it makes heavy use of
[transient](https://github.com/magit/transient). And there's a package [transient-posframe](https://github.com/yanghaoxie/transient-posframe) that can enable
posframes for transients. When I use magit's transients, the transient
pops up as a frame in the middle of my Emacs frame.


## vertico

Install [vertico-posframe](https://github.com/tumashu/vertico-posframe) to use posframes with vertico.


## which-key

Yep, there's [which-key-posframe](https://github.com/emacsorphanage/which-key-posframe).


## company

See [company-posframe](https://github.com/tumashu/company-posframe).


## flymake

I needed a bit of web searching to find this. [flymake-popon](https://codeberg.org/akib/emacs-flymake-popon) can use a
posframe in the GUI and popon in a terminal.

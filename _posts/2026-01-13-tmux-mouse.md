---
title: "Tmux and the mouse"
date: <span class="timestamp-wrapper"><span class="timestamp">&lt;2026-01-13 Tue&gt;</span></span>
layout: post
categories: 
tags: 
- tmux 
- linux 
- unix
---

I use `tmux` for just a few things, particular when
connecting to a remote system. But otherwise, I don't use it for local
terminals, as I can just open up another terminal in Wayland.

I have a Kensington trackball with a big scroll wheel. I've gotten
very used to using that scroll wheel for scrolling in terminals,
Emacs, my web browser, etc. But one place it didn't work was in
`tmux`.

But today I finally went searching around for how to turn on mouse
support. Something I read prompted me to think "Oh yeah, how do I do
that?" It turns out `tmux` does support the mouse, but not by
default. So you have to enable the support in your tmux configuration
file. I added

{% highlight ini %}
set -g mouse on
{% endhighlight %}

to $HOME/.config/tmux/tmux.conf, restarted tmux, and boom, I have
mouse wheel scrolling like most other applications I use.

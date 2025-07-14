---
title: "Updating my home Emacs config"
date: 2025-07-14
layout: post
categories: 
tags: 
- emacs
---

I keep my Emacs config files for my home systems and my work systems
in a common Git repository, in different branches. I started doing
this several years ago, but paused updating the repository for a
while. I decided to

-   update the repository branches based on my current configurations
-   move my home Emacs configuration to a literate configuration

I moved my work Emacs configuration to a literate configuration
several months ago. Along with using `use-package`, it's
somewhat organized now.


## Move to literate configuration

So, the first thing I did was copy the contents of my home init.el to
a new file config.org, wrapped with

    #+begin_src emacs-lisp
    ...
    #_end_src

Then replace the contents of init.el with this code:

{% highlight emacs-lisp %}
;; Literate init file
(org-babel-load-file
 (expand-file-name "config.org" user-emacs-directory))
{% endhighlight %}

Now, restart Emacs.

Well, that's okay, but it isn't yet a literate configuration, as it
doesn't actually describe anything about the code, except for any
embedded comments.


## Remove use of Emacs profiles, ala "chemacs"

I also noticed something else: I was using Emacs profiles provided by
the "chemacs" package. I figured this out when I wanted to change from
using a hardcoded path for a separate custom.el file to using
`user-emacs-directory` instead. But that didn't work!
The Emacs profiles don't change `user-emacs-directory`
based on the profile.<sup><a id="fnr.1" class="footref" href="#fn.1" role="doc-backlink">1</a></sup> I've not actively used these Emacs
profiles in years, so reverting to just one configuration is fine.

So, how to do that? Remove the current ~/.emacs.d directory, and move
~/.emacs.default to ~/.emacs.d. Of course, that forces me to change
the hardcoded paths, so I might as well then use
`user-emacs-directory`.


## Separate custom.el

When you customize variables, Emacs updates a section that begins with

{% highlight emacs-lisp %}
;; custom-set-variables was added by Custom.
;; If you edit it by hand, you could mess it up, so be careful.
;; Your init file should contain only one such instance.
;; If there is more than one, they won't work right.
{% endhighlight %}

By default, this happens in init.el. But you can change this to a
separate file, which I chose to do in my home configuration. I'll
probably change my work configuration to do this also.

The code that does this is:

{% highlight emacs-lisp %}
(setq custom-file
      (expand-file-name "custom.el" user-emacs-directory)
      )
(load custom-file)
{% endhighlight %}

Note the use of `user-emacs-directory` here. If you
decide to adopt this separate custom.el file, go read the information
you can see by running `M-x describe-variable RET custom-file`.

# Footnotes

<sup><a id="fn.1" href="#fnr.1">1</a></sup> At least that's the case with the version of "chemacs" I've
been using.

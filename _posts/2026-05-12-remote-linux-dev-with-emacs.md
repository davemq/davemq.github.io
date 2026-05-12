---
title: "Remote Linux kernel development with Emacs"
date: <span class="timestamp-wrapper"><span class="timestamp">&lt;2026-05-12 Tue&gt;</span></span>
layout: post
categories: 
tags: 
- emacs 
- linux 
- kernel 
- eglot 
- magit 
- tramp
---

I work on developing features and fixing bugs in the Linux kernel in
areas specific to IBM Power. I use a number of Emacs' facilities to
get my work done.


# Magit

I use [Magit](https://magit.vc/) extensively with clones of various Git repositories from
<https://git.kernel.org/>. I keep learning new (to me) things about git
and Magit and using them. I've found it useful to learn how things work
with the command line before attempting to use them in Magit.


# Compilation

This work being with C source code, I make heavy use of `M-x
compile`. I've made my life easier by preloading a couple of items in
`compile-history`. I don't need this everywhere, just in
my Linux trees, so I put all of my Linux repositories under ~/linux,
and there I've created a .dir-locals.el for directory local variables:

{% highlight emacs-lisp %}
((nil . ((compile-history 
	  . 
	  ("nice make -k ARCH=powerpc CROSS_COMPILE=powerpc64le-linux-gnu- -j `nproc` all compile_commands.json gtags"
	   "nice make -k ARCH=powerpc CROSS_COMPILE=powerpc64le-linux-gnu- -j `nproc` tarzst-pkg"
	   )
	  )
	 )
      )
 )
{% endhighlight %}

I do my work on my x86\_64 laptop, and cross compile for the powerpc
architecture in 64-bit little endian mode. I take advantage of all of
the threads available on my laptop. I use `nice` to make it a bit
easier to interactive things while compiling. 

The first history item does the big work. I build the **all** target to
build the kernel and modules. I build compile\_commands.json to capture
the information needed by `clangd` to use with Eglot. I also
keep gtags databases up-to-date, though I should probably drop that
since I'm completely using Eglot with clangd at this point.

The second history item creates a .tar.zst file from the kernel and
modules, which I transfer to the remote Power partition.


# C mode and Eglot

I use c-ts-mode with Eglot. Eglot is integrated with xref.


# TRAMP

My test systems are remote, so I need to transfer the kernel and
modules over to it. I initially use TRAMP with the **scp** method to
transfer the compressed tar file. If I rebuild and create a new
package with the same name, I switch to the  **rsync** method to
transfer. This saves several seconds each time.

In both of these cases, I've set up keys so I do passwordless
authentication.


# Non-Emacs things on the remote system

Once I have the tar file on the remote system, there are some
non-Emacs things to do. I'm almost certain I can do them in Emacs, as
they're just command line things, but I'm just not used to that and
haven't pushed myself to try it.

-   untar the tarball in / (as root)
-   run `dracut` to create an init ramdisk
-   optionally remove out older kernels and modules
-   run `grub2-mkconfig` to add the new kernel to the GRUB menu
-   copy the new grub config file to the right place
-   reboot

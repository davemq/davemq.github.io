---
title: "Pipe to Emacs buffer"
date: 2025-11-12
layout: post
categories: 
tags: 
- emacs
---



# Introduction

An #emacs IRC user wants to pipe from a program into an Emacs buffer.
`emacsclient` doesn't read standard input<sup><a id="fnr.1" class="footref" href="#fn.1" role="doc-backlink">1</a></sup>. So, is there a
way to do this with `emacsclient` and not writing a new server
program? I think so.

We'll use a shell script called "pipe-to-emacs-buffer.sh". It will
take a single argument, the name of a buffer to receive the data. The
Emacs Lisp code will create this buffer and set it as the current
buffer.


# Shell script


## Start

{% highlight sh %}

# pipe-to-emacs-buffer.sh buffer-name

if [ $# -lt 1 ]
then
    echo usage: pipe-to-emacs-buffer.sh buffer-name
    exit 1
fi

buffername="$1"
{% endhighlight %}


## Base64 encode standard input

So why base64 encoding? If standard input has non-text characters,
e.g. a NUL, the string gets mangled. Encoding ensures the data is preserved.

{% highlight sh %}
input=$(base64)
{% endhighlight %}

This assumes that the program feeding the pipe is not a long-running
program that never quits. In the case of a long running program, this
code could be extended to use a loop of the shell's `read`
builtin and encoding to get all of the data over time, or make some
decision not to get all the data.


## Run emacsclient

{% highlight sh %}
emacsclient --eval "(set-buffer (get-buffer-create \"${buffername}\"))" --eval "(insert (base64-decode-string \"${input}\"))"
{% endhighlight %}

`get-buffer-create` will either use an existing buffer
or create one.

`insert` will insert the text before point.


# Let's test it!

Here's the output of `who` on my laptop:

    davemarq seat0        2025-11-11 09:44
    davemarq tty2         2025-11-11 09:44

So piping who into our program, along with the buffer name who.txt,
should result in a buffer named who.txt in our running Emacs.

    davemarq:~$ who | ./pipe-to-emacs-buffer.sh who.txt
    #<buffer who.txt>
    nil

Looking at our buffer list, I see who.txt:

    * who.txt                                78 Fundamental      

Going to who.txt, I see

    davemarq seat0        2025-11-11 09:44
    davemarq tty2         2025-11-11 09:44

Running our pipeline a second time gives me a who.txt buffer that
looks like this:

    davemarq seat0        2025-11-11 09:44
    davemarq tty2         2025-11-11 09:44
    davemarq seat0        2025-11-11 09:44
    davemarq tty2         2025-11-11 09:44

# Footnotes

<sup><a id="fn.1" href="#fnr.1">1</a></sup> In reading emacsclient.c, it *does* read standard input, but
prepends "-eval " to every line before sending to Emacs. But this is
not documented and didn't work as I expected when I tried it.

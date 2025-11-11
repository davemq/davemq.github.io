---
title: "Python2 on Fedora 42"
date: 2025-11-11
layout: post
categories: 
tags: 
- linux 
- python 
- fedora
---

I have a Python 2 application I use for geocaching, for manipulating
GPX and GGZ files. I've looked into porting it to Python 3, but it
seems to be more work than I'd like.

When I reinstalled my laptops with Fedora 41 some time back, I
discovered Python 2 wasn't available. Oh right, Fedora did announce
they were dropping Python 2 support. Of course, if you already had
Python 2 installed and just upgraded to Fedora 41, you would likely
have kept the packages.

So, what to do?

Fedora has this tool called `toolbox` that gives easy access
to Fedora and other distributions at various levels by using
containers. Using `toolbox`, I created a Fedora 40 toolbox:

{% highlight shell %}
toolbox create --distro fedora --release 40
{% endhighlight %}

Once I did that, I entered the toolbox with

{% highlight shell %}
toolbox enter fedora-toolbox-40
{% endhighlight %}

Within the toolbox, I installed Python 2.7:

{% highlight shell %}
sudo dnf install python2.7
{% endhighlight %}

After that I ran `python2` and `python2.7` and
checked the version:

    ⬢ [davemarq@toolbx ~]$ python2 --version
    Python 2.7.18
    ⬢ [davemarq@toolbx ~]$ python2.7 --version
    Python 2.7.18

I exited the toolbox shell with `exit` and used
`toolbox run` to run Python2.7 in the Fedora 40 toolbox:

    davemarq:~$ toolbox run -c fedora-toolbox-40 python2 --version
    Python 2.7.18

Finally, I turned that into a shell alias and ran it:

    davemarq:~$ alias python2='toolbox run -c fedora-toolbox-40 python2'
    davemarq:~$ python2 --version
    Python 2.7.18

---
title: "Splitting the subject in Gnus summary subject lines"
date: 2025-07-15
layout: post
categories: 
tags: 
- emacs 
- gnus 
- mail 
- email
---

I've spent a lot of time using Gnus in Emacs to read Linux development
mailing lists in the past few months. Many of the messages are 1 or
more patches, and the subject lines start with tags that look like

    [PATCH something...] foo bar bletch

It occurred to me that it might be nice to split the subject into the
part with the tag and the part that follows, and vertically align the
subject without the tag, like this:

    [PATCH something...]          foo bar bletch
    [PATCH other thing...]        blah blah blah

So, I set forth to figure out how to do this.

First, the summary line format is controlled by variable
`gnus-summary-line-format`. Gnus interprets the various
formatting characters in `gnus-summary-line-format` to print
the summary line. None of the formatting characters provided a direct
way to split the subject line. I could have just split the subject
line at a fixed point, but that would often have too much with the tag
part, or perhaps, with the case of no tag at all, some portion of the
subject.

I needed a way to call an Emacs Lisp function to split the subject
string for me. Fortunately, the Gnus documentation in section 4.1.1
Summary Buffer Lines documents 'u' as a user defined specifier.

    ‘u’
         User defined specifier.  The next character in the format string
         should be a letter.  Gnus will call the function
         ‘gnus-user-format-function-X’, where X is the letter following
         ‘%u’.  The function will be passed the current header as argument.
         The function should return a string, which will be inserted into
         the summary just like information from any other summary specifier.

I used 't' for tag and 's' for subject. So I had to use "%ut %us" to
get the pieces I wanted.

First, I needed to define `gnus-user-format-function-t`
and `gnus-user-format-function-s`, and they should take
the header as the argument. I assumed this meant the subject header,
but that turned out to be wrong. I think that documentation should
says "headers", as the functions were passed a vector of information.
More on this in a bit.

My first attempt to at `gnus-user-format-function-t`
assumed the header passed in was the subject header. This was myopic
on my part, because a user defined header needn't be limited to what
*I'm* interested in. At any rate, I first did something like this for
`gnus-user-format-function-t`:

{% highlight emacs-lisp %}
(defun gnus-user-format-function-t (subject)
  "Return tag from subject"
  (let
      ((cb (string-match "]" subject)))
    (if cb
	(substring subject 0 (1+ cb))
      nil)
    )
  )
{% endhighlight %}

Then I replaced "%s" in `gnus-summary-line-format` with
"%ut". On entering a group, I got an error. I set the "Enter debugger
on error" option and tried again, and noted that the complaint was
that subject wasn't a string. Hmm. On examination, I saw a vector on
information as the parameter, and the subject header was in
position 1. So try 2 was this:

{% highlight emacs-lisp %}
(defun gnus-user-format-function-t (headers)
  "Return tag from subject"
  (let
      ((subject (aref headers 1))
       (cb (string-match "]" subject)))
    (if cb
	(substring subject 0 (1+ cb))
      nil)
    )
  )
{% endhighlight %}

Okay, that's closer, but my next error was that
`subject` wasn't defined. Looking at the definition of
`let`, it says this:

    let is a special-form in ‘C source code’.
    
    (let VARLIST BODY...)
    
    Bind variables according to VARLIST then eval BODY.
    The value of the last form in BODY is returned.
    Each element of VARLIST is a symbol (which is bound to nil)
    or a list (SYMBOL VALUEFORM) (which binds SYMBOL to the value of VALUEFORM).
    All the VALUEFORMs are evalled before any symbols are bound.

Oh, so I can use subject in the body, but not in the VALUEFORM's. Got
it.

Next version was good, at first:

{% highlight emacs-lisp %}
(defun gnus-user-format-function-t (headers)
  "Return tag from subject"
  (let
      ((subject (aref headers 1))
       (cb (string-match "]" (aref headers 1))))
    (if cb
	(substring subject 0 (1+ cb))
      nil)
    )
  )
{% endhighlight %}

This worked great on subjects that had tags. But on subjects without a
tag, it returned `nil`, and the formatter barfed trying
to format `nil` as a 40 character string. Oops.

So, final version is

{% highlight emacs-lisp %}
(defun gnus-user-format-function-t (headers)
  "Return tag from subject"
  (let
      ((subject (aref headers 1))
       (cb (string-match "]" (aref headers 1))))
    (if cb
	(substring subject 0 (1+ cb))
      "")
    )
  )
{% endhighlight %}

`gnus-user-format-function-s` is similar, but gets the
untagged subject:

{% highlight emacs-lisp %}
(defun gnus-user-format-function-s (headers)
  "Return subject - tag"
  (let
      ((subject (aref headers 1))
       (cb (string-match "]" (aref headers 1))))
    (if cb
	(substring subject (+ cb 2))
      subject
      )
    )
  )
{% endhighlight %}

Finally, I updated `gnus-summary-line-format` in the
`use-package gnus` part of config.org:

{% highlight emacs-lisp %}
(gnus-summary-line-format "%U%R%4i %11&user-date; %I%(%[%4L: %-23,23f%]%) %-40,40ut %us\12")
{% endhighlight %}

This says use 40 characters for the tag, left-justified and filled on
the right with blanks, followed by a space, then the untagged subject.

I also defined `gnus-user-format-function-s` and
`gnus-user-format-function-t` in the :init section of
`use-package gnus`.

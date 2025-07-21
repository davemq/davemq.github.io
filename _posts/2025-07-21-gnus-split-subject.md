---
title: "A bug fix for splitting the subject in Gnus summary subject lines"
date: 2025-07-21
layout: post
categories: 
tags: 
- emacs 
- gnus 
- mail 
- email
---

In [Splitting the subject in Gnus summary subject lines](https://davemq.github.io/2025/07/15/gnus-split-subject.html) I showed some
code to split the summary line in Gnus. Today saw an error when trying
to enter a Gnus group, and diagnosed it to a subject that ended in
"]". The `gnus-user-format-function-s` code adds 2 to
that for use as the beginning of second part of the subject, but
that's beyond the end of the string! Here's an updated version that
will use the length of the string if its less than `(+ cb 2)`:

{% highlight emacs-lisp %}
(defun gnus-user-format-function-s (headers)
  "Return subject - tag"
  (let*
	((subject (aref headers 1))
	 (cb (string-match "]" subject)))
    (if cb
	  (substring subject (min (+ cb 2) (length subject)))
	subject
	)
    )
  )
{% endhighlight %}

Of course, this points to the fact that I've got no test for this
boundary conditions. No tests at all, other than using the code in
production!

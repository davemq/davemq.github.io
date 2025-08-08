---
title: "Update #+date automatically when saving Org document"
date: 2025-08-08
layout: post
categories: 
tags: 
- emacs 
- org 
- org-mode
---


# Table of Contents


I started an Org mode document today. I used `C-x C-e # default` to
add a default set of options, title, date, author, and email. While I
like `#+DATE`, I *don't* like that I forget to update it as I update
the rest of the file, likely at a later date. I said to myself "Oh,
there must be a way to do this automatically." 

I asked Google search, and the AI Overview pointed me to the Emacs
`time-stamp` package. Doing a bit of research in the Emacs info pages
showed me that the rest of Google's answer was actually pretty good.
One thing it assumes is that I want the same configuration everywhere
for this. No, I don't. I only want this in some files, and even only
in some Org files. "Section 20.3.6.2 Forcing Time Stamps for One File"
on the Emacs info shows a nice way to use file local variables to
accomplish what I want. Here's what I ended up with.

{% highlight org %}
# Local Variables:
# time-stamp-start: "^#\\+date: "
# time-stamp-end: "$"
# time-stamp-format: "<%Y-%02m-%02d %a>"
# eval: (add-hook 'before-save-hook 'time-stamp nil t)
# End:
{% endhighlight %}

I verified it works by changing my `#+date` to something else, and
when I saved the file, it changed it to my preferred format.

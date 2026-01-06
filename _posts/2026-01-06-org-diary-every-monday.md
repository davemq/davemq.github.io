---
title: "Creating weekly diary-style timestamps in Org Mode"
date: <span class="timestamp-wrapper"><span class="timestamp">&lt;2026-01-06 Tue&gt;</span></span>
layout: post
categories: 
tags: 
- emacs 
- org
---

With the beginning of the new year, I received meeting invitations for
a twice weekly meeting, on Mondays and Thursdays. I added this to my
Org agenda file, and used a diary-style expression. I *thought* I
could use `diary-float` for every Monday and another for
every Thursday, like this

{% highlight org %}
* Recurring meeting
<%%(diary-float t 1 0) 11:30-12:00>
<%%(diary-float t 4 0) 11:30-12:00>
{% endhighlight %}

But when I looked my agenda, I didn't see anything for Mondays or
Thursdays at 11:30-12:00. Hmm.

The help for `diary-float` only specifies how the third
argument, N, applies if it is positive or negative. It would be
nice if 0, or some other value like t, meant every week.

I eventually settled on

{% highlight org %}
* Recurring meeting
<%%(diary-cyclic 7 1 5 2026) 11:30-12:00>
<%%(diary-cyclic 7 1 8 2026) 11:30-12:00>
{% endhighlight %}

for the meeting. It works, but it wasn't obvious to me that this was
the way to go.

I could also have put 5 entries for Mondays and 5 for Thursdays, to
get all five possible occurences of each in a month. Seems kind of
weird, so I didn't.

And finally, I could just use a regular +1w repeater, but then I need
to have two separate TODOs in my agenda file, one for Mondays and one
for Thursdays.

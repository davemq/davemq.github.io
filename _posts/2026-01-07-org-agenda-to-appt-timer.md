---
title: "org-agenda-to-appt-timer"
date: <span class="timestamp-wrapper"><span class="timestamp">&lt;2026-01-07 Wed&gt;</span></span>
layout: post
categories: 
tags: 
---

Among many other things, Emacs' Org mode manages tasks and allows you
to easily see an agenda of tasks that are scheduled or approaching a
deadline. Emacs also has an appointments system that shows scheduled
appointments at various times ahead of time. Org provides
`org-agenda-to-appt` to create appointments from your
agenda items.

So running `org-agenda-to-appt` once during Emacs
startup is great, but it has limitations:

-   it only creates appointments from today's agenda items
-   it has no clue when you've added an appointment to one of your
    agenda files

Assuming you run your Emacs for longer than a day, or that you add
appointments, this is a problem!

My solution is to run `org-agenda-to-appt` with
`run-at-time` with a repeater of 3600 seconds, i.e. 1
hour, like this, at Emacs start:

{% highlight emacs-lisp %}
(run-at-time 0 3600 'org-agenda-to-appt)
{% endhighlight %}

Time 0 means "right now", and the repeater is 3600 seconds. After
adding a new task to an agenda file, it shows up as an appointment
within an hour. This may not work if you add an appointment that's due
in the next hour, as the timer that adds the apointment may run after
the appointment time.

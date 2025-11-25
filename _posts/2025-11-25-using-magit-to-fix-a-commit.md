---
title: "Using Magit to fix a commit"
date: <span class="timestamp-wrapper"><span class="timestamp">&lt;2025-11-25 Tue&gt;</span></span>
layout: post
categories: 
tags: 
- emacs 
- magit 
- git
---

I've been working on a patch series for the Linux kernel. I had
consolidated my changes into four patches, with a few more to go,
when it dawned on me that I should review these patches myself before
sending them to anyone else. Duh!

Reviewing the first patch, I discovered a few things I wanted to fix.
If this was the fourth patch, I could directly use a squash commit to
fix the problem. But a commit that's had three patches on top of it
was a bit much for me to handle directly. This was a perfect
opportunity to try Magit for this. Magit's manual claims you can
squash into any commit. Cool.

So, following the manual, I created a squash commit on top of my
fourth patch. Then I requested that Magit squash this into my first
patch commit. This started a series of actions that were

-   resolve conflicts
-   continue the rebase

I resolved the conflicts with Emacs' SMerge mode. As is often the
case, I find Magit's status in these cases not useful. I consulted
`git status` in a terminal window, and it told me I needed to
resolve conflicts. After resolving the conflicts, there was a
suggestion of using `git rebase --continue`. So I tried it.

After doing this conflict resolution and continuing the rebase three
times, the work was complete. I was delighted that it worked.

I don't think I would have liked to try this from the command line!

For the future, I'd like to know if there's a Magit command to
continue a rebase. It wasn't obvious to me at the time from the rebase
screen in Magit. But the manual says you can use `r r` or
`magit-rebase-continue`.

I also wonder if there's some way I can modify the Magit status window
to be more useful to me. That's something that's bothered me for a
while when doing some operations. Many people rave about Magit, me
included, but I find the status screen difficult to parse at times.

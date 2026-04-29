---
title: "Browsing URLs from Gnus Summary buffer"
date: <span class="timestamp-wrapper"><span class="timestamp">&lt;2026-04-29 Wed&gt;</span></span>
layout: post
categories: 
- emacs 
- gnus 
- url
tags: 
---

I've been reading RSS and Atom feeds in Gnus for a few weeks, having
moved over from elfeed. IIRC in elfeed it was pretty easy to visit the
URL that an entry summarized. I've been searching for a while to do
the same in Gnus, without much luck.

But today I finally hit `C-h b` while in the Gnus summary buffer, and
finally noticed `w` bound to
`gnus-summary-browse-url`. Aha! This function will "Scan
the current article body for links, and offer to browse them." For
many articles, this will get me the one and only link, to the
article. For some of the articles, this finds several URLs, so I just
have to try to pick the right one. For that, I note

> If only one link is found, browse that directly, otherwise use
> completion to select a link.  The first link marked in the
> article text with ‘gnus-collect-urls-primary-text’ is the
> default.

And what is `gnus-collect-urls-primary-text`? It's "The
button text for the default link in ‘gnus-summary-browse-url’" and its
value is "Link". Exactly what I'm usually seeking!

This is an example of Emacs being self-documenting.

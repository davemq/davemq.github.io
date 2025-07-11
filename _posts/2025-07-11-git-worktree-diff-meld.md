---
title: "Setting up a Git external diff driver in a Git worktree"
date: 2025-07-11 Fri
layout: post
categories: 
tags: 
- git 
- worktree 
- diff 
- meld
---

I'm reviewing some code that's a refactor, where some parts of a C
function are put into a new function, and the old function calls the
new function. I found the default text from `git diff` difficult
to read. A tool like `meld` can be helpful to see the changes.

I was running `meld` "by hand", by extracting two revisions of
the file from `git` and then running `meld` on them. I
recalled that `git` could be configured to run external diff
drivers. Looking into this, it seemed like I could get `git diff` to use `meld` on some files.

One wrinkle is that I'm using Git worktrees, which are copies of a Git
repository with some shared state. This is a way to work on the same
repository, but different branches, in different directory trees.

Setting up an external diff through the configuration files involves
creating an external diff tool section with the external command in
the git config, globally, locally (the repository), or at the worktree
level. Great, I want the worktree. Then, you set the diff attribute
for a file or files.

`git config` has a `--worktree` option to manipulate
configuration in just the current worktree. Useful!

Using `git config set --worktree` to set the external diff
configuration hit a snag, as the documentation says to create a
section like this:

{% highlight conf %}
[diff "jcdiff"]
        command = j-c-diff
{% endhighlight %}

I tried to run `git config set --worktree 'diff "meld"'.command /usr/bin/meld`, but `git` didn't like it. I eventually gave up
and put it in the config file by hand. But first, I had to figure out
where the config file lived! For a repository, it lives in
`.git/config`. But apparently that's not the case for a worktree.
`git` puts some data in the parent repository at
`.git/worktrees/worktree`. But which file? I couldn't find anything,
so I decided to create one with `git config set --worktree foo.bar bletch`. I didn't get a `.git/config` in the top of the
worktree. I found it in the parent repo at
`.git/worktrees/worktree/config.worktree`. Ah, so now I could add my
external diff driver for `meld` in that file.

Interestingly, running `git config list --worktree` after all
this gave me

    diff.meld.command=/home/davemarq/bin/git-meld

Oh, so I could have used "diff.meld" as the section. And it would
likely have worked with `git config set --worktree`. That would
likely be a better way to document this in the gitattributes(5) manual
page.

You may have noticed I'm running `/home/davemarq/bin/git-meld`
rather than `meld`. Why? `meld` wants two arguments, the
old file and new file. But `git diff` passes seven arguments:

    path old-file old-hex old-mode new-file new-hex new-mode

I just want old-file and new-file. `git-meld` is a simple shell
script that calls meld with these arguments. The code is very short:

{% highlight shell %}
/usr/bin/meld $2 $5
{% endhighlight %}

Now I have a external diff driver for meld. But I don't have a way to
tell `git` to use it. That's the final piece of the puzzle. You
update your git attributes to use the external diff driver for certain
file path patterns. So I added

to the `.gitattributes` file in the worktree's top directory. There was
already a `.gitattributes` file there, so I just added this line.

Now, `git diff` correctly runs `meld` if I diff foo.c,
e.g.

{% highlight shell %}
git diff rev1 rev2 foo.c
{% endhighlight %}

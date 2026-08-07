---
title: "Run the file in Emacs' dired"
date: <span class="timestamp-wrapper"><span class="timestamp">&lt;2026-08-07 Fri&gt;</span></span>
layout: post
categories: 
tags: 
- emacs 
- dired 
- bash
---


# Table of Contents


I started going through the
[The Rust Programming Language](https://doc.rust-lang.org/book/title-page.html) to learn rust. I'm using the *rustic*
package to edit files, run `cargo`, etc.

Having run `cargo build` in the sample `hello_cargo` project, I
wanted to run the executable file from within Dired. I realized
there's no obvious way to do this in Dired or Dired-X, and that's
probably a good thing. If it was easy I can imagine all sorts of havoc
with people accidentally, or intentionally, runnning files out of
Dired. Too easy.

But it occurred to me that `bash` has `command` to run
the next word in the command line as a command. I tried `! command` in
Dired, but since the directory isn't in the path, it told me

    /bin/bash: line 1: hello_cargo: command not found

Okay, update **PATH** before running `command`: `! PATH`.:$PATH
command=. This actually works, but decades of training give me a
queasy feeling about adding `.` to **PATH**. How about $PWD instead?
`! PATH=$PWD:$PATH command`. Yep, that works too.

But of course, with *rustic*, I can use `C-c C-p r` to run
`cargo run` to run the command. But trying to run the file on
the line in Dired was an interesting diversion!

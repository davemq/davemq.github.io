---
title: "An obscure Emacs packages: decide"
date: <span class="timestamp-wrapper"><span class="timestamp">&lt;2025-09-10 Wed&gt;</span></span>
layout: post
categories: 
tags: 
- emacs 
- carnival
---

This is a post for the [September Emacs Carnival](https://goritskov.com/posts/obscure_packages.html).

I discovered **decide** some time ago when I was looking for a way to
simulate rolling dice and choosing letters for a word game. The
**decide** package summary is "Rolling dice and other random things."

After loading **decide**, you can enable it as a minor mode by `M-x
decide-mode`. This creates several key bindings starting with `?`,
providing access to many of the **decide** functions. Here's a great
table from [decide's Github README](https://github.com/lifelike/decide-mode?tab=readme-ov-file#examples-quick-overview):

| **What to decide** | **Keys** | **Input** | **Example Output** |
|---|---|---|---|
| Yes or no | ? ? | | YES |
| … probably yes | ? + | | YES |
| … probably no | ? - | | NO |
| Roll 1d6 | ? 6 | | 3 |
| Roll 2d6 | ? D | | (5 1) = 6 |
| Roll 1d20 | ? 2 0 | | 20 |
| Roll dice (general) | ? d | 2d6+1 | (5 3) +1 = 9 |
| Roll four FATE dice | ? F | | (- 0 + +) = 1 |
| Roll 5d6 and count 6s | ? d | 5dB6 | (6 3 2 6 4) = 2 |
| Number from range | ? r | 1-10 | 5 |
| … low likely | ? r | 1<<10 | 4 |
| … low more likely | ? r | 1<<<<10 | 1 |
| … high likely | ? r | 1>>10 | 7 |
| Choose from list | ? c | apple,orange | orange |
| Random dir (of 4) | ? w 4 | | N |
| Random dir (of 8) | ? w 8 | | NW |
| Use random table | ? t | example-dragon | wise ice dragon |

The [README](https://github.com/lifelike/decide-mode) has lots of information about all of these options.

Above I mentioned word games. I play a Star Trek themed "hangman"
style word game on Mastodon. For each round, I'm presented with a poll
with 4 choices for the next letter to guess. The guess for that round
is the letter that gets the most votes in the poll. If I don't know
which letter to pick based on some idea of the phrase being guessed, I
can load **decide** and enter `? 4` to let **decide** roll a 4-sided die
for me.

When I'm running the same game, I can use **decide**'s table functions
to load a table of weighted values, in this case, the letters of the
alphabet. I can load a table with `M-x decide-table-load-file` and
enter `alphaweighted.txt`. Then I can use that table to help me pick
letters for setting up the poll by `? t alphaweighted`.

The README has a fun example of using a yasnippet snippet to insert a
randomly generated Victorian name from a table:

    `(decide-choose-from-table "names-victorian")`.

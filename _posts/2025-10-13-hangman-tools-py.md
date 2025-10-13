---
title: "Hangman tools in Python"
date: 2025-10-13 Mon
layout: post
categories: 
tags: 
- python 
- hangman 
- startrek 
- hangtrek
---

For several months I've been playing a "hangman" style game on
Mastodon, HangTrek, where words and phrases are from the various Star
Trek television shows and films. The game starts with a phrase like

    _ _ _ / _ _ _ _ _ _ _ / _ _ / _ _ _ / _ _ _ _ _ _ / _ _ _ _ / _ _ _ / _ _ _ _ _ _ _ _ _ _
    
    Wrong guesses: none

The underscore (\_) characters are unknown (to the players) letters,
and the forward slashes (/) are word separators. For each round a poll
with 4 choices is run for some amount of time, typically 24 hours. The
poll winner is the guess for that round. If the winner matches one of
the unknown letters, for the next round, that letter is filled in. The
the winner isn't one of the unknown letters, that's noted below the
puzzle in the following rounds, so it doesn't get guessed again.

For example, if 'E' is a valid guess, in the next round it will show
up like this:

    _ _ _ / _ _ E _ _ _ _ / _ _ / _ _ _ / _ _ _ _ _ _ / _ _ _ _ / _ _ _ / _ _ _ _ _ _ _ _ _ _
    
    Wrong guesses: none

In another example, if 'Q' was the poll winner but doesn't match any
letters, it's noted in the wrong guesses.

    _ _ _ / _ _ _ _ _ _ _ / _ _ / _ _ _ / _ _ _ _ _ _ / _ _ _ _ / _ _ _ / _ _ _ _ _ _ _ _ _ _
    
    Wrong guesses: Q

Some number of Star Trek television and film scripts are available for
download, so I took to searching them with regular expressions. For
example, for a 10 letter word where none of the letters are known,
I'd search for

    [[:alpha:]]\{10\}

in the scripts, using a tool like `grep`.

Over time, I realized I could write a program to create these regular
expressions, so I did. I have a long history of turning my
non-programming homework into programming homework, back to my college
days 😃

For a whole phrase, there are some things we want to do:

-   anchor words with '\\<' and '\\>', so we don't match words within
    larger words
-   allow for multiple spaces between words in a phrase, using
    '\\+'
-   shrink the original '[:alpha:]' character class as letters are
    guessed. For correct answers, we see them in the phrase. For
    incorrect letters, add a way to specify them, so they're also
    eliminated from the character class

So, I wrote a Python program `hangman-regexp.py` to accomplish
this. It takes the phrase as a command line argument, and you can
optionally specify wrong guesses with a `-x Q` argument.


## hangman-regexp.py

For the first phrase above, the program runs like this:

{% highlight shell %}
hangman-regexp.py '_ _ _ / _ _ _ _ _ _ _ / _ _ / _ _ _ / _ _ _ _ _ _ / _ _ _ _ / _ _ _ / _ _ _ _ _ _ _ _ _ _'
{% endhighlight %}

which provides this output:

    \<[abcdefghijklmnopqrstuvwxyz]\{3\}\>[[:space:]]\+\<[abcdefghijklmnopqrstuvwxyz]\{7\}\>[[:space:]]\+\<[abcdefghijklmnopqrstuvwxyz]\{2\}\>[[:space:]]\+\<[abcdefghijklmnopqrstuvwxyz]\{3\}\>[[:space:]]\+\<[abcdefghijklmnopqrstuvwxyz]\{6\}\>[[:space:]]\+\<[abcdefghijklmnopqrstuvwxyz]\{4\}\>[[:space:]]\+\<[abcdefghijklmnopqrstuvwxyz]\{3\}\>[[:space:]]\+\<[abcdefghijklmnopqrstuvwxyz]\{10\}\>

For the second example, where we've successfully guessed 'E', here's
the command and the output:

{% highlight shell %}
hangman-regexp.py '_ _ _ / _ _ E _ _ _ _ / _ _ / _ _ _ / _ _ _ _ _ _ / _ _ _ _ / _ _ _ / _ _ _ _ _ _ _ _ _ _'
{% endhighlight %}

    \<[abcdfghijklmnopqrstuvwxyz]\{3\}\>[[:space:]]\+\<[abcdfghijklmnopqrstuvwxyz]\{2\}e[abcdfghijklmnopqrstuvwxyz]\{4\}\>[[:space:]]\+\<[abcdfghijklmnopqrstuvwxyz]\{2\}\>[[:space:]]\+\<[abcdfghijklmnopqrstuvwxyz]\{3\}\>[[:space:]]\+\<[abcdfghijklmnopqrstuvwxyz]\{6\}\>[[:space:]]\+\<[abcdfghijklmnopqrstuvwxyz]\{4\}\>[[:space:]]\+\<[abcdfghijklmnopqrstuvwxyz]\{3\}\>[[:space:]]\+\<[abcdfghijklmnopqrstuvwxyz]\{10\}\>

You'll note that the character class no longer contains 'e', and in
the second word we have the actual 'e' embedded in the regular
expression.

For the third example, of 'Q' which is not in the phrase, the command
and output are like this:

{% highlight shell %}
hangman-regexp.py -x q '_ _ _ / _ _ _ _ _ _ _ / _ _ / _ _ _ / _ _ _ _ _ _ / _ _ _ _ / _ _ _ / _ _ _ _ _ _ _ _ _ _'
{% endhighlight %}

    \<[abcdefghijklmnoprstuvwxyz]\{3\}\>[[:space:]]\+\<[abcdefghijklmnoprstuvwxyz]\{7\}\>[[:space:]]\+\<[abcdefghijklmnoprstuvwxyz]\{2\}\>[[:space:]]\+\<[abcdefghijklmnoprstuvwxyz]\{3\}\>[[:space:]]\+\<[abcdefghijklmnoprstuvwxyz]\{6\}\>[[:space:]]\+\<[abcdefghijklmnoprstuvwxyz]\{4\}\>[[:space:]]\+\<[abcdefghijklmnoprstuvwxyz]\{3\}\>[[:space:]]\+\<[abcdefghijklmnoprstuvwxyz]\{10\}\>

\## Running a game: make-hangman-phrase.py

On the other end of this game is the game runner. They think up the
phrase and run the rounds. I thought it would be nice to have a tool
that, given a phrase and zero or more guesses, output the phrase
"hangman style", along with the "wrong guesses" line. This tool is
`make-hangman-phrase.py`.

Let's suppose you want to run a game with the phrase "There are four
lights". At the beginning of the game, you would run

{% highlight shell %}
make-hangman-phrase.py 'There are four lights'
{% endhighlight %}

Here's the output:

    Phrase:		_ _ _ _ _ / _ _ _ / _ _ _ _ / _ _ _ _ _ _
    Occurrences:	None
    Wrong guess:	None

This provides the phrase and wrong guesses that can be published on
Mastodon. The "Occurrences" line here shows how many times a guess
shows up in the phrase. We'll see this below.

Let's suppose for the first round the letter 'T' is guessed. In this
case, we run

{% highlight shell %}
make-hangman-phrase.py -g T 'There are four lights'
{% endhighlight %}

We add `-g T` to specify that 'T' was guessed. The output is
this:

    Phrase:		T _ _ _ _ / _ _ _ / _ _ _ _ / _ _ _ _ T _
    Occurrences:	{'t': 2}
    Wrong guess:	None

So now the game runner can say "T was the guess, and there are 2 T's
in the phrase" in their next post.

Let's say the next poll winner is 'V'. There's no 'V' in the phrase.
Let's see what happens.

{% highlight shell %}
make-hangman-phrase.py -g T -g V 'There are four lights'
{% endhighlight %}

Here's the output:

    Phrase:		T _ _ _ _ / _ _ _ / _ _ _ _ / _ _ _ _ T _
    Occurrences:	{'t': 2, 'v': 0}
    Wrong guesses:	V


## Code availability

The code for these tools is at <https://github.com/davemq/hangman-tools>.

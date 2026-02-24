---
title: "Calculating RAGBRAI training actual vs. planned mileage"
date: <span class="timestamp-wrapper"><span class="timestamp">&lt;2026-02-24 Tue&gt;</span></span>
layout: post
categories: 
tags: 
- ragbrai 
- bicycling 
- orgmode 
- emacs 
- org-mode 
- org
---

I'm training to ride [RAGBRAI](https://ragbrai.com/) LIII in Iowa in July 2026. RAGBRAI
provides a [training plan](https://ragbrai.com/2026-training-plan/) which, if followed, helps riders get ready
for the ride.

Being an Emacs Org geek, I grabbed the provided [Excel spreadsheet,](https://ragbrai.com/wp-content/uploads/2026/02/RAGBRAI_Training_Plan_2026.xlsx)
exported it as a CSV (comma separated values) file, then converted
that to an Org table.

The spreadsheet shows two lines per week, one with the planned
mileages, and another to record the actual miles ridden. There's a
total mileage in column 6, which is filled in for the planned rides,
and you fill in yourself for the actuals.

The first few lines of the spreadsheet look like

{% highlight org %}
|----------------------+-----------------------------+-------------------+------------------+------------------+------------|
|                      | 2026 RAGBRAI® Training Plan |                   |                  |                  |            |
|----------------------+-----------------------------+-------------------+------------------+------------------+------------|
| Week of:             | Weekday 1                   | Weekday 2         | Saturday         | Sunday           | Week Total |
|----------------------+-----------------------------+-------------------+------------------+------------------+------------|
| February 9           | 5 miles                     | 5 miles           | 10 miles         | -                | 20 miles   |
|----------------------+-----------------------------+-------------------+------------------+------------------+------------|
| Actual Ridden        |                             |                   |                  |                  |            |
|----------------------+-----------------------------+-------------------+------------------+------------------+------------|
| February 16          | 10 miles                    | 10 miles          | 10 miles         | -                | 30 miles   |
|----------------------+-----------------------------+-------------------+------------------+------------------+------------|
| Actual Ridden        |                             |                   |                  |                  |            |
|----------------------+-----------------------------+-------------------+------------------+------------------+------------|
{% endhighlight %}

I wanted to do two things:

-   make column 6 the sum of columns 2 through 5, which include two
    weekday and two weekend rides
-   create a column 7 that's a percentage of the actual vs. planned
    mileage

Org tables let you specify formulas for cells, columns, and rows.
Getting the sum of columns 2 through 5 for column 6 can be done a
couple of ways:

-   `$6=$2+$3+$4+$5`
-   `$6=vsum($2..$5)`

I used the former for quite a while, until I realized I could use the
latter. Either way, it works.

I had difficulty getting the formula for column 7 working how I wanted
it. Org tables can do formulas using Calc syntax, which is what I used
for column 6, or using Emacs Lisp forms as formulas. I used Calc
syntax to get this working. I wanted to calculate $7 as 100 times the
value of @0$6, the curent row's sum of miles ridden, divided by the
value of the previous row's sum of planned miles. It's easy enough to
just do something like `$7=100*@0$6/@-1$6`, but the values on
the planned rides rows were 100 timnes the planned miles for that week
divided by the actual miles for the previous week, which is usually
some number over 100. And not that interesting to me.

I also got rid of all instances of "miles" in the table. I ended up
with weird things like 70.3 miles / miles, and couldn't figure out how
to get Org to simplify this. Getting rid of "miles" in the spreadsheet
made things look much better.

I wanted to key on the "Actual Ridden" string in $1. If $1 is "Actual
Ridden", then use the calculation we've been discussing. Otherwise,
use 100 or show nothing. I had great difficulty making this work with
Calc syntax.

So, I tried Emacs Lisp forms as formulas. I ended up with

{% highlight org %}
$7='(if (string-equal $1 "Actual Ridden") (format "%.2f" (* 100 (/ (string-to-number @0$6) (string-to-number @-1$6)))) "")
{% endhighlight %}

This is a pretty straightforward translation of the percentage
formula, along with using `format` to only show a couple
of decimal places. In the case of other lines, just return an empty
string. The final formula works great.

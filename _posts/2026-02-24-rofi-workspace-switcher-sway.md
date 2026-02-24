---
title: "A Rofi workspace switcher for Sway"
date: <span class="timestamp-wrapper"><span class="timestamp">&lt;2026-02-24 Tue&gt;</span></span>
layout: post
categories: 
tags: 
- sway 
- wayland
---

I started out on a journey to try to get `sway` to create a new
workspace for me, with a "probably" unique name. How could I do that?

`sway`, like it's ancestor `i3`, has an IPC mechanism that
allows you to query and control `sway` from other programs. You
can play with this with `swaymsg`. `swaymsg "workspace foo"` will switch your current display to show workspace "foo",
creating "foo" if it doesn't exist. So one interesting way to achieve
my goal is

{% highlight shell %}
swaymsg "workspace $(xkcdpass -n 1)"
{% endhighlight %}

`xkcdpass -n 1` prints a single random word from
`xkcdpass`' word file.

One problem with random words is that we don't typically have nice key
bindings in `sway` to switch to these. You can use the mouse and
click on the workspace name on your bar, but if you'd prefer to only
use your keyboard, too bad.

So I thought to use `rofi` to show a list of workspaces and let
you select one. `rofi` has no builtin mode for this, but it does
have a way to add modes using scripts. Such a script has two modes
itself:

-   with no arguments, list the items in question. In this case, list
    the workspaces
-   with one argument, do something with that item. In our case, switch
    to that workspace

`rofi` allows you to type a new item, so in our case that would
be another way to switch to a new workspace.

So, how do you get the list of workspaces? Again, the `sway` IPC
mechanism. You can run `swaymsg -t get_workspaces` to get the
list of workspaces. By default this will pretty print the list of
workspaces. But if you specify `-r` or `--raw` or pipe
`swaymsg` to another program, it outputs JSON. Here's an
example:

{% highlight json %}
[
  {
    "id": 6,
    "type": "workspace",
    "orientation": "horizontal",
    "percent": null,
    "urgent": false,
    "marks": [],
    "layout": "splith",
    "border": "none",
    "current_border_width": 0,
    "rect": {
      "x": 0,
      "y": 30,
      "width": 2560,
      "height": 1410
    },
    "deco_rect": {
      "x": 0,
      "y": 0,
      "width": 0,
      "height": 0
    },
    "window_rect": {
      "x": 0,
      "y": 0,
      "width": 0,
      "height": 0
    },
    "geometry": {
      "x": 0,
      "y": 0,
      "width": 0,
      "height": 0
    },
    "name": "1",
    "window": null,
    "nodes": [],
    "floating_nodes": [],
    "focus": [
      17
    ],
    "fullscreen_mode": 1,
    "sticky": false,
    "floating": null,
    "scratchpad_state": null,
    "num": 1,
    "output": "DP-9",
    "representation": "H[H[emacs]]",
    "focused": true,
    "visible": true
  },
  {
    "id": 18,
    "type": "workspace",
    "orientation": "horizontal",
    "percent": null,
    "urgent": false,
    "marks": [],
    "layout": "splith",
    "border": "none",
    "current_border_width": 0,
    "rect": {
      "x": 2560,
      "y": 30,
      "width": 1920,
      "height": 1170
    },
    "deco_rect": {
      "x": 0,
      "y": 0,
      "width": 0,
      "height": 0
    },
    "window_rect": {
      "x": 0,
      "y": 0,
      "width": 0,
      "height": 0
    },
    "geometry": {
      "x": 0,
      "y": 0,
      "width": 0,
      "height": 0
    },
    "name": "2",
    "window": null,
    "nodes": [],
    "floating_nodes": [
      {
        "id": 9,
        "type": "floating_con",
        "orientation": "none",
        "percent": 0.61330662393162383,
        "urgent": false,
        "marks": [],
        "focused": false,
        "layout": "none",
        "border": "normal",
        "current_border_width": 2,
        "rect": {
          "x": 2878,
          "y": 97,
          "width": 1284,
          "height": 1046
        },
        "deco_rect": {
          "x": 318,
          "y": 40,
          "width": 1284,
          "height": 27
        },
        "window_rect": {
          "x": 2,
          "y": 0,
          "width": 1280,
          "height": 1044
        },
        "geometry": {
          "x": 0,
          "y": 0,
          "width": 696,
          "height": 486
        },
        "name": "foot",
        "window": null,
        "nodes": [],
        "floating_nodes": [],
        "focus": [],
        "fullscreen_mode": 0,
        "sticky": false,
        "floating": "user_on",
        "scratchpad_state": "fresh",
        "pid": 5998,
        "app_id": "foot",
        "foreign_toplevel_identifier": "0483dba85d6ad4c7b88b28653765ab03",
        "visible": true,
        "max_render_time": 0,
        "allow_tearing": false,
        "shell": "xdg_shell",
        "inhibit_idle": false,
        "sandbox_engine": null,
        "sandbox_app_id": null,
        "sandbox_instance_id": null,
        "idle_inhibitors": {
          "user": "none",
          "application": "none"
        }
      }
    ],
    "focus": [
      12,
      9
    ],
    "fullscreen_mode": 1,
    "sticky": false,
    "floating": null,
    "scratchpad_state": null,
    "num": 2,
    "output": "DP-8",
    "representation": "H[google-chrome]",
    "focused": false,
    "visible": true
  },
  {
    "id": 21,
    "type": "workspace",
    "orientation": "horizontal",
    "percent": null,
    "urgent": false,
    "marks": [],
    "layout": "splith",
    "border": "none",
    "current_border_width": 0,
    "rect": {
      "x": 2560,
      "y": 30,
      "width": 1920,
      "height": 1170
    },
    "deco_rect": {
      "x": 0,
      "y": 0,
      "width": 0,
      "height": 0
    },
    "window_rect": {
      "x": 0,
      "y": 0,
      "width": 0,
      "height": 0
    },
    "geometry": {
      "x": 0,
      "y": 0,
      "width": 0,
      "height": 0
    },
    "name": "4",
    "window": null,
    "nodes": [],
    "floating_nodes": [],
    "focus": [
      24,
      25
    ],
    "fullscreen_mode": 1,
    "sticky": false,
    "floating": null,
    "scratchpad_state": null,
    "num": 4,
    "output": "DP-8",
    "representation": "H[V[foot] V[foot]]",
    "focused": false,
    "visible": false
  }
]
{% endhighlight %}

Phew, that's a lot for 3 workspaces! The only part I care about in
this case is the "name" for each. We could do some weird stuff with
`grep` and `sed` to get the names, or use `jq` to
extract what we want.

{% highlight shell %}
swaymsg -t get_workspaces | jq '.[] | .name'
{% endhighlight %}

generates

    "1"
    "2"
    "4"

Perfect!

Putting this all together, here's a shell script
`sway_list_workspaces` that we can use with `rofi` to
switch workspaces without using the mouse.

{% highlight shell %}
#!/bin/sh

if [ x"$1" = x ]
then
    swaymsg -t get_workspaces | jq '.[] | .name'
else
    swaymsg "workspace $1" >/dev/null
fi
{% endhighlight %}

Here's the `rofi` command line to use:

{% highlight shell %}
rofi -modes 'Workspaces:/home/davemarq/bin/sway_list_workspaces' -show Workspaces
{% endhighlight %}

I bind it to "$mod+Shift+w" in my sway config file:

    bindsym $mod+Shift+w exec "rofi -modes 'Workspaces:/home/davemarq/bin/sway_list_workspaces' -show Workspaces"

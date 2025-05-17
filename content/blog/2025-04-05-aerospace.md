+++
title = "9 Things I Love About Aerospace"
date = "2025-04-05"
description = "I love Aerospace"
tags = ["macOS", "tilingWM"]
+++

> It is about the [Aerospace](https://nikitabobko.github.io/AeroSpace/guide) tiling WM manager on macOS, not the field of engineering!

It was only a couple years ago that my life lacked any sort of window management in my operating system.
When a window spawned, I adjusted my pupils to the window instead of the other way around.
If the window was too small, I dragged the unholy device called a mouse with my fat right hand until the cursor perfectly lined up with the corner of the window.
To switch to a different window, I frustratedly spammed the good old `cmd tab` until I found what I was looking for.

It was when I started using Linux and i3 tiling window manager that changed my life.
I started utilizing workspaces to group windows and let automatic tiling rules manage my windows *for* me.
I craved better window management when I eventually came back to macOS.
I tried many open-source tiling window managers/management tools: [Rectangle](https://rectangleapp.com/), [Amethyst](https://github.com/ianyh/Amethyst), and [Yabai](https://github.com/koekeishiya/yabai).

I liked that Yabai was fully configurable with `~/.config/yabai/yabairc` and how it integrated with other open-source tools like [Sketchybar](https://github.com/FelixKratz/SketchyBar).
It was not without a problem; I wished there was a way to move windows between Mission Control Desktops without disabling SIP, something like the stacked/tabbed layout in i3 instead of having to maximize individual windows, and a more intuitive approach to external monitor management.

Thankfully [Aerospace](https://nikitabobko.github.io/AeroSpace/guide) solved all these issues for me.
Aerospace is an i3-inspired tiling WM, and I dare say it is the best window management tool available in macOS.
Here are 8 things I love about Aerospace.

![aerospace-demo.gif](/images/2025-04-05-aerospace/aerospace-demo.gif)

## 0. Minimal GUI Components

Aerospace installs as a Homebrew cask with `.app` package and provides a menu bar item for the current workspaces.
All configurations are done in a single TOML file (`~/.aerospace.toml`), which you can manage in your Dotfiles repository.
I like the fact that the GUI component *exists* to serve as a visual indicator, but 

## 1. Workspace Implementation

Other tiling WM implements the tiling feature on the top of the macOS Mission Control Desktops, which is slow to respond and often clashes with the tiling mechanism if settings are not right.
The default animation is also painfully slow, and you need to disable System Integrity Protection to be able to move a window to another desktop using a keybinding.
Aerospace completely bypasses the Mission Control Desktops and implements its own workspace, controlled with keybindings (`alt - 1-9,a-z` by default).
All you need to do is removing all the Mission Control Desktops and letting Aerospace do its job, and oh boy, it is very responsive and works like charm.
You completely lose out on the default Mission Control Desktops though, and when you try the preview with a three-finger swipe, the windows render very small, making previews useless.
I think it is a fair trade-off, but I sometimes miss the preview of the Mission Control -- more on this in the last section.

## 2. Default Configuration

[The default configuration](https://nikitabobko.github.io/AeroSpace/guide.html#default-config) provided almost every keybinding I wanted from the tiling window manager.
All the keybindings you need to know are: `alt-hjkl` to navigate windows, `alt-shift-hjkl` to move windows, `alt-+/-` to resize windows, `alt-1-9,a-z` to switch workspaces, `alt-shift-tab` to send the workspace to another monitor, and `alt-,/` to switch layouts within accordion and tiling mode.

I added `alt - shift - spc` to toggle the floating/tiling on the current window, `alt - enter` to launch Wezterm, and disabled unused alphabet workspaces and some functions in the `mode service`.
I also adjusted the paddings/gaps, added window rules, and made no other changes to the default config.
It really took a little less than an hour to set up and get used to the keybindings, the fastest I could think of in any text-file-configured tiling WM.

## 3. Modes

Like i3, you can have a keybinding mode.
You can put relevant actions (e.g., resizing, launching applications) or functions that are not often used in a mode:

For example, the default configuration provides `mode service`

```toml
[mode.main.binding]
    # ... Other eybindings ...
    alt-shift-semicolon = 'mode service'

[mode.service.binding]
    esc = ['reload-config', 'mode main']
    r = ['flatten-workspace-tree', 'mode main'] # reset layout
    f = ['layout floating tiling', 'mode main'] # Toggle between floating and tiling layout
    backspace = ['close-all-windows-but-current', 'mode main']

    alt-shift-h = ['join-with left', 'mode main']
    alt-shift-j = ['join-with down', 'mode main']
    alt-shift-k = ['join-with up', 'mode main']
    alt-shift-l = ['join-with right', 'mode main']

    down = 'volume down'
    up = 'volume up'
    shift-down = ['volume set 0', 'mode main']
```

`alt-shift-;` takes you to the service mode (indicated by `[S]` in the menu bar icon), and `ESC` in the service mode reloads the config, `r` resets the layout, etc.

## 4. Smart Resizing

Aerospace supports "smart resizing," which recognizes if the current layout is horizontal or vertical and changes either width or height.

```toml
[mode.main.binding]
    alt-minus = 'resize smart -50'
    alt-equal = 'resize smart +50'
```

## 5. Accordion View

Instead of having two windows side-by-side, sometimes you need them to be full-screen while still being accessible (e.g., working with different terminal windows with in the same project).
I loved the stacked/tabbed view of i3 because it allowed me to quickly switch between windows using `super hjkl` while viewing them full screen.
Aerospace brings this feature with the accordion view, where each window is stacked together, with `accordion-padding` acting as a visual indicator that other windows are there.

## 5. Floating Window Recognition

Many other tiling WM completely ignore the window once it becomes floating, not Aerospace.
You can still use `alt-hjkl` keybindings to navigate to it based on the relative position of the window.

## 6. Multi-monitor Support

Since Aerospace took an inspiration from i3, it is no surprise that Aerospace implements an elegant solution for multiple monitors.

- Suppose display 1 has active workspaces indexed 1 and 2, currently displaying WS 1
- you connected the display 2
- DP2 gets the WS 3
    - This will be indicated by `*3 | 1` in the menubar, saying, "DP2 is displaying WS 3 and is in focus, DP1 is displaying WS 1"
- If you input `alt-3` to move to the WS 3, the focus will be moved to the DP2
- If you want to move WS 2 to DP2, focus WS 2 using `alt-2` and use `alt-shift-Tab` to move the current WS to another display
- Create a new workspace using `alt-4` (or other numbers), move between monitors using `alt-shift-tab`

## 7. Ignoring Windows without the Fullscreen Button

Another touch that I really appreciate is that Aerospace [puts the windows without the fullscreen button to float](https://nikitabobko.github.io/AeroSpace/guide.html#dialog-heuristics).
This is a great quality-of-life improvement that prevents me from having a million different rules in my config file.

## 8. Shell Command Queries

Aerospace provides an API to access and control the windows using commands, and you can use this to automate window management.
One example is from [this YouTube video by DevOps Toolbox](https://www.youtube.com/watch?v=5nwnJjr5eOo), using [fzf](https://github.com/junegunn/fzf) to list and pick windows.

```sh
aerospace list-windows --all | fzf --bind 'enter:execute($SHELL -c "aerospace focus --window-id {1}")+abort'
```

## Minor Complains & Feature Requests

- `aerospace list-tree` that displays the tree view of all workspaces and windows that belong to them: the only feature I miss from the native macOS Mission Control is the preview of desktops and windows with the three-finger swipe gesture because I could easily get a visual idea of how my workspaces are set up. If not GUI, I would love to have this feature in the command line
- sticky windows: It seems like they are [working on it](https://github.com/nikitabobko/AeroSpace/issues/2)!

Other than these two, it does everything I need a window manager to do.

## Closing

macOS window management finally became usable with Aerospace!
I would go as far as to say that features like smart resizing and floating window management are better than what many other Linux tiling window mangers have to offer.
You should try Aerospace, you really should.


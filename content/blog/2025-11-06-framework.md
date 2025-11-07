+++
date = '2025-11-06T22:11:03-05:00'
draft = false
title = 'I got Framework 13! (2 months ago)'
tags = ["laptop", "tilingWM", "KDE", "Gnome", "Linux"]
+++

{{< figure src="/images/2025-11-06-framework/framework-oliver-exiftool-stripped.jpg" position="center" caption="Mr. Oliver showing his support for free and open-source software and consumer friendly, reparable hardware"  >}}

I have been wanting Framework since 2021.
And 2 months ago, 2 weeks before my birthday, I ordered a Framework Laptop 13 to celebrate my birthday and return to school.
Despite the estimated shipping time saying "Ships Within 2-3 Weeks," it was shipped in 2 days and arrived at my doorstep in less than a week.

{{< figure src="/images/2025-11-06-framework/framework-box-exiftool-stripped.jpg" position="center" caption="How it arrived"  >}}

Ever since, I have been using it daily as my primary laptop.
Here is a short write-up about how it is doing.

## Specs & Peripherals

{{< figure src="/images/2025-11-06-framework/framework-internal-exiftool-stripped.jpg" position="center" caption=""  >}}

- AMD Ryzen ~AI~ 5 340
- 2.8K 120Hz Display
- Translucent Purple (!) Bezel
- 2nd gen US English Keyboard
- 2 USB-C Expansion Cards (translucent purple and translucent pink)
- USB-A Expansion Card
- HDMI Expansion Card
- Brought my own RAM: Crucial DDR5 5600MHz 16GB RAM x2
- Brought my own SSD: Samsung 990 Evo Plus 1TB

Accessories:

- Inateck laptop bag for 13.3 inch laptop: the best laptop sleeve I found that (almost) perfectly fits this laptop
- Dbrand Holo White skin: So overpriced, but so mesmerizing

{{< figure src="/images/2025-11-06-framework/framework-dbrand-exiftool-stripped.jpg" position="center" caption="Look at her!"  >}}

## How is the Hardware?

> The point of comparison will be my M1 Macbook Air with 86% battery health

- Build quality: the machined aluminum feels very premium, no sharp edges, even the plastic parts (key caps, bezel, plastic feet in the bottom) feel premium; I am still impressed
    - Hinges are heavy to the point where it is very hard to open with one hand
    - The display wobbles a bit when I put the laptop down on a table, whereas the Macbook feels rock solid
    - Almost the same weight as the Macbook, but thicker (slightly thinner than my roommate's 14-inch M4 Macbook Pro)
- Battery life: with KDE, it is comparable to the M1 Macbook Air with 86% battery life -- about 30% left after a full day of light to medium use
- Display: Not as vibrant, but sharp, smooth, and it's matte! Rounded corners look a little odd, but they don't bother me. The 3:2 ratio makes it feel like a 14-inch laptop rather than a 13-inch.
- Performance: amazing, even with `tuned-ppd` set to power save mode, it feels much snappier than the Macbook
    - CPU maintains 30-45 degrees Celsius in my usage. The fan does not kick in very often, though when it does, it can get quite loud
- Speaker: It's not a Macbook, but it's not terrible either
- Microphone & webcam: awful. Also, in Fedora 42 and 43, Pipewire crashes if I try to set both speaker and microphone to internal ones; I have to have an external speaker or headphones plugged in, only then can I select the internal microphone. Not that I would ever want to use the internal one, though (and yes, I made sure that the kill switch was off)

## I Use ..., btw

Distro? Fedora. I am familiar with it, and it is officially supported by Framework.

My goal was to run Sway with [my config](https://github.com/theopn/haunted-tiles), but I chose to go with a DE for multiple reasons.
To name a few:

- system tools: battery management (`tuned-ppd` in Fedora), display management, printer & network management tools, idle inhibitor
- system daemons: Flatpak fractional scaling support, permission management through XDG desktop portal, Polkit
- default applications: for example, I do not care what calendar app I use, but I need one
- other quality of life features: cloud service integration, "nightlight" feature, clipboard history, screenshot tools

Sure, I can install and configure all these tools and services on my own with Sway (and that is what my config repository tries to do), but I reckon that it would be a never-ending project.
As much as I love the simplicity of pure WM, the last thing I want is having to debug why Flatpak Zoom won't let me share my screen in the middle of a meeting (I had to restart XDG Desktop Portal with `DISPLAY` variable set, adjust the permissions in Flatseal, ... you get the point).

{{< figure src="/images/2025-11-06-framework/framework-gnome-exiftool-stripped.jpg" position="center" caption="Gnome + PaperWM"  >}}

I *cannot* function without a [tiling window management](./2025-04-05-aerospace.md), so I explored some options.
I started with Gnome and [PaperWM](https://github.com/paperwm/PaperWM), but I got frustrated with them for a couple of reasons:

- Gnome was buggy. The app drawer often randomly decided not to show any apps, the animation would lag, the shell crashed more than one time when I connected an external display in my research advisor's office (though it might be a PaperWM issue), and not to mention all the extensions I installed, crashing every now and then
    - especially [Search Light](https://extensions.gnome.org/extension/5489/search-light/) extension crashed very often, which was a bummer since this was crucial for my workflow, coming from macOS. In KDE, KRunner is the perfect replacement for macOS Spotlight
- I knew Gnome was not customizable, but I did not realize it would be this bad. No option to disable Super key, no easy theming options, no easy way to disable animation, not even a media playback indicator in the status bar without installing an extension
- Scrollable WM is a cool concept, but not for me
    - The default keybindings are the farthest thing from the Vim keybindings
    - All the slow animations and mis-pressed super key felt like Gnome was actively pushing me away from using an extension to manage windows
    - I rarely have more than 4 windows in one workspace
        - and when I have 2-3 windows in a workspace, I constantly switch between: having two split 50/50, having two split 60/40, or having one window maximized
        - I found myself constantly manually adjusting the size in the scrollable model
        - Conclusion: fitting more windows in a workspace isn't really the problem I need to solve; more efficiently managing the 2-3 windows I have in a workspace is

### And here comes KDE + [Krohnkite by anametologin](https://github.com/anametologin/krohnkite)

{{< figure src="/images/2025-11-06-framework/framework-kde-exiftool-stripped.jpg" position="center" caption="KDE + Krohnkite" >}}

- KDE is *very* customizable, but you don't have to customize it if you don't want to
    - My setup is kept simple: moving the panel to the top, adding a couple of widgets (virtual desktop, current window name, media playback, and system monitors), applying the Nordic theme, and changing a few keymaps in Krohnkite
    - I was able to document all this in ~15 lines of text instruction and one keymap file
- I disabled the animation, and I am seeing a noticeable improvement in performance and battery life
- You define keymaps for Krohnkite within the native KDE settings, no need to worry about duplicate keymaps
- Krohnkite supports Vim keybinding out of the box and multiple dynamic layouts: my favorites are B-Tree (for a traditional tiling experience), three-columns (for having two windows in a 60:40 ratio), and Stair layout (for "maximizing" one window with a visual indicator that there are other windows in the back)
- KRunner is amazing, and I love that it supports different modules, like macOS Alfred or Raycast (I use calculator and spell checker all the time). Plus it supports Vim keybindings!
- Overall, default apps feel more powerful and polished than Gnome counterparts (I love KDE Connect), though I cannot say that I like the naming s*k*heme

## What About My Old Laptops?

{{< figure src="/images/2025-11-06-framework/lana-del-rice-exiftooled.jpg" position="center" caption="Lana Del Rice?"  >}}

My Macbook Air has become a desktop, mainly used to play YouTube videos in the background or to use the MS Office suite.
With 256GB of SSD almost maxed out, I am considering replacing it with an M4 Mac Mini or other mini PC.

I will likely turn my ThinkPad X270 into a home server. Stay tuned.

## Conclusion

I love my Framework 13!


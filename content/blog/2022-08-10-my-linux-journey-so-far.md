+++
draft = false
title = "My Linux Journey So Far"
date = "2022-08-10T23:48:34-04:00"

author = "Theo"
description = "Long, boring writing on how nerdy I have become in the past 8 months. You HAVE to click it because there is a cute picture of Oliver"
tags = ["i3", "Sway", "AwesomeWM", "Vim", "Neovim", "Linux"]

cover = ""
readingTime = true
showFullContent = false

toc = false
tocTItle = "Table of Contents"
+++

{{< figure src="/images//2022-08-10-my-linux-journey-so-far/ollie-thinkcat.jpg" alt="Oliver looking at my ThinkPad" position="center" style="border-radius: 8px;" caption="I told you it's a cute picture" captionPosition="left" captionStyle="color: black;" >}}

This spring semester, I took a C Programming class with, hmm, "Dr. T." Before meeting him, I have never seen a person in real life using Linux. In the first lecture, he emphasized the importance of command line editors when coding in C and announced that TA won't help the student if they are not using Vim or other command line text editors during office hours. Many students seemed to hate this, with many of them discovering the "hack" of using VS Code to connect to the SSH server and doing the homework in a GUI environment. I barely knew what Vim was at the time, let alone other command line tools and debuggers. Nevertheless, Dr. T's transparent terminal was enough to persuade me (It's a known fact that your programming credibility directly correlates to the number of monitors you have and how "riced" out your window manager is), and my life in the terminal has begun.

And my oh my, Vim instantly became my favorite editor. If it wasn't the intuitive keyboard shortcuts that boosted my productivity, it was the fact that I can configure it to my taste and import/export those settings in one text file. I spent lots of time modifying my `.vimrc` and had a lot of fun discovering hidden features and making custom auto commands and functions. This naturally made me discover more about command line tools, and in just a matter of 2 - 3 months, I was more comfortable editing text files on `Neovim`, multitasking with `tmux`, and installing applications through `homebrew`, than doing the same actions using mouse.

Up until this point, I've been a Mac user for years, and the 2020 M1 Macbook Air that I had for about 4 months at the time was fantastic. But I wanted more. I naturally was exposed to a lot of Linux users when I tried to find resources for Vim, and in no time, I was browsing r/Linux with the "Canonical bad, Arch good" mindset despite having never used Linux outside of the school CS labs. Nevertheless, after much research and consideration, I purchased a used Lenovo ThinkPad T460s for $130 in May to try using Linux.

While the laptop was in transit, I thought about the Linux distribution for the laptop. Should I go with Ubuntu as many people recommend? But cool Arch users said that Ubuntu was bad, because, because... (on a serious note, now that I learned more about Linux, I do think that Ubuntu is no longer a gold standard of desktop distribution and shouldn't be recommended to beginners). Or POP!\_OS that my friend recommended? Or Linux Mint? Then I remembered that one time I saw the Fedora logo on Dr. T's laptop screen, and without giving it too much thought, I made an installation USB for Fedora Workstation.

The laptop came with Windows 10, which was painfully slow (granted, the laptop had only 4GB of RAM at the time), so I erased the disk and installed Fedora without any hesitation (which performed much better than Windows, even with GNOME). The first impression was good, if not underwhelming. Everything except the fingerprint sensor worked without any configuration. GNOME 42 looked attractive and was intuitive to use, but I quickly got tired of installing GNOME extensions. Where is that "Linux customizability" that I was promised? I asked people on Purdue CS Discord for a DE recommendation, and experienced Linux users started to jokingly recommend pure window managers because "DEs are bloated." And I fell for it. Before I even realized it, I was installing the i3 window manager.

The i3 window manager was a cultural shock for me. My brightness and volume keys didn't work, it didn't show the battery information, and there was no graphical interface to launch an application, how am I supposed to use this? Yet, not wanting to look "uncool," I stuck with i3 and try to configure things myself. What made it harder (and more rewarding in the end) was that I didn't pay attention to other people's dotfiles and was making my i3 configuration from the scratch. Thankfully, my experience with command line text editors, relatively intuitive i3 and Polybar configuration structure, and the instant productivity boost that i3 bought made me stuck to the i3, and I made a complete i3 + Polybar (and Sway + Waybar) environment that I still use to this date.

Working with i3 changed my perspective on how I view operating systems and graphical interfaces. Most importantly, I realized that virtually every action done through GUI is just wrappers for command lines. Who knew that locking the computer before going to the suspend mode, recognizing the external display and rendering contents to it, and changing the volume of the speaker are all command line actions? I wouldn't have known if I weren't making widgets and custom keybindings for those actions.

It exposed me to more efficient programs too. Because `Mod + Enter` is bound to opening a terminal, I found myself wanting to utilize more out of the terminal. `mutt` is a simple yet powerful email client that is capable of 90% of what I need from an email client. I use `w3m` every day for a simple web search because no ads, fancy animations, or JavaScript contents are there to distract me. Need to see images or JS contents? Launching the current URL on Firefox is one keystroke away. I invested some time learning `Emacs` and still prefer `vim` and `Neovim` for text editing, but it's staying with me because of the `Org-mode`. It turned out to be the perfect to-do list and project management application, and I no longer pay for the Todoist subscription.

As the fall semester is around the corner, I am wrapping up my Awesome WM configuration and starting to prepare the laptop for actual daily school use. You heard me right, 7 years old, $130 dollar ThinkPad running Linux over M1 MacBook Air. I have used the laptop daily throughout the summer while working as a teaching assistant and course content developer, and I am confident that it can handle all of my work. Most importantly, using Linux and tiling window manager have become integral parts of my workflow that I really can't see myself going back to other operating systems. I have never had this much fun and joy when using a computer, so thanks to Linux, tiling window managers, and all the command line tools (and of course, Dr. T).


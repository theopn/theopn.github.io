+++
draft = false
title = "Theovim, in Comparison to My Old Neovim Configuration"
date = "2023-07-18T23:31:15+09:00"

author = "Theo"
description = "One year update on my Neovim journey"
tags = ["Vim", "Neovim"]

cover = ""
readingTime = true
showFullContent = false

toc = false
tocTItle = "Table of Contents"
+++

This article is still in process! Only public because Hugo won't let me set it to draft without deleting the references...

## Wait, what happened?

{{< figure src="/img/writing/2023-07-18-old-new-theovim.jpg" alt="My old Neovim config in comparison to Theovim" position="center" style="border-radius: 8px;" caption="My old Neovim config in comparison to Theovim" captionPosition="left" captionStyle="color: black;" >}}

I started using Neovim as my primary text editor last summer. By August, I developed a configuration with LSP support, and I have been using Neovim extensively for my development in C, C++, Python, shell scripting, basically everything but Java (Why not? Because: 1. I worked as an TA for the introductory Java class for 3 semesters, and IntelliJ is what most students use. I wanted to be on the students' level and use the tool they use. 2. IntelliJ is simply too good for Java).

This (2023) spring semester, me and my CS major friends took CS25200: Systems Programming. This class is the last of the required core CS classes list, and it had a reputation as a time sink with big low-level project like implementing memory allocator and free functions, making shell interpreter, and a web server. All the works were //TODO

## Appearance

- Colorscheme: I have moved on from [`OneDark.nvim`](https://github.com/navarasu/onedark.nvim) to [`tokyonight.nvim`](https://github.com/folke/tokyonight.nvim). There was an attempt to make my own colorscheme, [`pastelcula.nvim`](https://github.com/theopn/pastelcula.nvim/), but even with help from my friends, it was an impossible task for someone with a colorblindness
- Statusline: I have been using [`Lualine`](https://github.com/nvim-lualine/lualine.nvim) with a heavily modified [evil theme](https://github.com/nvim-lualine/lualine.nvim/blob/master/examples/evil_lualine.lua). At one point, I realized that with the amount of work I put in to make the custom statusline using Lualine, I could have just made a statusline from scratch. And that's exactly what I did! I am happy that I have much better understanding in Neovim's statusline system and that I have a full control over what components I can display.
- Tab/buffer line: I was using [`barbar.nvim`](https://github.com/romgrk/barbar.nvim), now I use [`bufferline.nvim`](https://github.com/akinsho/bufferline.nvim/). Honestly, there are no functional differences between two, and I might move back to Barbar since it offers less features (and that's exactly what I want). I have experiment with Neovim's stock tab system and [`tabby.nvim`](https://github.com/nanozuki/tabby.nvim), which simply beautifies the stock tab system. However, I never have that many buffers, and many of Theovim's users found the tab system confusing. I'm fine with bufferline plugins, as long as it supports tab system on the side
- Startup dashboard: I used [`dashboard-nvim`](https://github.com/nvimdev/dashboard-nvim) before. However, the author of the plugin, which also maintains [`lspsaga.nvim`](https://github.com/nvimdev/lspsaga.nvim), frequently releases breaking changes. There's nothing wrong with it, but I personally want more stability. So as a result, I wrote my own Dashboard plugin!! It was much harder than I thought it would be due to many error checking involved, but I'm happy with what I have. I am writing a post on how to write one yourself, and I will link it here once I finish.

## File and Search

Well, nothing much has changed except that I use [`telescope`](https://github.com/nvim-telescope/telescope.nvim) much more often. I have keybindings and a custom menu (more on this in the later section) for launching buffer switcher, recent files, search history, keybinding descriptions, etc. It's a great plugin.

## LSP

There are three main changes:

1. I now use [`mason-lspconfig`](https://github.com/williamboman/mason-lspconfig.nvim) so that when a user downloads an LSP server, it's automatically configured without touching a configuration file. This was the first change I made as soon as I released my configuration to other people
2. `on_attach` function for LSP servers format the file based on `vim.g.linter_status` variable, which is a custom variable I made to toggle formatting on/off. It also integrates with my custom statusline and you can see the status at all time!!
3. I have removed many LSP related plugins like [`trouble.nvim`](https://github.com/folke/trouble.nvim) and [`lspsaga.nvim`](https://github.com/nvimdev/lspsaga.nvim) and been utilizing more stock LSP function. For example, instead of `trouble`, I display buffer level diagnostics using `vim.diagnostic.open_float(0, { scope = "buffer", border = "rounded" })`. LSP on Neovim is being actively developed, and for stability and performance, I decided that this compromise (stock functions tend to be uglier and less functional -- duh) was worth it

## Note Taking

I don't use [`vimwiki`](https://github.com/vimwiki/vimwiki) on Neovim anymore. I have used it on a vanilla Vim for my journals and personal wiki for a while, and I'm currently in a process of migrating them to [`Org-roam`](https://www.orgroam.com/) (should I make Emacs post? Maybe). But I introduced [`VimTeX`](https://github.com/lervag/vimtex), which is the only Vimscript plugin in Theovim now. I use `VimTeX` along with [`TeXLab`](https://github.com/latex-lsp/texlab) LSP server. I still haven't found a better way to do real-time compile without `VimTeX`.

## Package Manager

I have migrated from [`packer.nvim`](https://github.com/wbthomason/packer.nvim) to [`lazy.nvim`](https://github.com/folke/lazy.nvim). Nothing wrong with `packer`, but it was a big trend in the Neovim community early this year to migrate to `Lazy`, so I hopped on the bus as well.

## Others

Other changes I made:

### File organization:

At one point, I broke configurations down to small chunks, such as making separate Lua files for each plugins. It may be a good practice for Lua programming, but as a Neovim configuration, I found scattered files hard to navigate and disorganized. So instead, I have broken down configuration to the smallest units where each can shine without other files. For example, one can take my `core.lua`, rename it as `init.lua`, and have a pretty decent stock Neovim experience. One can copy and paste my `plugin.lua`, and they will have a decent list of plugins and the bootstrap for [`lazy`](https://github.com/folke/lazy.nvim) package manager. One can copy my `lsp.lua`, and they will have a complete LSP setup including completion and keybindings for core functionalities, given they have [`Mason.nvim`](https://github.com/williamboman/mason.nvim) and other plugins installed. One can take my `dashboard.lua` and ... You get it.

### Utilizing floating windows

Floating windows are awesome. They look cool, they don't destroy your current window layout, when paired with scratch-buffer, they can be a great way to display information. I use floating window for two purposes:

1. Displaying various documentations
2. Notepad

Notepad is by far the favorite feature that I developed. It's a scratch-buffer paired with a small, transparent floating window, and I can write down random ideas I come across quickly and easily. The contents disappears with Neovim close, I am planning on adding a functionality to let users quickly save the contents.

//TODO image of keybinding help and notepad side by side

### Custom Menus

//TODO move this section to a separate post

I don't like unmemorable keybindings, and it's the main problems I have with Emacs. They have keybindings for almost every features (and adding packages only add more), and often times when I use Emacs, I almost always hit a random keystroke that does some weird things. It wouldn't be a problem if the keybindings are intuitive like Vim's editing keybindings (e.g. [d]elete [a] [w]ord, [i]nsert), but they aren't.

On the similar note, one great thing about Lua plugins that unlike Vimscript plugins, they don't define bunch of keybindings for you (which many Vimscript plugins tended to do). They merely gives you examples of keybindings you can define in your `setup()` function.

I took this opportunity and made many keybindings that I think are intuitive. However, the problem comes when there are many functionalities that I "often" use. Let's use [`telescope`](https://github.com/nvim-telescope/telescope.nvim) as an example. Following keybindings make a lot of sense

- `<SPC>ff`: [f]ind [f]ile
- `<SPC>fb`: [f]ile [b]rowser
- `<SPC>f/`: / is a search binding for Vim

However, I also want to quickly access things like "Telescope Commands" (list of commands), "Telescope Command History," and "Telescope Colorscheme." Those are features I don't use as much as the ones with dedicated keybindings, but I use them nonetheless. How do I make keybindings? `<SPC>fc` and `<SPC>fcc`? Aren't they getting too complicated?

That's when the custom menus come in play. I utilize `vim.ui.select` to group these features together, and the only binding I need to memorize is `<SPC> [f]ind [a]ction`. After that, features are one keystroke away from accessing. No need to squeeze your brain for keybindings you cannot memorize.

//TODO weird Org keybinding

### Comments

Every functions have a comment in the following form:

```lua
--[[ function_name()
-- This function does this and that
-- Use it like so: function_name(arguments)
--
-- @requires util.lua
-- @arg str: string to be processed
-- @returns cool table
--]]
function function_name()
-- ...
end
```

I coudln't find the definitive guide on Lua coding standard, so I mixed Java's Javadoc style comments with other programming language function headers that I knew. Kids, comment your code. It will help your future self.


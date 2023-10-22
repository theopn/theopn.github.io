+++
draft = false
title = "30 Plug-ins and 700 Lines of Code Later, Neovim is my favorite IDE"
date = "2022-08-25T13:39:11-04:00"

author = "Theo"
description = "List and descriptions of plug-ins I use with Neovim (2022). Let's go over what stuck with me and what did not in 2023"
tags = ["Neovim"]

cover = ""
readingTime = true
showFullContent = false

toc = true
tocTItle = "Plug-ins"
+++

## One-Year (2023-10-21) Update

I know more Lua, I know more Neovim, and as a result, my Neovim setup looks quite different now.
In fact, I separated my Neovim configuration into a separate repository [Theovim](https://github.com/theopn/theovim/).
I made comments on what plugins stuck with me and what did not in each section.

---

# 30 Plug-ins and 700 Lines of Code Later, Neovim is my favorite IDE

If you know me, chances are you heard about how much I love Vim. I love its portability and expandability. About 3 months ago, I made a switch to Neovim. Neovim is a "fork" of Vim (now they are too different from each other to be considered a fork, in my opinion. Consider them as cousins) that tries to make the project community-driven and extensible by utilizing Lua as a built-in language. Vimscript is still supported, and many people simply migrate their old `.vimrc` while enjoying all the new Lua plug-ins. This is totally okay, but I wanted to take full advantage of Neovim by building a 100% Lua configuration. As with all migration of framework, it's not porting the old base configuration that is difficult; it is the process of optimizing the settings and programs around it to take full advantage of the new framework (think of the migration process from i3 WM to Sway). The result was my half-baked Neovim configuration that I stuck for a month. I migrated all the settings and keybindings to Lua, but I got stuck with complex Neovim LSP and tree-sitter framework, so I kept using `coc.nvim` and traditional highlighting. Sure they worked well, but I wanted more efficient and improved features of Neovim. Coincidentally, I have been working on my Awesome WM setup, and as the project grew, I felt a need for a stronger Lua IDE. So I set down for the weekend and worked on improving my Neovim.

{{< image src="/img/writing/2022-08-25-neovim-ide/neovim-c.jpg" alt="C Editing in Neovim" position="center" style="border-radius: 8px;" >}}

The result? A simple yet powerful IDE for C, Lua, or any language you want (except for Java. That thing is just too bloated). I will go through 30 plug-ins that make up my Neovim.

> 1. I do not know if "many people simply migrate their old `.vimrc`..." is still true.
>      Neovim has grown quite significantly over the past year.
>      Stack Overflow developer survey shows that the percentage of Neovim users (who responded to the Stack Overflow survey) increased from [6%](https://survey.stackoverflow.co/2022#section-most-popular-technologies-integrated-development-environment) to [11%](https://survey.stackoverflow.co/2023/#section-most-popular-technologies-integrated-development-environment) in 2023.
>      Subsequently, there are more and more people who start their Vim journey in Neovim compared to transitioning from Vim.
> 2. I still have 28 plug-ins in my Neovim configuration, but the contents of them are quite different.
> 3. I will make updates in quote blocks like this each section!
>
> -- 2023 Theo

## Appearance

### OneDark.nvim

My choice of theme. It offers 6 different color tones and configurable styles for components. Supports transparency too!

> I still like OneDark, but I primarily use TokyoNight these days.
> Lately, I have been thinking about using a simpler colorscheme like Base 16 to minimize distraction and focus on highlights that actually matter.
>
> -- 2023 Theo

### Lualine.nvim

Lightweight and customizable status line plug-in. I do not use a whole lot of features and would be perfectly happy with building a status line with built-in features as I did with the vanilla Vim, but LSP integration (to show the number of warnings, errors, etc) is essential for me.

> I wrote my own Statusline based on Mini.Statusline.
> I really like customizing the looks of my own Neovim, and Statusline is the most prominent feature of it.
>
> -- 2023 Theo

### Barbar.nvim

"Tabbar" plug-in. The built-in tab/buffer system of Vim/Neovim is a bit untraditional and frankly not very productive for me. Barbar is very simple and offers a more "traditional" approach to the tab system. I am planning on trying out `bufferline.nvim`, which seems to be an improved approach to Vim buffer management.

> I no longer agree with "the built-in tab/buffer system of Vim/Neovim is ... not very productive for me" once I started to think of tabs as "workspaces" in your macOS/Window desktop/Linux window managers.
> Vim's built-in tab and buffer system is better than VS-Code-like bufferlines **as long as you have a good way to navigate between buffers**.
> For me, that is `:Telescope buffers`, which is bound to `<Space><Space>` in my config.
>
> For the actual "Tabline," I wrote my own while I was writing my Statusline.
> It does not change the built-in tab system, but it provides nice additions like icons, number of open windows in a tab, modified status, and number of tabs and buffers.
> Needless to say, I love the look of combined Tabline and Statusine, and they make me much more productive.
>
> -- 2023 Theo

### Dashboard-nvim

Very customizable startup screen.

> I wrote my own Dashboard based on NvChad Dash.
> It was the most unnecessary and complicated UI components I wrote, but I had fun doing it.
>
> -- 2023 Theo

### Nvim-notify

It redirects all the bottom notification (basically whenever `vim.notify` is called) to the top right, prettier notification. `vim.notify` is a Neovim-only protocol, and not all plug-ins utilize it, but plug-ins like `Telescope` and LSP components can display useful information. If you have a lot of Vimscript-based plug-ins or are okay with bottom error messages, you can skip this plug-in.

> Sure, it makes the notification pretty, but I did not think it was very useful.
> I simply use `:messages` command to retrieve all the previous error messages and notifications from Neovim.
>
> -- 2023 Theo

### vim-pets

Must have.

{{< image src="/img/writing/2022-08-25-neovim-ide/vim-pets.jpg" alt="C Editing in Neovim" position="center" style="border-radius: 8px;" >}}

> It was cute, but I never really used it.
> Sorry digital Oliver.
>
> -- 2023 Theo

## File et Search

### Gitsigns.nvim

It displays git diff and blame information, and much more useful Git information within the file.

> I still use it, and it is the only Git plugin I use!
> Gitsigns also have fantastic features like hunk preview, diff preview, and Git information variables that power my Statusline Git information.
>
> -- 2023 Theo

### nvim-treesitter

Tree sitter is an incremental parsing library. Long story short, it provides much more useful syntax highlighting and information about the source code. Like LSP, it is a built-in back-end feature of Neovim that needs to be enabled through the official plug-in.

> Treesitter is still the core part of my configuration, and I use a lot more features including incremental selection.
>
> -- 2023 Theo

### nvim-ts-rainbow

Rainbow matches for parentheses that utilize the tree-sitter library.

> I never really understood what this plugin did.
> I typically use `%` keybinding to jump between matching parenthesis.
>
> -- 2023 Theo

### nvim-tree.lua

Basically NERD Tree but written in Lua.

> I no longer use any file tree plugin.
> I use Oil.nvim to manage my files.
>
> -- 2023 Theo

### Telescope.nvim

A fuzzy finder within Vim. It not only can be used for finding files and text within the file, but also can be used for browsing commands, Git commits, LSP information, and much more. It is one of the essential plug-ins for my Neovim setup.

> It still is one of the essential plug-ins for my Neovim setup.
>
> -- 2023 Theo

### Telescope-file-browser.nvim

A file browser extension for Telescope. I use this and `Telescope find_files` all the time. File browser for the files in the parent directories, find files for the files in the child directories.

## LSP

Ah, here we go

### nvim-lspconfig

Third-party plug-ins cannot take advantage of the built-in LSP until you configure LSP using this official plug-in.

### Mason.nvim

A language server management tool. It is by the same author of `nvim-lsp-installer`, and I find Mason to be mature enough to be used over the outgoing `nvim-lsp-installer`. Install a language server of your choice, activate it in the `nvim-lspconfig`, and boom, you will see all the warning messages and feel horrible about your code.

### nvim-cmp

Auto-completion program that can receive snippets from

1. LSP server
2. `cmp-` plug-ins that I will introduce shortly
3. Snippet engine like `LuaSnip`

It also supports a tab completion as well with a little tweak.

### cmp-buffer, cmp-path, cmp-cmdline

Completion engines for words in the current buffer, the file path of your computer, and Vim command lines. `cmp-cmdline` is a plug-in that I didn't know I needed until I started using it. So much better than the built-in command line completion.

### cmp-luasnip, cmp-nvim-lsp

Bridge plug-ins between `nvim-cmp` and `luasnip`/`nvim-lspconfig`.

### LuaSnip

LuaSnip is a snippet *engine*. By itself, it does not add anything to `nvim-cmp`, which I will talk about later. However, once you feed a collection of snippets into it, it will parse them and recommend them through `nvim-cmp` when appropriate.

### friendly-snippets

The collection of snippets. The repository contains a lot of good VS Code style snippets. I am using my own fork of it because the original C snippet does not put the opening curly brace next to the function header (`int main(int good, char* programmer[]) {`).

### Trouble.nvim

JetBrains IDE style view of errors, warnings, etc from the LSP server.

> 1. Most of its functionalities can be done with built-in `vim.lsp`/`vim.diagnostics` functions.
>
> -- 2023 Theo

### Lspsaga.nvim

It bundles LSP-related actions like refactoring, code actions for warning/error items, reference finder, etc.

> 1. Most of its functionalities can be done with built-in `vim.lsp`/`vim.diagnostics` functions.
> 2. It was like 10 plug-ins jammed into one, and I frankly would use one plug-in that does one thing.
>
> -- 2023 Theo

## Note Taking

I am planning on using Neovim for my notes next semester and here are some plug-ins that will assist me with that (and also with general markdown editing).

### Glow.nvim

Glow is a command line markdown previewer, and this plug-in displays the preview in a pop-up window inside of Neovim. I was worried since Fedora DNF repository does not ship with Glow and I don't like binary installs, but the plug-in provides a binary file of Glow in `~/.local/bin/`, which is nice.

> I no longer take notes with Neovim.
> I rewrote my vanilla Vim config to be a focused note taker using Vimwiki.
>
> -- 2023 Theo

### vimwiki

Vimwiki is a personal wiki management plug-in for Vim/Neovim. It offers a lot of features, but I mainly use it for its quick file reference feature. I do not like the fact that Vimwiki is completely written in Vimscript and that it does not play nicely with the markdown LSP/tree-sitter server, and I am considering making the vanilla Vim to be the dedicated note taker. I have tried `neorg` to take notes, but Vimwiki still offers the best functionality for me.

> I no longer take notes with Neovim.
> I rewrote my vanilla Vim config to be a focused note taker using Vimwiki.
>
> -- 2023 Theo

### Vim-figlet

It connects to the command line tool `figlet` and generates ASCII text art within Vim. Fun and potentially useful for making more engaging notes (I am a sucker for ASCII art. Take a look at my dotfiles).

> I just use the command line `figlet`
>
> -- 2023 Theo

## Others

### Packer.nvim

The package manager for Neovim. Makes this all possible. Mwah.

> Everyone migrated to Lazy.nvim, so did I.
> The only thing I gained was a better syntax though, I think Lazy loading is overhyped.
>
> -- 2023 Theo

### Plenary.nvim

Lua function library for Neovim. I believe Telescope and Gitsigns need this as a dependency.

### nvim-web-devicon

Have no clue why it's named like that, but it's an icon pack for Barbar, Lualine, and NvimTree.

## Minimalist-ish approach

I am not opposed of having plug-ins if it significantly improves my workflow (or if they are cute), but I also try to rely on Vim's built-in feature if they don't have a significant downside. One example is using custom keybindings instead of `nvim-autopairs`. The bindings are

```lua
-- I am also sharing this for loop way of setting keybindings
local function vim_map(mode, shortcut, target)
  vim.keymap.set(mode, shortcut, target, { noremap = true, silent = true })
end

local key_opt = {
  -- Stuffs ...
  { 'i', "(", "()<LEFT>" },
  { 'i', "[", "[]<LEFT>" },
  { 'i', "{<CR>", "{<CR>}<ESC><S-o><ESC><S-i><TAB>" },
  -- Stuffs ...
}

for _, v in ipairs(key_opt) do
  vim_map(v[1], v[2], v[3])
end
```

> I do not do this anymore (although I still have this in my minimal vanilla Vim configuration), I use `nvim-autopairs`.

It can get a bit annoying when you try to insert one parenthesis or curly bracket, but it's a very simple and clean solution that I don't have many complaints about (I also experienced some weird bugs when using `nvim-autopairs`, such as " not being inserted when it's adjacent to parentheses).

> The "bug" I described of `nvim-autopairs` was a feature actually, and `nvim-autopairs` does a lot of nice things other than inserting the matching parentheses.

`nvim-cmp` comes with a [way](https://github.com/hrsh7th/nvim-cmp/wiki/Menu-Appearance#how-to-add-visual-studio-code-codicons-to-the-menu) to customize menu appearance, including VS-Code like pictogram. This eliminates the need for plug-ins like `lspkind.nvim`.

> Yep, this is still nice!

Neovim has several built-in LSP-related commands. On example is `vim.lsp.buf.hover()` to view LSP hover doc. All you have to do is map `<CMD>lua vim.lsp...` to your choice of keybindings, and you can view hover docs for code components without extra plug-in (although plug-in like LSPSage makes it easier to access the LSP features).

> Yeah Theo, why did you even use LspSaga if you were going to mix them up with built-in functions?

I also utilize Packer's lazy load features to minimize boot time, particularly `ft` keyword. You can specify plug-ins to be loaded in a certain file using `ft` keyword, so I block tree-sitter to be launched only with files I have language servers for and markdown-related plug-ins to markdown files.

> This was the only form of lazy loading I do (and Lazy does this automatically), and it is not even the tip of the iceberg compared to other lazy loading techniques like setting priorities between plug-ins, etc.
> I think sacrificing the readability and simplicity of your configuration files for a couple milliseconds of gain in startup time (if there is any) is an unnecessary trade-off.
>
> -- 2023 Theo

## You got more things to say?

No. Not for now. I am trying to keep the number of plug-ins around 30 - 33, and I am really happy with the current setup. Two things I am considering now are moving Vimwiki to Vim and integrating a CLI PDF reader for LaTeX editing. Other than that, I am significantly more productive when I use and tweak Neovim, compared to any other IDE/text editor (including VS Code).

`:q!`

> It was fun seeing how my thoughts has changed over the year.
> I went from "use whatever plugin is popular" to "maximize built-in features and keep the config minimal."
> Until the next time I make a Neovim related post, `:q!`
>
> -- 2023 Theo


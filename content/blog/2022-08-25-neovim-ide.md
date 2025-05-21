+++
title = "30 Plug-ins and 700 Lines of Code Later, Neovim is my favorite IDE + commentary in 2025"
date = "2022-08-25"
description = "List and descriptions of plug-ins I use with Neovim (2022)"
tags = ["Neovim", "Lua"]
+++

## *Looking back on this post after two years (2025-05-23)*

I know more Lua, I know more Neovim, and my Neovim looks quite different now.

![2025-05-15-nordfox](/images/2025-05-23-neovim-over-time/2025-05-15-nordfox.jpg)

A couple of things in this article that I no longer do/agree with:

- Using a "bufferline" plugin (because I did not understand the native tab system): once I started to think of tabs as *workspaces* in window managers and buffers as *open applications*, it made all sense.
    I [wrote a custom tabline](blog/2025-05-13-tabline-in-lua.md) and bound `<space><space>` to launch `:FzfLua buffers` to quickly switch between buffers.
    This allows me to use Neovim in one continuous session without needing to close and open new Neovim sessions.
- In fact, I removed most UI-related plugins (lualine.nvim, barbar.nvim, dashboard-nvim, etc.) and wrote my own UI elements (statusline, winbar, tabline, and even dashboard at one point, though I removed it now).
    It was an opportunity to learn the Neovim API and made Neovim feel more personal.
- Using a file tree plugin: I switched to [Oil.nvim](https://github.com/stevearc/oil.nvim), and I now cannot live without it
- I replaced Telescope.nvim with [fzf-lua](https://github.com/ibhagwan/fzf-lua) and Telescope-file-browser.nvim with [a custom function](/blog/2025-04-23-fzf-lua-parent-dir.md)
- I cut out a lot of LSP plugins (trouble.nvim, lspsaga.nvim) and replaced them with the native `vim.lsp` and `vim.lsp.buf` module methods
- I migrated to [lazy.nvim](https://github.com/folke/lazy.nvim)
- Overall, the config is better organized and actually maintainable

Nonetheless, hope you enjoy me geeking out.

# 30 Plug-ins and 700 Lines of Code Later, Neovim is my favorite IDE

If you know me, chances are you heard about how much I love Vim. I love its portability and expandability. About 3 months ago, I made a switch to Neovim. Neovim is a "fork" of Vim (now they are too different from each other to be considered a fork, in my opinion. Consider them as cousins) that tries to make the project community-driven and extensible by utilizing Lua as a built-in language. Vimscript is still supported, and many people simply migrate their old `.vimrc` while enjoying all the new Lua plug-ins. This is totally okay, but I wanted to take full advantage of Neovim by building a 100% Lua configuration. As with all migration of framework, it's not porting the old base configuration that is difficult; it is the process of optimizing the settings and programs around it to take full advantage of the new framework (think of the migration process from i3 WM to Sway). The result was my half-baked Neovim configuration that I stuck for a month. I migrated all the settings and keybindings to Lua, but I got stuck with complex Neovim LSP and tree-sitter framework, so I kept using `coc.nvim` and traditional highlighting. Sure they worked well, but I wanted more efficient and improved features of Neovim. Coincidentally, I have been working on my Awesome WM setup, and as the project grew, I felt a need for a stronger Lua IDE. So I set down for the weekend and worked on improving my Neovim.

![Neovim C](/images/2022-08-25-neovim-ide/neovim-c.jpg)

The result? A simple yet powerful IDE for C, Lua, or any language you want (except for Java. That thing is just too bloated). I will go through 30 plug-ins that make up my Neovim.

## Appearance

### OneDark.nvim

My choice of theme. It offers 6 different color tones and configurable styles for components. Supports transparency too!

### Lualine.nvim

Lightweight and customizable status line plug-in. I do not use a whole lot of features and would be perfectly happy with building a status line with built-in features as I did with the vanilla Vim, but LSP integration (to show the number of warnings, errors, etc) is essential for me.

### Barbar.nvim

"Tabbar" plug-in. The built-in tab/buffer system of Vim/Neovim is a bit untraditional and frankly not very productive for me. Barbar is very simple and offers a more "traditional" approach to the tab system. I am planning on trying out `bufferline.nvim`, which seems to be an improved approach to Vim buffer management.

### Dashboard-nvim

Very customizable startup screen.

### Nvim-notify

It redirects all the bottom notification (basically whenever `vim.notify` is called) to the top right, prettier notification. `vim.notify` is a Neovim-only protocol, and not all plug-ins utilize it, but plug-ins like `Telescope` and LSP components can display useful information. If you have a lot of Vimscript-based plug-ins or are okay with bottom error messages, you can skip this plug-in.

### vim-pets

Must have.


![Vim Pets](/images/2022-08-25-neovim-ide/vim-pets.jpg)

## File et Search

### Gitsigns.nvim

It displays git diff and blame information, and much more useful Git information within the file.

### nvim-treesitter

Tree sitter is an incremental parsing library. Long story short, it provides much more useful syntax highlighting and information about the source code. Like LSP, it is a built-in back-end feature of Neovim that needs to be enabled through the official plug-in.

### nvim-ts-rainbow

Rainbow matches for parentheses that utilize the tree-sitter library.

### nvim-tree.lua

Basically NERD Tree but written in Lua.

### Telescope.nvim

A fuzzy finder within Vim. It not only can be used for finding files and text within the file, but also can be used for browsing commands, Git commits, LSP information, and much more. It is one of the essential plug-ins for my Neovim setup.

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

### Lspsaga.nvim

It bundles LSP-related actions like refactoring, code actions for warning/error items, reference finder, etc.

## Note Taking

I am planning on using Neovim for my notes next semester and here are some plug-ins that will assist me with that (and also with general markdown editing).

### Glow.nvim

Glow is a command line markdown previewer, and this plug-in displays the preview in a pop-up window inside of Neovim. I was worried since Fedora DNF repository does not ship with Glow and I don't like binary installs, but the plug-in provides a binary file of Glow in `~/.local/bin/`, which is nice.

### vimwiki

Vimwiki is a personal wiki management plug-in for Vim/Neovim. It offers a lot of features, but I mainly use it for its quick file reference feature. I do not like the fact that Vimwiki is completely written in Vimscript and that it does not play nicely with the markdown LSP/tree-sitter server, and I am considering making the vanilla Vim to be the dedicated note taker. I have tried `neorg` to take notes, but Vimwiki still offers the best functionality for me.

### Vim-figlet

It connects to the command line tool `figlet` and generates ASCII text art within Vim. Fun and potentially useful for making more engaging notes (I am a sucker for ASCII art. Take a look at my dotfiles).

## Others

### Packer.nvim

The package manager for Neovim. Makes this all possible. Mwah.

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

## You got more things to say?

No. Not for now. I am trying to keep the number of plug-ins around 30 - 33, and I am really happy with the current setup. Two things I am considering now are moving Vimwiki to Vim and integrating a CLI PDF reader for LaTeX editing. Other than that, I am significantly more productive when I use and tweak Neovim, compared to any other IDE/text editor (including VS Code).

`:q!`


+++
draft = false
title = "Vimrc Guide Part 1"
date = "2022-06-20T18:58:40-04:00"

author = "Theo"
description = ".vimrc Guide for Beginners"
tags = ["Vim"]

cover = ""
readingTime = true
showFullContent = false

toc = true
tocTItle = "Table of Contents"
+++

Hopefully you have decided to use Vim after attending our passionate lecture and seeing other people's cool Vim setup. However, when you open up Vim on your computer, it looks nothing like other people's Vim. Why is that?

Assuming you haven't configured your .vimrc when you open up Vim, it will not load any settings (if you have, open up Vim with `vim --clean`). Now in normal mode in Vim, type the command `:set number`, and you will see that your Vim now shows the line numbers on the left side. You can turn this off with `:set nonumber`. What about changing the theme? Try typing in `:colorscheme blue` to execute one of the beautiful built-in themes. However, when you close the Vim and open it again (in case you forgot, `:q` or `:q!` to force close), the settings you executed in the previous sessions are not saved. This is why if you have settings that you want to be executed every time you open up Vim, you will have to configure them in .vimrc.

## Understanding .vimrc

The dot in front of the .vimrc indicates \*nix system that the file is hidden. You can test this by executing `ls` and `ls -a` in your home directory (`~/`), notice how many hidden files you have in the system when you view them using `-a` flag. Hidden files are used by many programs to store configuration files, and many programmers configure, export, and import these "dotfiles" to personalize and back up their programming environment. `vim` in the file name stands for Vim, of course, and `rc` stands for "run commands." These are commonly used by command-line tools to indicate files with user-configured settings that get executed on the startup. Some examples include `.bashrc`, `.npmrc`, and `.zshrc`.

## Configuring .vimrc

Now let's begin to configure `.vimrc`. The first step is to create one since Vim does not make one by default. In your home directory (`~/`), create `.vimrc` using `touch ~/.vimrc`. Now let's open this up with `vi ~/.vimrc`.
(Notes: You might already have `.vimrc` with some settings if you're using the Purdue CS SSH server).

Here, you can add settings and even more complicated auto-commands and functions using Vimscript language. Let's start with some easy but essential settings, shall we?

```vimscript
" Double quotes are used for comments!
" Below are .vimrc contents!

" Display line numbers
set number
" Alternatively, some people prefer to see line numbers relative to the current line
set relativenumber
" Automatically indent code
set autoindent
" Highlight search result
set hlsearch
" With this, you can use a mouse for scrolling, moving cursor, etc!
set mouse=a
" Turn syntax coloring for supported files
syntax on
" For the aesthetic
colorscheme blue
" These makes tabs to be 2 spaces, which is a convention with C programming
set tapstop=2 shiftwidth=2 expandtab
```

You can explore more commands in built-in Vim manual, using `:help` followed by the commands (e.g. `:help number`).

You can map some keys as well. Let's map `(` in insert mode to `()` so that all the parenthesis are automatically closed!

```vimscript
inoremap ( ()<LEFT>
```

`i` stands for the insert mode and `noremap` stands for non-recursive mapping. Recursive mapping (achieved using `map`) can be expanded to another mapping (i.e. You map a to b, c to b, then c does what a does), whereas non-recursive mapping does not extend the mapping to another mapping. I personally use and recommend `noremap` for most cases to prevent confusion and errors. Finally, \<LEFT\> moves the cursor back inside of the parenthesis (left arrow key)!

## Taking .vimrc further

Vim comes with a language called "Vimscript," which is a full-fledged (Turing complete) language, albeit limited in its syntax and usability. You can make custom functions and auto-commands, so Let's make a simple function that displays a message and opens your `.vimrc`.

```vimscript
function Hi()
  " e command is used to open a file
  exec 'e ~/.vimrc'
  echo 'Hello! You just opened .vimrc!'
endfunction
```

Paste the function above in your `.vimrc`, reopen Vim, and type `:call Hi()`. You will see that it opens your `.vimrc` with a welcome message! (Both double quotes and single quotes can be used for strings - there is only one technical difference, though I recommend you to stick with a single quote to prevent confusion with comments.)

## Conclusion

Vim is a powerful text editor that is present on pretty much every \*nix system, and you can easily unlock many potentials of Vim by configuring `.vimrc`. If you want more inspiration, check out [an article for a simple .vimrc](https://docs.j7k6.org/my-minimal-vimrc/) and ["ultimate .vimrc" for power users](https://github.com/amix/vimrc). You can also check out [my dotfiles repository](https://github.com/theopn/dotfiles), which also includes Lua configuration files for Neovim (a fork of Vim that is community-developed and supports a wide variety of plugins). :wq!



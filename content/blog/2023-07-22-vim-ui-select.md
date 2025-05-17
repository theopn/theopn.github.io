+++
draft = false
title = "[I DO NOT RECOMMEND THIS ANYMORE] Using vim.ui.select to Group Features and Fix Keybinding Problems"
date = "2023-07-22T18:49:46+09:00"

author = "Theo"
description = "[I DO NOT RECOMMEND THIS ANYMORE] Emacs diss disguised as a friendly Neovim help"
tags = ["Neovim", "Lua"]

cover = "/images/2023-07-22-vim-ui-select/with-dressing-nui-theme.jpg"
readingTime = true
showFullContent = false

toc = true
tocTItle = "TOC"
+++

## Confession


**I have to admit, this post aged like milk.**

After sharing this post, some people suggested trying the [Which Key](https://github.com/folke/which-key.nvim) plugin.
For those of you who do not know, Which-Key is a plugin that displays a pop-up with possible keybindings when you start typing in normal and visual mode.

I refused to use Which Key, thinking "good keybindings should be memorable without a guide."
But the truth is, with the number of LSP, Treesitter, and Telescope features in the modern Neovim users' configurations, some sort of visual guide for keybindings is a must.
My `vim.ui.select()` solution in this post is essentially one of the visual guides.
An inefficient one, I should say, because it is creating yet another set of keybindings in the form of `<leader>f` + `1, 2, 3, ...` and displaying them using `vim.ui.select()`.
After realizing this, I concede that Which-Key is a better and more efficient solution.

{{< figure src="/images/2023-07-22-vim-ui-select/which-key.jpg" alt="Which Key" position="center" style="border-radius: 8px;" caption="My Which Key setup" captionPosition="left" captionStyle="color: black;" >}}

With the right amount of `timeoutlen` (mine is 300ms), Which-Key can guide you through keybinding efficiently.
Also, if you use keybinding chains for groups of features (e.g., `<leader>f<char>` for all Telescope keybindings, `<leader>g<char>` for all Git keybindings), you can [register group name](https://github.com/folke/which-key.nvim#%EF%B8%8F-mappings) to show up in Which-Key.

Which-key also provides extra functionalities, such as:

- better UI for spell suggestion (`z=`)
- displaying registers when using `C-r` in the insert mode

I got half of the equation right by using keybinding chains when making keybindings, though, and **I still think this post has value as a "fun Lua hack", just not as a good way to use Neovim**.

Now, back to the original post:

## Emacs Rant (You Can Skip This Section)

I have been using both (Doom) Emacs and (Neo)vim for a year now. They both have pros and cons, but I use Emacs for Org-mode (I am a slave to Org-agenda and Org-roam) exclusively, and Neovim is my main text editor, IDE, and configuration playground. You have Evil mode and an experience with Emacs, why not use Emacs as your primary text editor, one might ask: well, out of many reasons, **I don't like Emacs keybindings**.

To begin with, Emacs has too many keybindings. If you have used Vim and Emacs, you know one has significantly more keybindings than the other, even when you double/triple-count modal bindings in Vim. Even worse, most Emacs packages I encountered create tons of keybindings *for you*. Because there are so many keybindings, I almost always hit a random keystroke and execute a random function. I consider that an "undefined behavior" (C reference), and **I would rather see an error message than delete 3 random words in the buffer**.

This would be less of a problem if those keybindings were good and intuitive. Vim keybindings are great -- I don't need to talk about how good `hjkl` is, and even bindings like `[d]elete [a] [w]ord`, `[d]elete [t]ill`, and `[w]ord`, are very easy to memorize and fit my fingers like they were meant to be. On the other hand, Emacs keybindings are... well, navigation keybindings (e.g. `C-p/n/f/b`), which should focus more on ergonomics instead of mnemonics, are difficult to use because of how far apart they are, and feature keybindings (e.g. `C-x +` to balance windows) often completely ignores mnemonics and are impossible to memorize (and it's even worse with keybindings that come with packages). I am slightly cherry-picking examples here, but I believe that there are reasons why 80% of Emacs tutorial starts by installing `evil` and completely ignores stock keybindings.

{{< figure src="/images/2023-07-22-vim-ui-select/org-mode-bindings.jpg" alt="Average Emacs Keybindings" position="center" style="border-radius: 8px;" caption="From [Org-mode manual](https://orgmode.org/manual/Key-Index.html)" captionPosition="left" captionStyle="color: black;" >}}

In my opinion, Vim used to have a toned-down version of this problem. Vimscript plug-ins like [`VimTeX`](https://github.com/lervag/vimtex) and [`VimWiki`](https://github.com/vimwiki/vimwiki) created many keybindings *for you*. However, I noticed that most Lua plug-ins ditched this behavior in favor of merely giving you examples of keybindings you *could* make in the `setup()` functions.

## The Problem

Okay, enough of dissing Emacs, how is it relevant to the post? Here's the problem: I want to avoid the Emacs keybinding situation in my Neovim configuration. I **only want to have keybindings that are easy to memorize** and **avoid having too many keybindings**.

However, Neovim and its plug-in ecosystem offer far too many functionalities to easily achieve that. Let's use [`telescope`](https://github.com/nvim-telescope/telescope.nvim) as an example. Following is the list of features and keybindings that I frequently use.

- `<SPC>fb`: [f]ile [b]rowser
- `<SPC>fc`: [f]ind [c]ommand history
- `<SPC>ff`: [f]ind [f]ile
- `<SPC>f/`: Buffer search

Other than the ones I listed above, I often use `Telescope search_history`, `Telescope colorscheme`, and `Telescope help_tags`. And here comes the dilemma:

1. I want them to be more easily accessible than typing a bunch of letters to the command prompt
2. But how do I make three extra keybindings without running out of letters? How do I differentiate `Telescope command_history` and `Telescope colorscheme`?
3. Besides, are they worthy of dedicated keybindings if I will only use them once in a while?

Maybe Emacs was right all along!

## The Solution

Of course not. To tackle this problem, I use `vim.ui.select()` and make a custom menu to bundle all these "often-used" features. Thoroughly read the following code and have them in your config file.

```lua
--[[ create_select_menu()
-- Create a menu to execute a Vim command or Lua function using vim.ui.select()
-- Example usage:
-- local options = {
--   [1. Onedark ] = "colo onedark"
--   [2. Tokyonight ] = function() vim.cmd("colo tokyonight") end
-- }
-- create_select_menu("Choose a colorscheme", options)
--
-- @arg prompt: the prompt to display
-- @arg options_table: Table of the form { [n. Display name] = lua-function/vim-cmd, ... }
--                    The number is used for the sorting purpose and will be replaced by vim.ui.select() numbering
--]]
local create_select_menu = function(prompt, options_table)
-- Or M.create_select_menu = function(prompt, options_table)

  -- Given the table of options, populate a list with option display names
  local option_names = {}
  local n = 0
  for i, _ in pairs(options_table) do
    n = n + 1
    option_names[n] = i
  end
  table.sort(option_names)

  -- Return the prompt function. These global function var will be used when assigning keybindings
  local menu = function()
    vim.ui.select(
      option_names, --> the list we populated above

      {
        prompt = prompt, --> Prompt passed as the argument
        -- Remove this variable if you want to keep the numbering in front of option names
        format_item = function(item) return item:gsub("%d. ", "") end
      },

      function(choice)
        local action = options_table[choice]
        -- When user inputs ESC or q, don't take any actions
        if action ~= nil then
          if type(action) == "string" then
            vim.cmd(action)
          elseif type(action) == "function" then
            action()
          end
        end

      end)
  end

  return menu
end
```

A few things to note:

1. Always have a function header comment! Your future self will thank you
2. In this example, I made it as a local function, but you can have it as a part of module (I have it as a module function in `util.lua` and call `require(util).create_select_menu(...)`)
3. My version requires numbering (`n. `) in front of each option name for sorting purposes. If you have a better solution, please let me know in (Reddit) comments
    - It will be more performant if you populate the `option_names` array as a local variable out of the function

### Usage

Let's fix our problem with `Telescope`. Have the following code in the same config file.

```lua
local telescope_options = {
  ["1. Search History"] = "Telescope search_history",
  ["2. Help Tags"] = "Telescope help_tags",
  ["3. Colorscheme"] = function() require("telescope.builtin").colorscheme() end --> You can pass Lua func if you wish
}
local telescope_menu = create_select_menu("Telescope feature to use:", telescope_options)
vim.keymap.set('n', "<leader>fa", telescope_menu, { noremap = true, silent = true })
```

And when I hit `<leader>fa`, I get a nice menu like this:

{{< figure src="/images/2023-07-22-vim-ui-select/without-dressing.jpg" alt="Final product!" position="center" style="border-radius: 8px;" caption="Our Telescope menu!" captionPosition="left" captionStyle="color: black;" >}}

From here, these features are one keystroke away!

{{< figure src="/images/2023-07-22-vim-ui-select/telescope-help-tag.jpg" alt="//TODO" position="center" style="border-radius: 8px;" caption="Using the Help Tags option" captionPosition="left" captionStyle="color: black;" >}}

You can also use a plug-in like [`dressing.nvim`](https://github.com/stevearc/dressing.nvim) to make it look nicer.

{{< figure src="/images/2023-07-22-vim-ui-select/with-dressing-nui-theme.jpg" alt="with dressing.nvim" position="center" style="border-radius: 8px;" caption="With dressing.nvim NUI theme" captionPosition="left" captionStyle="color: black;" >}}

One might say that they can make "longer" keybindings like `<leader>[f]ind [s]earch [h]istory` or `<leader>[f]ind [c]ol[o]`. I disagree. It won't solve the keybinding pollution problem I mentioned earlier, and I would certainly not remember any of the keybindings.

It makes sense to have keybindings for features that are frequently used and demanded instantly (like buffer switching and finding files), but I can afford an extra keystroke for changing colorscheme.

## Conclusion

This might not be your cup of tea, but I love making custom menus. I no longer forget these "often-used" functionalities while not spending 30 seconds trying to figure keybindings out. I have ones for LSP functions (`<SPC>[c]ode [a]ctions`), Git features (`<SPC>[g]it [a]ctions`), and terminal.

If you have found this post helpful and want to know more about my Neovim setup, leave a star on my [Theovim](https://github.com/theopn/theovim) repository! It is full of fun features like this.

Thank you!


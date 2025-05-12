+++
date = '2025-05-13'
title = 'Wrote my own Tabline in Lua (with clickable buttons + buffers in the current tab)'
draft = false
description = 'Showing off my custom tabline (+ Winbar + Statusline)'
tags = ['Neovim']
+++

![tabline-demo](/images/2025-05-13-tabline-in-lua/tabline-demo.gif)

I could not find a tabline plugin that I liked (closest I could find was [tabby.nvim](https://github.com/nanozuki/tabby.nvim)), so I wrote my own tabline to:

- display the list of tabs and number of windows for each tab (if more than one, excluding floating windows) on the left side
- display the list of buffers opened in the current tab (excluding `unlisted` buffers), highlighting the focused one, on the right side

One surprising thing was that it does not say anywhere in the help documentation that the tabline buttons (`%nT` where `n` is the tab number) are draggable, but I was surprised that they in fact are in both Neovide and Neovim running under Wezterm and Kitty.

Overall, configuring tabline (+ winbar and statusline) was a good learning experience for me, learning about

- tab number v.s. tab ID
- various tab related Neovim API (`nvim_tabpage_list_wins`, `nvim_tabpage_is_valid`,  etc.) and Vimscript functions (`tabpage_buflist`)
- verifying if a window is floating by checking `relative` field of the window config in the return result of `nvim_win_get_config`
- various tabline/statusline components (`%T`, `%X`, etc.) and using evaluation of Lua function as tabline/statusline (`%!`, `%{%..%}`, `v:lua`, `luaeval()`)

I highly recommend you to configure your own Tabline if you want to utilize the built-in tabs and have 3 hours to burn.

Here is a link to the [`tabline.lua` in my dotfiles repository](https://github.com/theopn/dotfiles/blob/main/nvim/.config/nvim/lua/ui/tabline.lua)

I am including the Lua code as well, call `tabline.setup()` in your `init.lua`.

```lua
--- *tabline.lua* Theovim Tabline
--- $ figlet -f tinker-joy theovim
---  o  o
---  |  |                  o
--- -o- O--o o-o o-o o   o   o-O-o
---  |  |  | |-' | |  \ /  | | | |
---  o  o  o o-o o-o   o   | o o o
---
--- Initialize tabline with:
--- - Clickable tab list
--- - Number of window iff there is more than one
--- - List of buffers in the current tab

Tabline = {}

local logo = vim.g.have_nerd_font and " 󰬛  " or "Theovim"


---Given a list of |window-ID|, filters out abnormal (i.e., float) windows.
---The mechanism relies on checking the `relative` field of the window config (|api-win_config|),
---as |api-floatwin| states "To check whether a window is floating, check whether `relative` option ... is non-empty."
---Example:
---
---```lua
---local currTabNonFloats = filterFloatWins(vim.api.nvim_tabpage_list_wins(0))
---```
---@param winids table List of window-iD
---@return table nonFloats List of window-ID of non-floating windows
local function filterFloatWins(winids)
  local nonFloats = {}
  for _, winid in pairs(winids) do
    if vim.api.nvim_win_get_config(winid).relative == "" then
      nonFloats[#nonFloats + 1] = winid
    end
  end
  return nonFloats
end


---Given a list of buffer numbers (e.g., return value of |tabpagebuflist()|),
---filters out duplicates AND unlisted buffers.
---Example:
---
---```lua
---local currTabListedBuf = filterUnlistedBuffers(vim.fn.tabpagebuflist())
---````
---@param bufnums table List of buffer numbers
---@return table listed List of listed buffers
local function filterUnlistedBuffers(bufnums)
  local listed = {}
  local hash = {}
  for _, buf in pairs(bufnums) do
    if vim.fn.buflisted(buf) == 1 and (not hash[buf]) then
      listed[#listed + 1] = buf
      hash[buf] = true
    end
  end
  return listed
end


---Format a string for Vim tabline based on tabs and buffers on the current tab
---@return string s Formatted string to be used as a Vim tabline
Tabline.build = function()
  local s = "%#TabLineFill#" .. logo

  -- ========== Left ==========
  -- List of tabs
  -- ==========================

  local currTabID = vim.api.nvim_get_current_tabpage()
  for _, tabID in pairs(vim.api.nvim_list_tabpages()) do
    -- This should not happen
    if not vim.api.nvim_tabpage_is_valid(tabID) then break end

    local tabNum = vim.api.nvim_tabpage_get_number(tabID)
    local winids = filterFloatWins(vim.api.nvim_tabpage_list_wins(tabID))

    -- Basic setup
    s = s .. ((tabID == currTabID) and "%#TabLineSel#" or "%#TabLine#") --> diff hl for active and inactive tabs
    s = s .. " "                                                        --> Left margin/separator
    s = s .. "%" .. tabNum .. "T"                                       --> make tab clickable (%nT)
    s = s .. tabNum .. " "                                              --> Tab index

    -- Add a number of windows in the tab, if applicable
    if #winids > 1 then
      if vim.g.have_nerd_font then
        s = s .. "[ " .. (#winids) .. "]"
      else
        s = s .. "[" .. (#winids) .. " WINS]"
      end
    end

    -- Make close button clickable ("%nX", %999X closes the current tab)
    s = s .. "%" .. tabNum .. "X"
    s = s .. (vim.g.have_nerd_font and "" or "X")

    -- Add a reset button (%T)
    s = s .. "%T"
    -- Add a BG highlight and left spacing
    s = s .. " %#TabLineFill# "
  end

  -- ========== Middle ==========
  -- Empty space
  -- ============================
  s = s .. "%="            --> spacer
  s = s .. "%#TabLineSel#" --> highlight

  -- ========== Right ==========
  -- List of LISTED buffers in the current tab
  -- ===========================
  local bufnums = filterUnlistedBuffers(vim.fn.tabpagebuflist(vim.api.nvim_tabpage_get_number(currTabID)))
  for _, bufnum in pairs(bufnums) do
    local bufname = vim.fn.fnamemodify(vim.fn.bufname(bufnum), ":t")

    -- Give a name to an empty buffer
    if bufname == "" then
      bufname = "[No Name:" .. bufnum .. "]"
    end

    -- Limit bufname to n character + 3 (accounting for "..." to be appended)
    local bufnameLimits = 18
    if string.len(bufname) > bufnameLimits + 3 then
      bufname = string.sub(bufname, 1, bufnameLimits) .. "..."
    end

    -- Add a flag to a modified buffer
    if vim.fn.getbufvar(bufnum, "&modified") == 1 then
      bufname = bufname .. "[+]"
    end

    -- Determine highlight to use
    local hl = "%#TabLine#"
    if vim.fn.bufnr() == bufnum then
      hl = "%#TabLineSel#"
    end

    -- Append formatted bufname
    s = s .. hl .. " " .. bufname .. " "
  end

  -- Add a truncation starting point: truncate buffer information first
  s = s .. "%<"
  return s
end


-- Set tabline. The Lua function called must be globally accessible
Tabline.setup = function()
  vim.go.tabline = "%!v:lua.Tabline.build()"
end

return Tabline
```


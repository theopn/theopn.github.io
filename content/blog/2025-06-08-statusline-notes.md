+++
date = '2025-06-08'
draft = false
title = 'Notes I took while configuring Neovim statusline, winbar, and tabline'
tags = ['Neovim', 'Lua']
+++

![ui-elements-showcase](/images/2025-06-08-statusline-notes/ui-elements-showcase.jpg)

Here are the notes I took while trying to learn & configure statusline, winbar, and tabline.
It was originally written in Vim helpdoc, so excuse me for the imperfect translation to markdown.
Hope you find this helpful!

My config for statusline, winbar, and tabline: https://github.com/theopn/dotfiles/tree/main/nvim/.config/nvim/lua/ui

## 1. Basics of \*line components

For every \*line update events, Neovim translates the \*line string, containing "printf style '%' items."
The list of these items are available in `|'statusline'|`.
If your \*line string only contains these items, you can pass it as a literal string, such as

```lua
vim.go.statusline = "FILE %t MODIFIED %m %= FT %Y LOC %l:%v"
```

## 2. Function Evaluation

If you want to pass a dynamic element, such as Git or LSP status of the buffer/window, you need to pass a function and evaluate.
There are two '%' items you can use to evaluate functions:

- `|stl-%!|`: evaluates the function based on the currently focused window and buffer
- `|stl-%{|`: evaluates the function based on the window the statusline belongs to

For example,

```lua
vim.go.winbar = "Buffer #: %{bufnr('%')}"
vim.go.tabline = "%!bufnr('%')"  --> %! has to be the only element
```

Winbar will display the buffer number for the respective windows, and tabline will display the buffer number of currently focused window.

`%{%...%}` is almost the same as `%{...}`, except it expands any '%' items.
For example,

```lua
vim.cmd[[
func! Stl_filename() abort
  return "%t"
endfunc
]]
vim.go.statusline = "Filename: %{Stl_filename()}"    --> %t
vim.go.statusline = "Filename: %{%Stl_filename()%}"  --> init.lua
```

Overall, I recommend using `%{%...%}` in most cases, because:
1. it is essentially a better version of `%{...}`
2. it can be placed within a string, unlike `%!...`
3. you typically want information such as LSP and Git to be window-specific

## 3. Lua function evaluation

To pass Lua function to be evaluated in \*line components, you have the following two options.

- `|luaeval()|` (also see: |lua-eval|): converts Lua values to Vimscript counterparts.
- `|v:lua|` (also see: |v:lua-call|): used to access Lua functions in Vimscript.

Both requires the Lua function to be global.

Either works fine, `v:lua` seems to be the choices of many \*line plugins, but I could not figure out how to use `v:lua` call with arguments.
Following example is configuring winbar with Devicons and LSP information.

```lua
Winbar = {}

Winbar.fileinfo = function()
  local has_devicons, devicons = pcall(require, "nvim-web-devicons")
  if not has_devicons then return "%t%m%r" end

  local bufname = vim.fn.bufname()
  local ext = vim.fn.fnamemodify(bufname, ":e")
  local icon = devicons.get_icon(bufname, ext, { default = true })
  return icon .. " %t%m%r"
end

Winbar.lsp_server = function()
  local clients = vim.lsp.get_clients({
    bufnr = vim.api.nvim_get_current_buf()
  })
  if rawequal(next(clients), nil) then return "" end

  local format = "LSP:"
  for _, client in ipairs(clients) do
    format = string.format("%s [%s]", format, client.name)
  end
  return format
end

Winbar.build = function()
  return table.concat({
    Winbar.fileinfo(),
    "%=",  --> spacer
    Winbar.lsp_server(),
})
end

Winbar.setup = function()
  -- Use one of the following
  --vim.go.winbar = "%{%luaeval('Winbar.build()')%}"
  vim.go.winbar = "%{%v:lua.Winbar.build()%}"
end

Winbar.setup()
```

## 5. Force-updating dynamic components

With the above Winbar example in your `init.lua`, try opening a buffer with LSP server(s) attached to it and stop the LSP clients with

```lua
:lua vim.lsp.stop_client(vim.lsp.get_clients())
```

You might find that the information in your winbar does not automatically update until you take an action (e.g., |CursorMoved|).
If you want to force an update in certain events, you need to create an autocmd that triggers |:redrawstatus| or |:redrawtabline|.

```lua
vim.api.nvim_create_autocmd({ "LspAttach", "LspDetach", "DiagnosticChanged" },
{
  group = vim.api.nvim_create_augroup("StatuslineUpdate", { clear = true }),
  pattern = "*",
  callback = vim.schedule_wrap(function()
  vim.cmd("redrawstatus")
  end),
  desc = "Update statusline/winbar"
})
```

Other use case might include `GitSignsUpdate` and `GitSignsChanged`.

## 6. Making separate \*line for active and inactive windows

This section is heavily inspired by Mini.Statusline (commit 83209bf).
When evaluating `|stl-%{|`, Neovim sets the current buffer/window to the window whose statusline/winbar is currently being drawn.
It also offers `|g:actual_curbuf|` and `|g:actual_curwin|` variables containing buffer/window number of the actual current buffer/window.
We can utilize these variables to check if the current window is active or inactive and draw separate statusline/winbar.

```lua
Winbar = {}

Winbar.build = function(isActive)
  return isActive and "active window" or "inactive window"
end

vim.go.winbar =
  "%{%(nvim_get_current_win()==#g:actual_curwin) ? luaeval('Winbar.build(true)') : luaeval('Winbar.build(false)')%}"
```

See also:
- `|setting-tabline|`: guide on configuring tabline with Vimscript


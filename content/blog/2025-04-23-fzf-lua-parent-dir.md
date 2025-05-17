+++
date = '2025-04-23'
draft = false
title = 'Custom fzf-lua Picker to Search Files in the Parent Directory'
tags = ["Neovim", "Lua"]
+++

## The Problem

I use [GNU Stow](https://www.gnu.org/software/stow/) to manage and deploy my [Dotfiles](https://github.com/theopn/dotfiles).
This means that there is a lot of nested directories in my Dotfiles repository:

```
dotfiles/
├── aerospace
│   └── .aerospace.toml
├── ...
├── nvim
│   └── .config
│       └── nvim
│           ├── after
│           │   └── ftplugin
│           │       └── ...
│           ├── doc
│           │   └── ...
│           ├── init.lua
│           ├── lsp
│           │   └── ...
│           ├── lua
│           │   ├── plugins
│           │   │   └── ...
│           │   ├── theovim
│           │   │   └── ...
│           │   └── ui
│           │       └── ...
│           └── spell
│               └── ...
├── README.md
└── ...
```

Often I am working inside the nested directories (e.g., `dotfiles/nvim/.config/nvim/lua/plugins`) and need to access files in the root directory (e.g., `dotfiles/README.md`).
It gets annoying to manually type `:cd ..` until I reach the desired parent directory.
When I was using Telescope, I used the [file-browser extension](https://github.com/nvim-telescope/telescope-file-browser.nvim) and navigated to the parent directories within the Telescope window, but it was not exactly the behavior I was hoping for.
So, during the migration to fzf-lua, I wrote a custom picker to select a parent directory to launch the file picker from. This is the single most used fzf-lua function in my use case.

## Solution

In `/nvim/lua/plugins/fzf-lua.lua`

```lua
return {
  "ibhagwan/fzf-lua",
  config = function()
  -- ... other config and keymaps ...

  vim.keymap.set("n", "<leader>s.", function()
    -- Fill the table with parent directories
    local dirs = {}
    for dir in vim.fs.parents(vim.uv.cwd()) do
      table.insert(dirs, dir)
    end

    -- Open a custom fzf to select a directory and launch fzf-files
    require("fzf-lua").fzf_exec(dirs, {
      prompt = "Parent Directories❯ ",
      actions = {
        ["default"] = function(selected)
          fzf.files({ cwd = selected[1] })
        end
      }
    })
  end, { desc = "[S]earch Parent Directories [..]" })

  -- ... other config and keymaps ...
  end
}
```

With `<leader>s.`, you are prompted with the list of parent directories to choose from.
Upon selection, the `fzf` file picker opens in the selected directory.

### Bonus: Receiving a path from the user input

Credit: https://www.reddit.com/r/neovim/comments/1k56ih4/custom_fzflua_function_to_select_a_parent/mogbz1b/?context=3

```lua
  vim.keymap.set("n", "<leader>s/", function()
    vim.ui.input({
      prompt = "Enter a directory: ",
      completion = "dir",
    }, function(input)
      if input then
        local dir = vim.fs.normalize(input)
        local stat = vim.uv.fs_stat(dir)
        if stat and stat.type == "directory" then
          require("fzf-lua").files({ cwd = dir })
        else
          print("Invalid directory!")
        end
      end
    end)
  end, { desc = "[S]earch the Given Directory" })
```


+++
date = '2025-05-23'
draft = false
title = 'My Neovim Config Over Time'
+++

[I started using Vim in 2021 and Neovim in the summer of 2022](blog/2022-08-10-my-linux-journey-so-far.md).
Over the past three years, Neovim remained as my main text editor and IDE, and a lot has changed in my config.

Following the [Nordfox migration post](blog/2025-05-18-nordfox.md), I thought it would be interesting to see how my Neovim changed over the past years.

## 2022 - early 2023: playground opened (Onedark era)

{{< figure src="/images/2025-05-23-neovim-over-time/2023-01-21-onedark.jpg" position="center" caption="2023-01-21, editing C++ with `clangd`"  >}}

{{< figure src="/images/2025-05-23-neovim-over-time/2023-02-15-onedark.jpg" position="center" caption="2023-02-15, editing the custom command to invoke `curl` [`wttr.in`](https://github.com/chubin/wttr.in) and display the result in a small floating window" >}}

This was when I started to discover the Neovim plugin scene and showed an early sign of addiction to Neovim.
At the beginning of 2023, I made my config available in a separate repository called [Theovim](https://github.com/theopn/theovim) for my friend Jonathan who wanted to use my config in the school SSH server.
And I spent much of that semester adding plugins and implementing features to prove him that it is better than VS Code.

Overall, it looks bad compared to the next two stages, but it certainly had a charm.
You can also take a glimpse at the hideous Tmux config I used to rock.

Plugins shown:

- [Onedark.nvim](https://github.com/navarasu/onedark.nvim)
- [Dashboard.nvim](https://github.com/nvimdev/dashboard-nvim)
- [Barbar.nvim](https://github.com/romgrk/barbar.nvim)
- [Telescope.nvim](https://github.com/nvim-telescope/telescope.nvim)
- [Lspsaga.nvim](https://github.com/nvimdev/lspsaga.nvim)
- [Lualine.nvim](https://github.com/nvim-lualine/lualine.nvim) with [evil_lualine theme](https://github.com/nvim-lualine/lualine.nvim/blob/master/examples/evil_lualine.lua)
- Custom weather command:
    {{< details summary="[best revision](https://github.com/theopn/theovim/blob/5e0733a5785bde1f8b62e3722ae1cede84264cf8/src/lua/theovim.lua#L67) I could find." >}}
    ```
    local function weather_popup(location)
    -- window size and pos
    local win_height = math.ceil(vim.o.lines * 0.6 - 20)
    local win_width = math.ceil(vim.o.columns * 0.3 - 15)
    local x_pos = 1
    local y_pos = vim.o.columns - win_width

    local win_opts = {
        style = "minimal",
        relative = "editor",
        width = win_width,
        height = win_height,
        row = x_pos,
        col = y_pos,
        border = "single",
    }

    local buf = vim.api.nvim_create_buf(false, true)
    local win = vim.api.nvim_open_win(buf, true, win_opts)

    vim.api.nvim_buf_set_option(buf, "bufhidden", "wipe")
    vim.api.nvim_win_set_option(win, "winblend", 0)

    local keymaps_opts = { silent = true, buffer = buf }
    vim.keymap.set('n', "q", "<C-w>q", keymaps_opts)
    vim.keymap.set('n', "<ESC>", function() vim.api.nvim_win_close(win, true) end, keymaps_opts)

    local weather_command = "curl 'https://wttr.in/?0T' > /dev/null"
    if location ~= nil then
        weather_command = string.format("curl https://wttr.in/%s'?'0T", location.args)
    end
    vim.fn.termopen(weather_command)
    end

    -- function() my_func() end: inline function calling my_func(); my_func(): return val; my_func: function itself
    -- You need to pass function itself to use the usercommand arguments
    vim.api.nvim_create_user_command("Weather", weather_popup, { nargs = '?' }) --> ?: 0 or 1, *: > 0, +: > 1 args
    ```
    {{< /details >}}
    Surprised that it still works in 0.11.1, but `api.nvim_buf_set_option`, `api.nvim_win_set_option`, and `fn.termopen` are deprecated in 0.10, 0.10, and 0.11, respectively.

## Mid 2023 - early 2025: quirky and different? (Tokyonight era)

{{< figure src="/images/2025-05-23-neovim-over-time/2023-10-04-tokyonight.jpg" position="center" caption="2023-10-04, screenshot for r/unixporn that I ended up not posting"  >}}

{{< figure src="/images/2025-05-23-neovim-over-time/2023-07-18-tokyonight-comparison.jpg" position="center" caption="2023-07-18, comparison to my old config"  >}}

{{< figure src="https://raw.githubusercontent.com/theopn/theovim/refs/heads/main/assets/theovim-banner.jpg" position="center" caption="2023-10-11, the appearance stayed pretty much the same until 2025" >}}

This was when I tried to be quirky and different by writing custom UI components instead of using plugins.
And yes, I still do that, but the design language was all over the place, and the internals of the UI components were poorly written and not very maintainable.
Still, I learned a lot about Lua during this period.

After all this effort to differentiate my config, I chose to use the Tokyonight colorscheme, which made my config look like any other popular Neovim distro.
But damn, that colorscheme is sexy (`tokyonight-night`, btw).

Plugins shown:

- [Tokyonight.nvim](https://github.com/folke/tokyonight.nvim)
- Custom floating notepad
    {{< details summary = "[best revision](https://github.com/theopn/theovim/blob/dacc6938ac36f2bdbcf0e85ce5f6a5f28225f1f8/lua/ui/notepad.lua)" >}}
    ```
    -- Otherwise, you will have multiple scratch buffer with multiple notepad status
    M.notepad_loaded = false
    M.notepad_buf, M.notepad_win = nil, nil

    --[[ launch_notepad()
    -- Checkes if notepad window is active first
    -- then checkes if notepad buffer has been initialized. If so, reuse the buffer, else, create a new scratch buffer
    -- If window is not active, display a small floating window with the scratch buffer
    --
    -- @requires M.notepad_loaded, M.notepad_buf, M.notepad_win variables in util (this) module
    --]]
    function M.toggle_notepad()
    if not M.notepad_loaded or not vim.api.nvim_win_is_valid(M.notepad_win) then
        if not M.notepad_buf or not vim.api.nvim_buf_is_valid(M.notepad_buf) then
        -- Create a buffer if it none existed
        M.notepad_buf = vim.api.nvim_create_buf(false, true)
        vim.api.nvim_buf_set_option(M.notepad_buf, "bufhidden", "hide")
        vim.api.nvim_buf_set_option(M.notepad_buf, "filetype", "markdown")
        vim.api.nvim_buf_set_lines(M.notepad_buf, 0, 1, false,
            { "# Theovim Notepad",
            "",
            "> Notepad clears when the current Neovim session closes",
            })
        end
        -- Create a window
        M.notepad_win = vim.api.nvim_open_win(M.notepad_buf, true, {
        border = "rounded",
        relative = "editor",
        style = "minimal",
        height = math.ceil(vim.o.lines * 0.5),
        width = math.ceil(vim.o.columns * 0.5),
        row = 1,                                                 --> Top of the window
        col = math.ceil(vim.o.columns * 0.5),                    --> Far right; should add up to 1 with win_width
        })
        vim.api.nvim_win_set_option(M.notepad_win, "winblend", 30) --> Semi transparent buffer

        -- Keymaps
        local keymaps_opts = { silent = true, buffer = M.notepad_buf }
        vim.keymap.set('n', "<ESC>", function() M.toggle_notepad() end, keymaps_opts)
        vim.keymap.set('n', "q", function() M.toggle_notepad() end, keymaps_opts)
    else
        vim.api.nvim_win_hide(M.notepad_win)
    end
    M.notepad_loaded = not M.notepad_loaded
    end

    --[[ setup()
    -- Creates an autocommand to launch the notepad
    --]]
    M.setup = function()
    vim.api.nvim_create_user_command("Notepad", M.toggle_notepad, { nargs = 0 })
    end

    return M
    ```
    {{< /details >}}
    Those function comments are horrendous.
- Custom Dashboard
    {{< details summary = "[best revision](https://github.com/theopn/theovim/blob/d2f50d6078f393ab60fcdd07c252edcdccd6342d/lua/ui/dashboard.lua)" >}}
    ```
    --- *dashboard.lua* Startup Dashboard
    ---
    --- $ figlet -f mini theovim
    --- _|_|_  _  _   o._ _
    ---  |_| |(/_(_)\/|| | |
    ---
    --- Provide a framework to open a dashboard on the Neovim startup when there is no buffer opened (only the empty buf)
    ---
    --- Suggested dependencies:
    --- - Nerd font
    --- - MiniStarter* highlights from your choice of colorscheme
    ---

    local Dashboard = {}

    -- ASCII arts of my chunky cat Oliver
    -- Mostly from: https://www.asciiart.eu/animals/cats
    -- Make sure the length of each string is consistent! Hard coding much...
    local olivers = {
    {
        [[          \/   \/              ]],
        [[          |\__/,|     _        ]],
        [[        _.|o o  |_   ) )       ]],
        [[       -(((---(((--------      ]]
    },
    -- This one is by Jonathan
    {
        [[       \/ \/                   ]],
        [[       /\_/\ _______           ]],
        [[      = o_o =  _ _  \     _    ]],
        [[      (__^__)   __(  \.__) )   ]],
        [[   (@)<_____>__(_____)____/    ]],
        [[     ♡ ~~ ♡ OLIVER ♡ ~~ ♡      ]],
    },
    {
        [[        \/   \/                ]],
        [[        |\__/,|        _       ]],
        [[        |_ _  |.-----.) )      ]],
        [[        ( T   ))        )      ]],
        [[       (((^_(((/___(((_/       ]]
    },
    {
        [[          \/       \/          ]],
        [[          /\_______/\          ]],
        [[         /   o   o   \         ]],
        [[        (  ==  ^  ==  )        ]],
        [[         )           (         ]],
        [[        (             )        ]],
        [[        ( (  )   (  ) )        ]],
        [[       (__(__)___(__)__)       ]],
    },
    {
        [[                          _    ]],
        [[         |\      _-``---,) )   ]],
        [[   ZZZzz /,`.-'`'    -.   /    ]],
        [[        |,4-  ) )-,_. ,\ (     ]],
        [[       '---''(_/--'  `-'\_)    ]]
    },
    }
    local header = olivers[math.random(#olivers)]

    local logo = {
    [[ ___                    ]],
    [[  | |_  _  _     o __   ]],
    [[  | | |(/_(_)\_/ | |||  ]],
    "",
    os.date("[ ━━%m-%d━━ ❖ ━━%H:%M━━ ]"),
    }
    -- Hard code button spacing here
    local buttons = {
    { "󰥨  Find File      SPC f f", cmd = function() require("telescope.builtin").find_files() end, },
    { "󰈙  Recent Files   SPC   ?", cmd = function() require("telescope.builtin").oldfiles() end, },
    { "  Exit Theovim        ZZ", cmd = "quit", },
    }

    -- Calculating max width and make an empty length of the max width
    local max_width = #header[1]
    local empty_line = string.rep(" ", max_width)
    -- Add empty line paddings for ASCII
    table.insert(header, 1, empty_line)
    header[#header + 1] = empty_line
    logo[#logo + 1] = empty_line
    -- max height = empty line + #header + #logo + #buttons + empty lines after each button + empty line + 1 safety net
    local max_height = 1 + #header + #logo + (#buttons * 2) + 1 + 1


    --- render()
    --- If the buffer does not have a name, replace the buffer with the formated Dashboard contents
    --- Inspired by : https://github.com/chadcat7/kodo/blob/main/lua/ui/dash/init.lua
    ---               https://github.com/NvChad/ui/blob/dev/lua/nvchad_ui/nvdash/init.lua
    ---
    local render = function()
    local win_width = vim.api.nvim_win_get_width(0)
    local win_height = vim.api.nvim_win_get_height(0)

    -----------------------------------
    -- Condition check --

    -- Expands the current file name and see if it's empty
    if vim.api.nvim_buf_get_name(0) ~= "" then return end --> or use vim.fn.expand("%")

    -- Check if window is too small to launch
    if win_height < max_height then
        vim.notify_once("Dashboard: window size is too small :(") --> use notify_once to stop notification spamming
        return
    end

    -- The default empty buffer will go away when the new Dashboard buffer replaces it
    vim.api.nvim_buf_set_option(0, "bufhidden", "wipe")

    -- Create a new buffer and replace the old one
    local buf = vim.api.nvim_create_buf(false, true) --> listed false, scratchbuffer true
    vim.api.nvim_win_set_buf(0, buf)
    vim.opt_local.filetype       = "TheovimDashboard"
    vim.opt_local.buflisted      = false
    vim.opt_local.list           = false
    vim.opt_local.wrap           = true
    vim.opt_local.relativenumber = false
    vim.opt_local.number         = false
    vim.opt_local.cursorline     = false
    vim.opt_local.cursorcolumn   = false
    vim.opt_local.colorcolumn    = "0"

    vim.opt_local.modifiable     = true

    -----------------------------------
    -- Init the DB contents--

    -- Table for the contents
    local dashboard              = {}
    -- add padding to the header
    local add_padding            = function(str)
        local pad = (win_width - vim.fn.strwidth(str)) / 2
        return string.rep(" ", math.floor(pad)) .. str
    end

    -- Inserting contents to the DB table
    for _, val in ipairs(header) do
        table.insert(dashboard, add_padding(val))
    end
    for _, val in ipairs(logo) do
        table.insert(dashboard, add_padding(val))
    end
    for _, val in ipairs(buttons) do
        table.insert(dashboard, add_padding(val[1]))
        table.insert(dashboard, empty_line)
    end
    table.remove(dashboard, #dashboard) --> Remove the extra new line padding from the buttons

    --------------------
    -- Setting the dashboard --
    local result = {}
    for i = 1, win_height do
        result[i] = ""
    end

    local hdr_start_idx_save = math.floor((win_height / 2) - (#dashboard / 2) - 1)
    local hdr_start_idx = math.floor((win_height / 2) - (#dashboard / 2) - 1)

    -- adding the dashboard
    for _, val in ipairs(dashboard) do
        result[hdr_start_idx_save] = val
        hdr_start_idx_save = hdr_start_idx_save + 1
    end

    -- setting the dasboard
    vim.api.nvim_buf_set_lines(buf, 0, -1, false, result)

    -- set the cursor: 15 is is my best guess on where the first char of the button would be. If too narrow, use 0
    local cursor_column_idx = (win_width > max_width) and (math.floor(win_width / 2) - 15) or (0)
    vim.api.nvim_win_set_cursor(0, { hdr_start_idx + #header + #logo, cursor_column_idx })

    ---------------------------
    -- Setting highlights --

    for i = hdr_start_idx, hdr_start_idx + #header - 2 do                       --> Ignore last two empty lines
        vim.api.nvim_buf_add_highlight(buf, -1, "MiniStarterFooter", i, 0, -1)    --> -1 for no namespace
    end
    for i = hdr_start_idx + #header - 2, hdr_start_idx + #header + #logo - 2 do --> Again, -2 because of empty lines
        vim.api.nvim_buf_add_highlight(buf, -1, "MiniStarterHeader", i, 0, -1)
    end
    for i = hdr_start_idx + #header + #logo - 2, hdr_start_idx + #header + #logo - 2 + (#buttons * 2) do --> Reach ends
        vim.api.nvim_buf_add_highlight(buf, -1, "MiniStarterItemBullet", i, 0, -1)
    end

    -----------------------
    -- Keybindings --

    local curr_btn_line = hdr_start_idx + #header + #logo + 2 --> Line number where the first button is located
    local btns_line_nums = {}

    -- Make a table of line numbers where buttons exist
    for _, _ in ipairs(buttons) do
        table.insert(btns_line_nums, curr_btn_line - 2)
        curr_btn_line = curr_btn_line + 2
    end

    -- Setting hjkl and arrow keys movement
    local up_mvmt = function()
        local curr = vim.fn.line(".") -- Current line
        -- Check if the current line number - 2 is button or move to the last element
        local target_line = vim.tbl_contains(btns_line_nums, curr - 2) and curr - 2 or btns_line_nums[#btns_line_nums]
        vim.api.nvim_win_set_cursor(0, { target_line, cursor_column_idx })
    end
    local down_mvmt = function()
        local curr = vim.fn.line(".") -- Current line
        -- Check if the current line number + 2 is button or move to the first element
        local target_line = vim.tbl_contains(btns_line_nums, curr + 2) and curr + 2 or btns_line_nums[1]
        vim.api.nvim_win_set_cursor(0, { target_line, cursor_column_idx })
    end
    vim.keymap.set("n", "h", "", { buffer = true })
    vim.keymap.set("n", "j", down_mvmt, { buffer = true })
    vim.keymap.set("n", "k", up_mvmt, { buffer = true })
    vim.keymap.set("n", "l", "", { buffer = true })
    vim.keymap.set("n", "<LEFT>", "", { buffer = true })
    vim.keymap.set("n", "<DOWN>", down_mvmt, { buffer = true })
    vim.keymap.set("n", "<UP>", up_mvmt, { buffer = true })
    vim.keymap.set("n", "<RIGHT>", "", { buffer = true })

    -- Setting return key movement
    vim.keymap.set("n", "<CR>", function()
        for i, v in ipairs(btns_line_nums) do
        if v == vim.fn.line(".") then
            local action = buttons[i].cmd
            if type(action) == "string" then
            vim.cmd(action)
            elseif type(action) == "function" then
            action()
            end
        end
        end
    end, { buffer = true })

    -- The end --
    vim.opt_local.modifiable = false
    end

    --- opener()
    --- Wrap Dashboard.render() with schedule().
    --- schedule_wrap({cb}) "Defers callback `cb` until the Nvim API is safe to call," and schedule() calls wrapped func
    --- Let's say the user is resizing the terminal window. Without deferring, render() will jump ahead and start rendering,
    --- which casues breakage in calculations and rendering. This allows render() to wait until Neovim says it's safe
    ---
    Dashboard.opener = function()
    vim.schedule(render)
    end

    --- setup()
    --- Call opener in the startup and make autocmd for resizing
    ---
    Dashboard.setup = function()
    Dashboard.opener()

    vim.api.nvim_create_autocmd("VimResized", {
        callback = function()
        if vim.bo.filetype == "TheovimDashboard" then
            vim.opt_local.modifiable = true
            vim.api.nvim_buf_set_lines(0, 0, -1, false, { "" })
            Dashboard.opener()
        end
        end,
    })
    end

    return Dashboard

    ```
    {{</ details >}}
    It was the pinnacle of "I need no plugin for this!", but it was never not buggy, it was not functional enough for me to keep around.
- Custom Statusline and Winbar 1.0 ([best revision](https://github.com/theopn/theovim/tree/4561cbedd9db5406639d7118bea97357e26ece22/lua/ui))
- [Bufferline.nvim](https://github.com/akinsho/bufferline.nvim) -> custom Tabline 1.0 ([best revision](https://github.com/theopn/theovim/blob/96c6c1e417f344b272ead8aa7a7372b34d2d59c0/lua/ui/tabline.lua))

## Early 2025 - ??: hopefully productivity focused (Nordfox era)

{{< figure src="/images/2025-05-23-neovim-over-time/2025-05-15-nordfox.jpg" position="center" caption="2025-05-15, in macOS, configuring tabline & winbar, showcasing org-agenda"  >}}

Over time, I started to feel that my config was not only bloated with so many unnecessary plugins and custom functions, but also because I wrote them as I was learning Lua and Neovim API, many components were poorly written and documented.
The final nail in the coffin was the [0.11 update](https://neovim.io/doc/user/news-0.11.html), which brought many native features worth using.

As I was migrating from `nvim-lspconfig` to the native `vim.lsp.config` functions, I refactored the configuration, cutting out all the unmaintained and unused code.
I chose Nordfox colorscheme as it was easy on my eyes, and I wrote UI components from scratch with maintainability and usability in mind.
I also wrote personal help documentation ([`:h theovim`](https://github.com/theopn/dotfiles/blob/main/nvim/.config/nvim/doc/theovim.txt)), documenting tips and tricks for Vim features and plugins I have learned over time.
This helped me cut out code and comments that I had for the sake of future reference.

- Nordfox colorscheme in [nightfox.nvim](https://github.com/EdenEast/nightfox.nvim)
- Custom Tabline 2.0 ([post about it](blog/2025-05-13-tabline-in-lua))
- Custom Winbar 2.0
- Custom Statusline 2.0, with highlight groups borrowed from [mini.statusline](https://github.com/echasnovski/mini.statusline)
- [nvim-org-mode](https://github.com/nvim-orgmode/orgmode)

## Conclusion

I love Neovim, and I may have a problem.


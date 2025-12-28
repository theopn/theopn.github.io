+++
date = '2025-12-28T17:28:16-05:00'
draft = false
title = 'A sane starting point for writing tab_bar.py for Kitty'
tags = ['terminal']
+++

Writing a custom Kitty tab bar can be tedious, as you have to understand how the default `tab_bar.py` works, as well as the surrounding modules like [`boss.py`](https://github.com/kovidgoyal/kitty/blob/master/kitty/boss.py) and [`tabs.py`](https://github.com/kovidgoyal/kitty/blob/master/kitty/tabs.py).

I have been experimenting with Kitty as a potential replacement for Wezterm, and I wanted the following items in the tab bar:

1. Tab index
2. Current tab layout
3. Current working directory
4. Current process

I did not get too far with `kitty.conf`, as listing all those items in `tab_title_template` rendered each tab too long; I needed to use Python.

But even though [this](https://github.com/kovidgoyal/kitty/discussions/4447) discussion page exists, I still found writing the `draw_tab` function from scratch a little overwhelming, especially because I wanted things like mouse clicking to work as well.
After reading the discussion page and many dotfiles, I found a good starting point: modifying the `draw_data` and simply calling one of the built-in functions.

![my-kitty-tab-bar](/images/2025-12-28-kitty-tab-bar/kitty-tab-bar.jpg)

`tab_bar.py`

```py
from kitty.fast_data_types import Screen
from kitty.tab_bar import (DrawData,
                           TabBarData,
                           ExtraData,
                           draw_tab_with_powerline,
                           )


def draw_tab(
    draw_data: DrawData, screen: Screen, tab: TabBarData,
    before: int, max_title_length: int, index: int, is_last: bool,
    extra_data: ExtraData,
) -> int:
    """
    Kitty's DrawData is defined here:
    https://github.com/kovidgoyal/kitty/blob/master/kitty/tab_bar.py#L58

    Strat is to edit title_template and active_title_template
    and call the original draw_tab_with_* function.
    """

    layout_icon = "?"
    if tab.layout_name == "tall":
        layout_icon = " "
    elif tab.layout_name == "vertical":
        layout_icon = " "
    elif tab.layout_name == "horizontal":
        layout_icon = " "
    elif tab.layout_name == "stack":
        layout_icon = " "

    new_draw_data = draw_data._replace(
        title_template="{fmt.fg.red}{bell_symbol}{activity_symbol}{fmt.fg.tab}"
        + "{sup.index}󰘳  "
        + layout_icon
        + "{sub.num_windows}"
        + " "
        + "󰉋 {tab.active_wd.rsplit('/', 1)[-1] or '/'}"
        + " "
        + " {tab.active_exe}"
        + " {title}"

        # active_title_template inherits title_template if nil
    )

    return draw_tab_with_powerline(
        new_draw_data, screen, tab,
        before, max_title_length, index, is_last,
        extra_data)
```

`kitty.conf`:

```
# ...

tab_bar_style custom
# This is still used since i call `draw_tab_with_powerline` in tab_bar.py
tab_powerline_style round
tab_bar_align center
tab_bar_min_tabs 1
tab_title_max_length 0

# ...
```

A future optimization is to use `tab.title` instead of calling `active_wd`/`active_exe`, since apparently those calls are apparently [expensive](https://sw.kovidgoyal.net/kitty/conf/#opt-kitty.tab_title_template).
I have been studying [TabAccessor](https://github.com/kovidgoyal/kitty/blob/v0.45.0/kitty/tab_bar.py#L203) and the [tab title functions](https://github.com/kovidgoyal/kitty/blob/v0.45.0/kitty/tabs.py#L389) to find a way to (a) check if the tab name has been manually changed (b) parse current executable and CWD from the title instead of system calls.

So... yeah, I guess you do have to learn how Kitty works.


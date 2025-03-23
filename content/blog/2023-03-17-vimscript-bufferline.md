+++
title = "I made a 'bufferline' with 40 lines of Vimscript!"
date = "2023-03-17"
description = "Copypasta of a popular r/vim post I made"
tags = ["Vim", "Vimscript"]
+++

![vim-bufline-demo.gif](/images/2023-03-17-vimscript-bufferline/bufline-demo.gif)

I wrote a simple Vimscript function to implement the "bufferline" that many Neovim plugins try to emulate.
It goes over all the listed buffers and displays them in the TabLine.
I also put the tab (as the tab number) on the right side of the TabLine so that you can use the tab feature if you want to.
Make sure to set showtabline=2 if you want the TabLine to be always displayed since this function is overriding the default TabLine.
Feel free to modify it as you want and put it in your Vimrc!

Credit: Structure of the code taken from [this Stack Overflow thread](https://stackoverflow.com/questions/33710069/how-to-write-tabline-function-in-vim)

```vim
set showtabline=2

function! SpawnBufferLine()
  let s = ' hello r/vim | '

  " Get the list of buffers. Use bufexists() to include hidden buffers
  let bufferNums = filter(range(1, bufnr('$')), 'buflisted(v:val)')
  " Making a buffer list on the left side
  for i in bufferNums
    " Highlight with yellow if it's the current buffer
    let s .= (i == bufnr()) ? ('%#TabLineSel#') : ('%#TabLine#')
    let s .= i . ' '  " Append the buffer number
    if bufname(i) == ''
      let s .= '[NEW]'  " Give a name to a new buffer
    endif
    if getbufvar(i, "&modifiable")
      let s .= fnamemodify(bufname(i), ':t')  " Append the file name
      " let s .= pathshorten(bufname(i))  " Use this if you want a trimmed path
      " If the buffer is modified, add + and separator. Else, add separator
      let s .= (getbufvar(i, "&modified")) ? (' [+] | ') : (' | ')
    else
      let s .= fnamemodify(bufname(i), ':t') . ' [RO] | '  " Add read only flag
    endif
  endfor
  let s .= '%#TabLineFill#%T'  " Reset highlight

  let s .= '%=' " Spacer

  " Making a tab list on the right side
  for i in range(1, tabpagenr('$'))  " Loop through the number of tabs
    " Highlight with yellow if it's the current tab
    let s .= (i == tabpagenr()) ? ('%#TabLineSel#') : ('%#TabLine#')
    let s .= '%' . i . 'T '  " set the tab page number (for mouse clicks)
    let s .= i . ''          " set page number string
  endfor
  let s .= '%#TabLineFill#%T'  " Reset highlight

  " Close button on the right if there are multiple tabs
  if tabpagenr('$') > 1
    let s .= '%999X X'
  endif
  return s
endfunction

set tabline=%!SpawnBufferLine()  " Assign the tabline
```


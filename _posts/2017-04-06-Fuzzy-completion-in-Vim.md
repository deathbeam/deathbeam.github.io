---
layout: post
title: Fuzzy completion in Vim
author: Tomas Slusny
tags:
  - vim
  - fzf
---

I was not active for more than a year again, but hopefully this time it will be
finally interesting.

Recently, I really fell in love with [FZF](https://github.com/junegunn/fzf). FZF
is really awesome fuzzy finder for command-line. I started to use to navigate
basically everything, starting with searching through `zsh` history and ending
with controlling my music player with it. FZF looks like this (taken from
official repository, because I am too lazy to make pretty gifs):

![](https://raw.github.com/junegunn/i/master/fzf.gif)

And in between, there is Vim. I am
using FZF and fzf.vim plugins for Vim to:

  * Navigate buffer
  * Search in tags
  * Open files
  * Search in vim documentation
  * Search through all text files in my current directory (not using this very
    much, but it is neat how fast it is with `ripgrep` set as FZF grepping program)

And now, I am also using it for completion instead of default Vim completion
popup. Awesome, right? I agree that it is maybe a bit overkill and maybe I will
switch back to completion popup, but right now I am super happy with it (and
spent too many hours making it work to just dump it) so I decided to write
shitty blog post with my bad english about how awesome it is and how awesome AM
I.


#### Using FZF with Vim omni completion

So, here is the custom completion function:


```vim
function! FzfCompletionPop(findstart, base)
  let l:res = function(&omnifunc)(a:findstart, a:base)

  if a:findstart
    return l:res
  endif

  return fzf#run({ 'source': l:res, 'down': '~40%', 'options': printf('--query "%s" +s', a:base) })
endfunction
```

This function first gathers results from calling Vim omnifunc, that is
completion function that plugins use to provide semantic completion for Vim. We
will talk about how to use this function later.

#### Using FZF with completor.vim

Here, we need to use a bit different implementation of last completion function:

```vim
function! FzfCompletionPop(findstart, base)
  let l:res = completor#completefunc(a:findstart, a:base)

  if a:findstart
    return l:res
  endif

  let l:words = []

  for word in l:res.words
    call add(l:words, word['word'] . ' ' . word['menu'])
  endfor

  let l:result = fzf#run({ 'source': l:words, 'down': '~40%', 'options': printf('--query "%s" +s', a:base) })

  if empty(l:result)
    return [ a:base ]
  endif

  return [ split(l:result[0])[0] ]
endfunction
```

This one is a bit more complicated. It first calls internal `completor.vim`
completion function, but as this one is returning a bit more complicated results
than omni completion function (or maybe not, and correct way to use omnifunc in
previous example will be to just replace call to `completor#completefunc` with
call to `omnifunc`) so we need to convert them to correct format for FZF (and also with
documentation) and then revert back to simple format and return it. Okay, now we
have our completion function, so we can use it.

### Use custom FZF completion function in Vim

We can set our complete function as `completefunc`:

```vim
set completefunc=FzfCompletionPop
set completeopt=menu
```

`completeopt` modification is needed to tell vim to automatically match
completion if there is only one result (and our custom completion function will
return only one result). This change will make FZF with completion trigger after
pressing Control-X and then Control-U. But, this option will not work with
`completor.vim`, because it is setting it's own `completefunc`, so we need to
make small hack.
So second option is to create your own trigger (as I did in my own vimrc) and
not mess with `completefunc` and `completeopt` and leave them free for other plugins
(like `completor.vim`):


```vim
function! FzfCompletionTrigger()
  setlocal completefunc=FzfCompletionPop
  setlocal completeopt=menu
  call feedkeys("\<c-x>\<c-u>", 'n')
endfunction
imap <c-x><c-j> <c-o>:call FzfCompletionTrigger()<cr>
```

This will add new trigger, Control-X and then Control-J that will trigger FZF
completion.
This is nice and stuff, but triggerring completion with Control-X and
Control-J... **triggers me**. But auto popups too, so let's make completion to
be triggered by **TAB**:

```vim
function! Smart_TabComplete()
  let col = col('.') - 1
  if !col || getline('.')[col - 1] !~ '\k'
    call feedkeys("\<tab>", 'n')
    return
  endif
  call feedkeys("\<c-x>\<c-j>")
endfunction
inoremap <silent> <tab> <c-o>:call Smart_TabComplete()<cr>
```

If you chose to use `completefunc` option instead of custom trigger with
Control-X and then Control-J, then just replace `<c-j>` with `<c-u>` in above
snippet.

Now, place whatever you want from here to your `.vimrc`, and we are done. Yes,
we are done, do no talk to my father of father of my father of my father ever
again. Maybe I will post also screenshot later, but now, I won't, but it is
great, trust me.

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

So, here is how final result will look like (that small thing at bottom right):
![](http://i.imgur.com/tQbIFW7.png)

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

#### Requirements

* [FZF](https://github.com/junegunn/fzf) (of course)
* [completor.vim](https://github.com/maralla/completor.vim) (this one is
optional, and required only for context-sensitive completion)

#### Let's get started

So, here is the custom completion function:


```vim
let g:fuzzyfunc = &omnifunc

function! FuzzyCompleteFunc(findstart, base)
  let Func = function(get(g:, 'fuzzyfunc', &omnifunc))
  let results = Func(a:findstart, a:base)

  if a:findstart
    return results
  endif

  if type(results) == type({}) && has_key(results, 'words')
    let l:words = []
    for result in results.words
      call add(words, result.word . ' ' . result.menu)
    endfor
  elseif len(results)
    let l:words = results
  endif

  if len(l:words)
    let result = fzf#run({ 'source': l:words, 'down': '~40%', 'options': printf('--query "%s" +s', a:base) })

    if empty(result)
      return [ a:base ]
    endif

    return [ split(result[0])[0] ]
  else
    return [ a:base ]
  endif
endfunction
```

This function first gathers results from calling defined `g:fuzzyfunc` (that we
set to vim `omnifunc`) and then sends it to `fzf#run` function, that is function
from `fzf` plugin for vim and this function will open small FZF split at bottom
what will be used as filter for our results. Then it returns single filtered
result from this function.

Now, to actually use this, we can set our complete function as `completefunc`:

```vim
set completefunc=FuzzyCompleteFunc
set completeopt=menu
```

`completeopt` modification is needed to tell vim to automatically match
completion if there is only one result (and our custom completion function will
return only one result). This change will make FZF with completion trigger after
pressing Control-X and then Control-U. But, this option will not work with
`completor.vim` (will talk about this later), because it is setting it's own
`completefunc`, so we need to make small hack.
So second (and better) option is to create your own trigger (as I did in my own vimrc) and
not mess with `completefunc` and `completeopt` and leave them free for other plugins
(like `completor.vim`):


```vim
function! FuzzyFuncTrigger()
  setlocal completefunc=FuzzyCompleteFunc
  setlocal completeopt=menu
  call feedkeys("\<c-x>\<c-u>", 'n')
endfunction

imap <c-x><c-j> <c-o>:call FuzzyFuncTrigger()<cr>
```

This will add new trigger, Control-X and then Control-J that will trigger FZF
completion.
This is nice and stuff, but triggerring completion with Control-X and
Control-J... **triggers me**. But auto popups too, so let's make completion to
be triggered by **TAB**:

```vim
function! TabComplete()
  let col = col('.') - 1

  if !col || getline('.')[col - 1] !~# '\k'
    call feedkeys("\<tab>", 'n')
    return
  endif

  call feedkeys("\<c-x>\<c-j>")
endfunction

inoremap <silent> <tab> <c-o>:call TabComplete()<cr>
```

If you chose to use `completefunc` option instead of custom trigger with
Control-X and then Control-J, then just replace `<c-j>` with `<c-u>` in above
snippet. And now, let's try to integrate this with `completor.vim`.

#### Using FZF with completor.vim

So first, you are maybe asking what is
[completor.vim](https://github.com/maralla/completor.vim)? In short it is some
sort of async completion thingy for Vim. Faster completion, no lags and stuff.
But this is not why I chose it. I chose it because it have awesome completion
function that I can wrap in FZF. This completion function is deciding if it will
run file completion, buffer completion, it's internal async completion for
supported languages or omnicompletion. So, that is why. So, just go and get it.
Or just keep using omnifunc.

So, thanks to previous awesome impletentation of `FuzzyCompleteFunc` all we need
to do is adjust `g:fuzzyfunc`:

```vim
let g:fuzzyfunc = 'completor#completefunc'
```
`completor#completefunc` is internal completion function of `completor.vim` that
is returning results in same format as `omnifunc`, but they are context
sensitive (as I wrote earlier).

#### Final words

Now, place whatever you want from here to your `.vimrc`, and we are done. Yes,
we are done, really.

I will try to write more articles about some cool random stuff I am doing with
Vim and Tmux (if I will have time) so stay tuned (or not, whatever).

Also, here are my
**[dotfiles](https://github.com/deathbeam/dotfiles)** that contains everything
mentioned in this post and a lot more and here is my
**[.vimrc](https://github.com/deathbeam/dotfiles/blob/master/vim/.vimrc)**

**EDIT**: Updated post with better completion function and with images (woohooo)

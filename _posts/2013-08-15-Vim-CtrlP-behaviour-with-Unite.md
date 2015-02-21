---
layout: post
title: Vim CtrlP behaviour with Unite
tags : [vim]
---


When I first found out about the popular [CtrlP](https://github.com/kien/ctrlp.vim) plug-in, I found myself using it constantly. Being able to switch between the document I had in my head was increasingly faster than traditional methods I was used to, and CtrlP soon became my most used plug-in.

I am constantly trying to "streamline" my vimrc, adding new tricks and removing unused shortcuts. I had heard about the [Unite](https://github.com/Shougo/unite.vim) plug-in by Shugo and thought it could help me consolidate my file/buffer/mru search setup.

With help with various articles I setup Unite to handle content searching, yank history and file/buffer searching but I still wasn't satisfied with the behaviour, I was still more comfortable with Ctrlp. After half an hour of tweaking I managed to replicate most of CtrlP's behaviour with Unite.

## Replicated Behaviour 
+ `<C-x> <C-v>` open file in horizontal and vertical split
+ `<C-t>` open file in new tab
+ `<esc>` exit unite window
+ `<C-j> <C-k>` Navigation, keep hands on home row
+ Appearance changes.

Below is a portion of my vim unite config that has Unite acting similar to CtrlP.
You can view my full [unite configuration](https://github.com/eddie/dotfiles/blob/master/vim/vimrc#L249) on github.


{% highlight vim %}
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
" => Unite
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
let g:unite_enable_start_insert = 1
let g:unite_split_rule = "botright"
let g:unite_force_overwrite_statusline = 0
let g:unite_winheight = 10

call unite#custom_source('file_rec,file_rec/async,file_mru,file,buffer,grep',
      \ 'ignore_pattern', join([
      \ '\.git/',
      \ ], '\|'))

call unite#filters#matcher_default#use(['matcher_fuzzy'])
call unite#filters#sorter_default#use(['sorter_rank'])

nnoremap <C-P> :<C-u>Unite  -buffer-name=files   -start-insert buffer file_rec/async:!<cr>

autocmd FileType unite call s:unite_settings()

function! s:unite_settings()
  let b:SuperTabDisabled=1
  imap <buffer> <C-j>   <Plug>(unite_select_next_line)
  imap <buffer> <C-k>   <Plug>(unite_select_previous_line)
  imap <silent><buffer><expr> <C-x> unite#do_action('split')
  imap <silent><buffer><expr> <C-v> unite#do_action('vsplit')
  imap <silent><buffer><expr> <C-t> unite#do_action('tabopen')

  nmap <buffer> <ESC> <Plug>(unite_exit)
endfunction
{% endhighlight %}



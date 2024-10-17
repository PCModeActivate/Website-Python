set splitright
nmap <C-up> <C-w><up>
nmap <C-down> <C-w><down>
nmap <C-left> <C-w><left>
nmap <C-right> <C-w><right>
:set tabstop=4
:set shiftwidth=4
:set expandtab

vnoremap // y/\V<C-R>=escape(@",'/\')<CR><CR>
""nnoremap <C-P> :cscope find e

command H set hlsearch
command NH nohlsearch
command VT :vert term

nnoremap <C-W><C-M> :call MaximizeToggle()<CR>

    function! MaximizeToggle()
      if exists("s:maximize_session")
        exec "source " . s:maximize_session
        call delete(s:maximize_session)
        unlet s:maximize_session
        let &hidden=s:maximize_hidden_save
        unlet s:maximize_hidden_save
      else
        let s:maximize_hidden_save = &hidden
        let s:maximize_session = tempname()
        set hidden
        exec "mksession! " . s:maximize_session
        only
      endif
    endfunction

    call plug#begin()

    " List your plugins here
    Plug 'prabirshrestha/vim-lsp'
    Plug 'mattn/vim-lsp-settings'
    Plug 'prabirshrestha/asyncomplete.vim'
    Plug 'prabirshrestha/asyncomplete-lsp.vim'
    Plug 'keremc/asyncomplete-clang.vim'
    Plug 'preservim/nerdtree'
    Plug 'jesseleite/vim-agriculture'

    call plug#end()

    let g:LanguageClient_serverCommands = {
      \ 'cpp': ['clangd'],
      \ 'c': ['clangd'],
      \ 'h': ['clangd'],
      \ 'hh': ['clangd'],
      \ }


    """"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
    " CSCOPE settings for vim
    """"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
    "

    " This file contains some boilerplate settings for vim's cscope interface,
" plus some keyboard mappings that I've found useful.
"
" USAGE:
" -- vim 6:     Stick this file in your ~/.vim/plugin directory (or in a
"               'plugin' directory in some other directory that is in your
"               'runtimepath'.
"
" -- vim 5:     Stick this file somewhere and 'source cscope.vim' it from
"               your ~/.vimrc file (or cut and paste it into your .vimrc).
"
" NOTE:
" These key maps use multiple keystrokes (2 or 3 keys).  If you find that vim
" keeps timing you out before you can complete them, try changing your timeout
" settings, as explained below.
"
" Happy cscoping,
"
" Jason Duell       jduell@alumni.princeton.edu     2002/3/7
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""


" This tests to see if vim was configured with the '--enable-cscope' option
" when it was compiled.  If it wasn't, time to recompile vim...
if has("cscope")

    """"""""""""" Standard cscope/vim boilerplate

    " use both cscope and ctag for 'ctrl-]', ':ta', and 'vim -t'
    set cscopetag

    " check cscope for definition of a symbol before checking ctags: set to 1
    " if you want the reverse search order.
    set csto=0

    " add any cscope database in current directory
    if filereadable("cscope.out")
        set nocscopeverbose
        cscope add cscope.out
    " else add the database pointed to by environment variable
    elseif $CSCOPE_DB != ""
        cscope add $CSCOPE_DB
    endif

    " show msg when any other cscope db added
    set cscopeverbose


    """"""""""""" My cscope/vim key mappings
    "
    " The following maps all invoke one of the following cscope search types:
    "
    "   's'   symbol: find all references to the token under cursor
    "   'g'   global: find global definition(s) of the token under cursor
    "   'c'   calls:  find all calls to the function name under cursor
    "   't'   text:   find all instances of the text under cursor
    "   'e'   egrep:  egrep search for the word under cursor
    "   'f'   file:   open the filename under cursor
    "   'i'   includes: find files that include the filename under cursor
    "   'd'   called: find functions that function under cursor calls    
" Below are three sets of the maps: one set that just jumps to your
    " search result, one that splits the existing vim window horizontally and
    " diplays your search result in the new window, and one that does the same
    " thing, but does a vertical split instead (vim 6 only).
    "
    " I've used CTRL-\ and CTRL-@ as the starting keys for these maps, as it's
    " unlikely that you need their default mappings (CTRL-\'s default use is
    " as part of CTRL-\ CTRL-N typemap, which basically just does the same
    " thing as hitting 'escape': CTRL-@ doesn't seem to have any default use).
    " If you don't like using 'CTRL-@' or CTRL-\, , you can change some or all
    " of these maps to use other keys.  One likely candidate is 'CTRL-_'
    " (which also maps to CTRL-/, which is easier to type).  By default it is
    " used to switch between Hebrew and English keyboard mode.
    "
    " All of the maps involving the <cfile> macro use '^<cfile>$': this is so
    " that searches over '#include <time.h>" return only references to
    " 'time.h', and not 'sys/time.h', etc. (by default cscope will return all
    " files that contain 'time.h' as part of their name).


    " To do the first type of search, hit 'CTRL-\', followed by one of the
    " cscope search types above (s,g,c,t,e,f,i,d).  The result of your cscope
    " search will be displayed in the current window.  You can use CTRL-T to
    " go back to where you were before the search.
    "

    nmap <C-\>s :cs find s <C-R>=expand("<cword>")<CR><CR>
    nmap <C-\>g :cs find g <C-R>=expand("<cword>")<CR><CR>
    nmap <C-\>c :cs find c <C-R>=expand("<cword>")<CR><CR>
    nmap <C-\>t :cs find t <C-R>=expand("<cword>")<CR><CR>
    nmap <C-\>e :cs find e <C-R>=expand("<cword>")<CR><CR>
    nmap <C-\>f :cs find f <C-R>=expand("<cfile>")<CR><CR>
    nmap <C-\>i :cs find i ^<C-R>=expand("<cfile>")<CR>$<CR>
    nmap <C-\>d :cs find d <C-R>=expand("<cword>")<CR><CR>


    " Using 'CTRL-spacebar' (intepreted as CTRL-@ by vim) then a search type
    " makes the vim window split horizontally, with search result displayed in
    " the new window.
    "
    " (Note: earlier versions of vim may not have the :scs command, but it
    " can be simulated roughly via:
    "    nmap <C-@>s <C-W><C-S> :cs find s <C-R>=expand("<cword>")<CR><CR>

"    noremap <C-@>s :scs find s <C-R>=expand("<cword>")<CR><CR>
"    noremap <C-@>g :scs find g <C-R>=expand("<cword>")<CR><CR>
"    noremap <C-@>c :scs find c <C-R>=expand("<cword>")<CR><CR>
"    noremap <C-@>t :scs find t <C-R>=expand("<cword>")<CR><CR>
"    noremap <C-@>e :scs find e <C-R>=expand("<cword>")<CR><CR>
"    noremap <C-@>f :scs find f <C-R>=expand("<cfile>")<CR><CR>
"    noremap <C-@>i :scs find i ^<C-R>=expand("<cfile>")<CR>$<CR>
"    noremap <C-@>d :scs find d <C-R>=expand("<cword>")<CR><CR>


    " Hitting CTRL-space *twice* before the search type does a vertical
    " split instead of a horizontal one (vim 6 and up only)
    "
    " (Note: you may wish to put a 'set splitright' in your .vimrc
    " if you prefer the new window on the right instead of the left


"    noremap <C-@><C-@>s :vert scs find s <C-R>=expand("<cword>")<CR><CR>
"    noremap <C-@><C-@>g :vert scs find g <C-R>=expand("<cword>")<CR><CR>
"    noremap <C-@><C-@>c :vert scs find c <C-R>=expand("<cword>")<CR><CR>
"    noremap <C-@><C-@>t :vert scs find t <C-R>=expand("<cword>")<CR><CR>
"    noremap <C-@><C-@>e :vert scs find e <C-R>=expand("<cword>")<CR><CR>
"    noremap <C-@><C-@>f :vert scs find f <C-R>=expand("<cfile>")<CR><CR>
"    noremap <C-@><C-@>i :vert scs find i ^<C-R>=expand("<cfile>")<CR>$<CR>
"    noremap <C-@><C-@>d :vert scs find d <C-R>=expand("<cword>")<CR><CR>


    """"""""""""" key map timeouts
    "
    " By default Vim will only wait 1 second for each keystroke in a mapping.
    " You may find that too short with the above typemaps.  If so, you should
    " either turn off mapping timeouts via 'notimeout'.
    "
    "set notimeout
    "
    " Or, you can keep timeouts, by uncommenting the timeoutlen line below,
    " with your own personal favorite value (in milliseconds):
    "
    "set timeoutlen=4000
    "
    " Either way, since mapping timeout settings by default also set the
    " timeouts for multicharacter 'keys codes' (like <F1>), you should also
    " set ttimeout and ttimeoutlen: otherwise, you will experience strange
    " delays as vim waits for a keystroke after you hit ESC (it will be
    " waiting to see if the ESC is actually part of a key code like <F1>).
    "
    "set ttimeout
    "
    " personally, I find a tenth of a second to work well for key code
    " timeouts. If you experience problems and have a slow terminal or network
    " connection, set it higher.  If you don't set ttimeoutlen, the value for
    " timeoutlent (default: 1000 = 1 second, which is sluggish) is used.
    "
    "set ttimeoutlen=100

endif

autocmd User asyncomplete_setup call asyncomplete#register_source(
    \ asyncomplete#sources#clang#get_source_options())


function! s:on_lsp_buffer_enabled() abort
    setlocal omnifunc=lsp#complete
    setlocal signcolumn=yes
    if exists('+tagfunc') | setlocal tagfunc=lsp#tagfunc | endif
    nmap <buffer> gd <plug>(lsp-definition)
    nmap <buffer> gs <plug>(lsp-document-symbol-search)
    nmap <buffer> gS <plug>(lsp-workspace-symbol-search)
    nmap <buffer> gr <plug>(lsp-references)
    nmap <buffer> gi <plug>(lsp-implementation)
    nmap <buffer> gt <plug>(lsp-type-definition)
    nmap <buffer> <leader>rn <plug>(lsp-rename)
    nmap <buffer> [g <plug>(lsp-previous-diagnostic)
    nmap <buffer> ]g <plug>(lsp-next-diagnostic)
    nmap <buffer> K <plug>(lsp-hover)
    nnoremap <buffer> <expr><c-f> lsp#scroll(+4)

    let g:lsp_format_sync_timeout = 1000
    autocmd! BufWritePre *.rs,*.go call execute('LspDocumentFormatSync')

    " refer to doc to add more commands
endfunction

augroup lsp_install
    au!
    " call s:on_lsp_buffer_enabled only for languages that has the server registered.
    autocmd User lsp_buffer_enabled call s:on_lsp_buffer_enabled()
augroup END

let g:lsp_semantic_enabled = 1
let g:lsp_diagnostics_enabled = 1
let g:lsp_document_highlight_enabled = 0

nnoremap <C-N> :NERDTreeToggle<CR>

nnoremap <silent> <C-P> :Rg <C-R><C-W><CR>
nnoremap <silent> <C-L> :Rg! <C-R><C-W><CR>
nnoremap <silent> <C-J> :Files<CR>
nnoremap <silent> <C-H> :History<CR>

let g:agriculture#rg_options = '--fixed-strings --color=always --case-sensitive'

let g:fzf_history_dir = '~/.vim/fzf-history'


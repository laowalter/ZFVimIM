# ZFVimIM

Input Method by pure vim script, inspired by [VimIM](https://github.com/vim-scripts/VimIM)

Outstanding features:

* more friendly long sentence match and sentence predict
* auto create user word during input
* auto re-order word priority accorrding to your input history
* cloud input, auto pull and push your db file from/to Github

![](https://raw.githubusercontent.com/ZSaberLv0/ZFVimIM/master/preview.gif)

if you like my work, [check here](https://github.com/ZSaberLv0?utf8=%E2%9C%93&tab=repositories&q=ZFVim) for a list of my vim plugins


# how to use

1. requirement:

    * `v:version >= 704`, older version may work, but not tested
    * (optional) `git`, for db update
    * (optional) `vim8` with `job` or `neovim`, and `Plug 'ZSaberLv0/ZFVimJob'`, for async db update
    * (optional) `has('python')` or `has('python3')`, for better db load performance

1. use [Vundle](https://github.com/VundleVim/Vundle.vim) or any other plugin manager you like to install

    ```
    Plugin 'ZSaberLv0/ZFVimJob' " optional, for async db update
    Plugin 'ZSaberLv0/ZFVimIM'
    Plugin 'ZSaberLv0/ZFVimIM_pinyin' " repo that contain db files
    ```

1. use `;;` to toggle input method

    default keymaps:

    ```
    nnoremap <expr><silent> ;; ZFVimIME_keymap_toggle_n()
    inoremap <expr> ;; ZFVimIME_keymap_toggle_i()
    vnoremap <expr> ;; ZFVimIME_keymap_toggle_v()

    nnoremap <expr><silent> ;: ZFVimIME_keymap_next_n()
    inoremap <expr> ;: ZFVimIME_keymap_next_i()
    vnoremap <expr> ;: ZFVimIME_keymap_next_v()

    nnoremap <expr><silent> ;, ZFVimIME_keymap_add_n()
    inoremap <expr> ;, ZFVimIME_keymap_add_i()
    xnoremap <expr> ;, ZFVimIME_keymap_add_v()

    nnoremap <expr><silent> ;. ZFVimIME_keymap_remove_n()
    inoremap <expr> ;. ZFVimIME_keymap_remove_i()
    xnoremap <expr> ;. ZFVimIME_keymap_remove_v()
    ```

    you may disable default keymap by `let g:ZFVimIM_keymap = 0`

1. during input:

    * scroll page by `-` or `=`
    * input and choose word by `<space>` or `0~9`

1. your input history would be recorded and
    automatically push to github,
    see `cloud input` below for more info


# cloud input

## cloud input (minimal recommend config)

1. fork [ZSaberLv0/ZFVimIM_pinyin](https://github.com/ZSaberLv0/ZFVimIM_pinyin)
    and use `Plugin 'YourUserName/ZFVimIM_pinyin'`
1. supply your git info (make sure it has `git push --force` permission)

    ```
    let g:ZFVimIM_pinyin_gitUserEmail='YourEmail'
    let g:ZFVimIM_pinyin_gitUserName='YourUserName'
    let g:ZFVimIM_pinyin_gitUserToken='YourGithubAccessToken'
    ```

## cloud input (detail config)

once configured properly, your db changes would be pushed to Github automatically

requirement:

* register and supply your db repo and git info
    (see [ZSaberLv0/ZFVimIM_pinyin](https://github.com/ZSaberLv0/ZFVimIM_pinyin) for how to implement)
* (optional) have [ZSaberLv0/ZFVimJob](https://github.com/ZSaberLv0/ZFVimJob) installed and `ZFJobAvailable()`,
    for async pull and push
* (optional) have `has('python')` or `has('python3')` support for better save/load performance


if it's hard to support async mode, you may also:

* pull and push manually by `:call ZFVimIM_download()` and `:call ZFVimIM_upload()`
* automatically ask you to input git info to push before exit vim,
    by `let g:ZFVimIM_cloudSync_enable=1`


of course, you must have push permission for db repo,
feel free to fork the default repo (`ZSaberLv0/ZFVimIM_pinyin`),
or supply your own db repo

**NOTE:**

your db repo may contain many commits after long time usage,
which may cause a huge `.git` dir,
it's recommended to clean up it occasionally, by:

* delete and re-create the repo
* if you have `push --force` permission,
    use [ZSaberLv0/ZFVimGitUtil](https://github.com/ZSaberLv0/ZFVimGitUtil)'s
    `:ZFGitHardRemoveAllHistory` to remove all history commits,
    or use `g:ZFVimIM_cloudAsync_autoCleanup` for short


# configs

* `let g:ZFVimIM_predictLimitWhenMatch = 5`

    max number of predict when exact match exists,
    limit this number would let you choose exact match more easily

* `let g:ZFVimIM_predictLimit = 10`

    max number of predict, set to `-1` for no limit,
    set to `0` to disable predict,
    note: predict feature would cause a bit longer loading time
    when you first enable input method

* `let g:ZFVimIM_autoAddWordLen=3*4`

    when you choose word and the word's byte length less than this value,
    we would add the word to db file automatically

* `let g:ZFVimIM_cloudAsync_outputTo={...}`

    for async cloud input, output log to where
    (see [ZFJobOutput](https://github.com/ZSaberLv0/ZFVimJob)), default:

    ```
    let g:ZFVimIM_cloudAsync_outputTo = {
                \   'outputType' : 'statusline',
                \   'outputId' : 'ZFVimIM_cloud_async',
                \ }
    ```

* `let g:ZFVimIM_cloudAsync_autoCleanup=30`

    for async cloud input only,
    if greater than 0,
    and your `git rev-list --count HEAD` exceeds this value,
    we would try to remove all history commits by
    [ZSaberLv0/ZFVimGitUtil](https://github.com/ZSaberLv0/ZFVimGitUtil)'s
    `:ZFGitHardRemoveAllHistory`,
    to keep the db repo clean


# functions

* `ZFVimIME_start()` `ZFVimIME_stop()` `ZFVimIME_toggle()` `ZFVimIME_next()`

    start or stop, must called during Insert Mode, as
    `<c-r>=ZFVimIME_start()<cr>`

* `:IMAdd word key` or `ZFVimIM_wordAdd(word, key)`

    manually add word

* `:IMRemove word [key]` or `ZFVimIM_wordRemove(word [, key])`

    manually remove word

* `:IMReorder word [key]` or `ZFVimIM_wordReorder(word [, key])`

    manually reorder word priority


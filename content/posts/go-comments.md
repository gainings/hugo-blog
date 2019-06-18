---
title: exported function should have comment or be unexportedに対抗するためのプログラムを作った
date: 2018-08-25T22:58:39+09:00
---

## はじめに

どうも最近落ち着きがないゲインです。

普段の趣味プロとかで`exported function hogehoge should have comment~` 

って言われるのが鬱陶しいのと、

わざわざ手打ちで解決するのもアホらしいのでプログラムで解決してもらいました。

リポジトリは[こちら gainings/go-comments](https://github.com/gainings/go-comments)

## 出来ること

プログラムの第1引数にgoのファイル名を食わせると、

`should have comment~`な関数、変数、構造体に,

`//{name} is TODO:need to enter a comment`

を追加した状態で標準出力に出力します。

これだけです。

中身としてはASTをゴニョゴニョこねていい感じに判別して、
コメントを追加しているだけです。

## 用途

流石にこれだけでは使いみちが微妙なので、

自分はvimのcommandに登録しました

こんな感じのscriptを書いて

```vim
command! -nargs=? GoComments call g:GoComments() 

function! g:GoComments()
  let src=systemlist('go-comments '. bufname(""))
  call writefile(src, expand("%:p"))
  e!
endfunction
```

これを`.vimrc`で適当に呼び出して見ました

```vim
source ~/vim_script/go-comments/goComments.vim
```

なお今回はどうせ自分しか使わないだろうと思ったのと、

そこまでvim plugin詳しくない...なため雑な使いみちになりましたが、

将来気力が湧いたら[こちらのBlog](http://haya14busa.com/vim-go-client/)[^plugin]を参考にちゃんとしたpluginとして提供してみたいです。

## Demo

![gas-newfile](/images/go_comments_demo.gif)

ざっくりこんな感じ

GoCommentsと打つと

`Export` かつ `shoud have comments`な奴ら用にコメントが自動で挿入される。

あとはTODOタスクとして忘れずにコメントを入力するだけです。

## まとめ

GoのASTを使って自分用vim scirptを作ってみました。

ASTは色々遊べそうで面白いですね。

あとは一応はじめての自作vim-scriptだったんですが、

多分実装が良くないのでちゃんとそこも見直したいと思います

## 参考文献
[^plugin]: http://haya14busa.com/vim-go-client/


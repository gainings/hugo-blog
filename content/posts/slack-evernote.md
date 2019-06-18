---
title: Slackで"/clip yesterday" をGASに自動実行してもらう
date: 2018-06-01T23:33:13+09:00
---

## はじめに

どうも最近数学ガールを読み始めたゲインです．

内容もさることながら，著者の[結城浩先生のTwitter](https://twitter.com/hyuki)もなかなか参考になるので，
控えめに言って良さ味が深いです．

さてそんな結城先生の気になったつぶやきがこちら

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr"><a href="https://twitter.com/hashtag/%E3%81%B2%E3%81%A8%E3%82%8ASlack?src=hash&amp;ref_src=twsrc%5Etfw">#ひとりSlack</a><br>朝のリマインダーにいわれて /clip yesterdayを実行した。昨日のSlack投稿をEvernoteに転送する儀式。まだ自動化できてない。</p>&mdash; 結城浩 (@hyuki) <a href="https://twitter.com/hyuki/status/1001964561052860417?ref_src=twsrc%5Etfw">2018年5月30日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

はいこれなんとなくできそうですね．

ということでちゃちゃっとお試し．

## 自動化

[こちらの記事](http://mirahouse.jp/n10/2018/01/17-104344.html)を参考に試してみる

(本当はあんまり良くないんだろうな...)

記事では cron を実行していますが，

そのためには常にプログラムを実行するためのサーバーを用意する必要があるため，
万人には向きません．

ではこれを自動的に毎日実行するために
Google App Scriptと呼ばれる，
無料のプログラミング環境を使ってみましょう.

[Google Drive](https://www.google.com/intl/ja_ALL/drive/)へ自分のアカウントでログインし，

以下のような感じでGASのファイルを作成する．

`新規 > その他` の中に `Google App Script`がない方は，
`新規 > その他 > アプリを追加` から `Google App Script` を探して追加する必要があります

![gas-newfile](/images/gas-newfile.png)

これでGASのファイルが作れたので，

中身を実装していきます．

事前に自分のチームのドメインとチャンネルIDとトークンを確認しておいてください

チャンネルIDは[こちら](https://api.slack.com/custom-integrations/legacy-tokens)

トークンは[こちらの記事](https://qiita.com/Yinaura/items/bd28c7b9ef614696fb7e)を参考に

```
function clip() {
  var options = {
    "method" : "POST",
  }

  //ここからを自分の環境に合わせて編集

  // 自分のSlackのドメイン名(hogehoge.slack.comのhogehogeの部分)
  var domain = "gainings"; 
  var token = "xoxp-xxxx-xxxx-xxxx-xxxx";
  var channelID = "CCCCCCC"
  
  var url = "https://"+domain+".slack.com/api/chat.command?token="+token+"&channel="+channelID+"&command=/clip&text=yesterday";
  var response = UrlFetchApp.fetch(url, options);
}
```

これで上のバーにある再生ボタンのようなボタンを押すと，
Slackに `/clip yesterday` が打たれるはずです．

ではこれを毎朝実行してもらいましょう．

再生ボタンの隣にある時計のようなボタンを押してください．

![trigger](/images/trigger.png)

これを押すと 

`トリガーが設定されていません。今すぐ追加するにはここをクリックしてください。`

と出るはずです．

ではクリックして追加してみましょう

そうするとこんな画面になるはずです．

![trigger-when](/images/trigger-when.png)

ここでは 何の関数を いつ動かす？ ということが設定できます．

結城先生は朝7時に打っているらしいので，

`日タイマー` の `午前6時~午前7時`を設定してみましょう．

これは1日1回 午前6時から午前7時のあいだに実行するという意味です．

これで設定が完了です．

## まとめ

Google App Script を使ってEvernoteのClipを自動化してみました．

他にも色んなAPIを活用することで ひとりSlack生活 が捗るんじゃないかなと思います．

なにかご指摘等あればどこでも連絡お待ちしております．

---
title: PixelaのAPIを叩くGolang製Clientを作った"
date: 2018-10-15T23:14:08+09:00
tags: ["Go"]
---
# 10/16 修正

SDKっていう名前が変な気がしたのでClientに変更しました

# はじめに

どうも修論の危機ゲインです．

先日 [Pixela](https://pixe.la)というAPIベース操作可能なでGitHub用にの"草"を生やせるサービスが，
リリースされました．
[commit以外の数値でも草を生やせる、PixelaというAPIサービスを作った！](https://blog.a-know.me/entry/2018/10/14/212338)

結構イケてるな-と思ったので非公式ながらgolangの ~~~SDK~~~ Clientを実装してみました．

かなり雑に作ってあるので優しい言葉でまさかりをお待ちしています．

## 作ったもの

上記の通りPixelaのgolang用 ~~~SDK~~~ Clientです．

GitHubは[こちら](https://github.com/gainings/pixela-go-client)

基本的にはAPI ReferenceにあるAPIをWrapperなのでただただ一生懸命API叩いているだけなんですが，
curlコマンドでパラメーターを指定する煩わしさからは開放されるかなーと考えてます．

あとはこれを使って日々の何かで"草"を生やしていけば楽しくなるかなぁとか

## まとめ

雑にPixela用のgolang clientを作りました．

作る途中で作者の [@a-knowさん](https://twitter.com/a_know)にバグ報告して，直していただいたりと貢献している感が満たせたので良かったです．

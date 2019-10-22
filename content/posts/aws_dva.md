---
title: AWS Developer Associateを取得した話
date: 2019-10-20T08:59:44+09:00
tags: ["AWS", "雑記"]
---

## はじめに

<blockquote class="twitter-tweet" data-partner="tweetdeck"><p lang="ja" dir="ltr">どうも私がAWS Deyeloper Associateです</p>&mdash; ゲイン (@gainings) <a href="https://twitter.com/gainings/status/1185377246325833728?ref_src=twsrc%5Etfw">October 19, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

どうもAWS De**v**eloper Associateのゲインです。

タイトル通り2019/10/19にAWS Developer Associate(以下DVA)を取得してきたので雑記を残します。


## 対象読者

- DVAを受けるか迷っているエンジニア
- どんな対策したか気になるエンジニア

## DVAとは

今回受験した資格は[AWS 認定デベロッパー – アソシエイト](https://aws.amazon.com/jp/certification/certified-developer-associate/)と呼ばれる、AWSに関する技術的スキルと専門知識を証明するための資格です。

具体的には以下のような知識が問われます。

- あるケースではどのAPI呼び出しを使うべきか？
- これを実現するのに必要な手順は？

そのためSAAと比べサービス全般知識ではなく、実際の開発に必要な細かい知識が求められる試験だったと感じました。

## 勉強内容

さて今回合格に至るまでどんな勉強をしたかをざっくりご紹介します。

今回僕が取り組んだのは以下のコンテンツです。

- AWS公式の模試
- Udemyのコースをつまみ食い
- DynamoDBの勉強会に参加
- 実際に触ったり、APIリファレンス読んだり

### AWS公式の模試

とりあえず現在の知識を確かめるために模試を受けました。

以前SAAを取得していたので、模試は無料で受けることが可能でした。

模試の結果、総合スコアは70％で少し勉強が必要だったなという印象でした。

### Udemy

Udemyで今回受講したコースは[AWS Certified Developer - Associate 2019](https://www.udemy.com/share/101WeMBkUddF1bQHw=/)です。

こちらは定価だと18,000円もするのですがまぁUdemyはしょっちゅう割引をやっているので実際は1,800円で買った気がします。

こちらのコースではそれぞれのサービスに関するビデオ講義があり、最期には小テストを受けることができます。

また2つほど模試のような形で問題形式が付随しており、自分の知識を確かめることが可能でした。

やはり問題形式では、わかってるつもりの知識も間違えてしまうことがあって確認には非常に良かったです。

### DynamoDBの勉強会

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">なんかDynamoDB使ってみてもとか考えているけど良くわからないって人で、数時間とか内容によっては1-2日くらい僕がお伝えできる内容で勉強した内容とか流れを会社のブログに体験記として書くくらいはするので、会社に来て教えて欲しい的な人いないかな。</p>&mdash; oranie (@oranie) <a href="https://twitter.com/oranie/status/1169159244785995776?ref_src=twsrc%5Etfw">September 4, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

というツイートに対して、会社の先輩が申し込みをしてなんやかんやで勉強会が開催されることに。

これに関してはそのうち先輩が素晴らしい体験記事を上げてくれるはずなのでお待ち下さい。

試験後の感想ですがDynamoDBの出題はかなり多いなと感じたので、概念的な部分や単語、ユースケースやベストプラクティスを教えていただいてかなり試験ではきちんと答えることが出来たと感じました。

当日優しく教えてくださった[@oranie](https://twitter.com/oranie)さんのおかげで合格したと言っても過言ではありません。ありがとうございました。

### 実際に触ったり、調べたり

それ以外として、あたりまえですが実際にAWSを触ってみました。

特にElasticBeanstalkやEC2は業務では使わない部分だったので自分で触ってみて感触を確かめてみました。

また模試やUdemyのテストでは、あるユースケースに対してどのAPIを使うべきかと言った問題も沢山あったので、
適当にS3、EC2、IAM周りのリファレンスを読みました。

特にSTS周りはわかってない知識が多く新鮮でしたね。

## 結果報告

既に結論は書いてありますが無事に合格することができました。

スコアは831とそこそこの点数を取れたのではないでしょうか。

![aws-dva](/images/aws-dva.jpg)

スキル的な話として普段適当に使っているIAM周りや基本的なEC2周りの知識を獲得できたのが良かったなと感じます。

普段はFargate等を触ることが多いので、逆に基本的な部分が疎かになっていたなと実感しました。

これでSAAと合わせて二冠なのでSysOpsを取らない理由がなくなりましたね。

がんばります。

## まとめ

- DVAは実際の開発時に必要な知識が問われる
- 資格取得を通じてAWSにもっと詳しくなれる
- AWSの新卒研修があって、社内に詳しい人がたくさんいて、月1万までAWSとGCPを使えるTechな会社があるらしい


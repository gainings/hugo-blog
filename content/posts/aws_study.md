---
title: AWSをちょっと理解するまでの4ヶ月
date: 2019-08-11T14:31:31+09:00
tags: ["AWS", "雑記"]
---

## はじめに

どうもいつのまにか社会人になっていたゲインです。

新卒研修や資格取得を通してAWSの事がだんだんと分かってきたので、
どうして今までAWSがわからなかったのか、どうして分かるようになったのかを残します。

よくあるこれをやったら絶対合格とかそういうのは出てきませんのでご了承ください。

## 対象読者

- AWSを触りたい初心者
- AWSを触っているがイマイチ掴みきれていない初学者
- 初心者がどこで詰まっているのかよくわからないリードエンジニア
- 来年の新人研修をどうすべきか悩んでいる人事および担当エンジニア

## AWSが何もわからん時

学生時代はバイトでGoのサーバーサイドのAPIを書いており、
そこでもAWSを使っていました。

今なら何となくその時の構成も分かるのですが、
当時はGoを書くことで精一杯だったので、AWSの事までは追いついていない状態でした。

当時分かっていたのは以下の項目です。

- アプリケーションはEC2で動いているらしい
- どっかにnginxが動いているらしい
- S3ってところに画像とかをおいているらしい
- なんかAurora?っていうDBを使ってるらしい

今考えればまぁよくあるWeb3層アーキテクチャですね。

2019年3月くらいまでの私はそういう人間でした。

## AWSをちょっと理解した

なんやかんやでバイト先を卒業し、
新社会人として東京のTechな企業で務めることになりました。

そこでは多種多様な技術を3か月でツメツメに学ぶという素晴らしいカリキュラム（本当に言葉通りの意味です）が組まれており、
その中にAWSの研修がありました。

4日間かけて実際にAWSの方から研修用コンテンツである
[AWS Technical Essentials 1](https://aws.amazon.com/jp/training/course-descriptions/essentials/)と
[Architecting on AWS](https://aws.amazon.com/jp/training/course-descriptions/architect/)のトレーニングを実施していただきました。

その中では以下のようなことを学びました。

- AWSの基本的なサービスの紹介
- 仮想的なクラウド移行のシナリオをもとにした設計
- AWSを利用したWeb3層アーキテクチャや
- 機械学習SaaSを利用したアプリケーションの構築・デプロイ

特に良かったのが毎日ペアやグループで設計についてお絵描きや議論する部分があり、
このサービスはどこに図示すればよいのかをきちんと身につけるられるプログラムとなっていました。

例えば一般的な3層アーキテクチャをMulti-AZで図示してくださいという課題では以下の部分で悩みました。

- Multi-AZ間でのSecurity Group
- ELBの配置
- AZサービス、Regionサービス、Globalサービスなのか

それらの悩みを実際にAWSの講師の方に質問しながら課題に取り組むことが出来たため、
今まで自分がわかってない部分をきちんと実感し、それを学習することが出来ました。

そのおかげもあって18人にいる同期はAWSの詳しく、
研修のチーム開発ではチームメンバー全員が設計について議論できるレベルで非常に良い体験でした。

凄く良い研修だったので来年の新人研修を設計している方は考慮してみると良いでしょう。

## AWSをさらにちょっと理解した

仲の良い先輩社員さんが同期に対して資格取れよ〜とだる絡みしているところを、
後ろで聞いていたら私も巻き添えを食らって、雑に受けることになりました。(この時受験日の10日前）

受験した資格は[AWS 認定ソリューションアーキテクト - アソシエイト](https://aws.amazon.com/jp/certification/certified-solutions-architect-associate/)と呼ばれる、
AWS上での安全で堅牢かつ最適なコストでシステム設計可能な能力を証明するための資格です。

実はこちらの出題範囲は研修で行った
[Architecting on AWS](https://aws.amazon.com/jp/training/course-descriptions/architect/)
が主で、少し足りないところを補足したら合格できるかなという感じでした。

自分がどこまで理解しているかを確認するために模擬試験をまずは受けました。

結果としては正答率70％でまぁ大体はわかるけど…という結果でした。

このままではもちろんやばいのでひたすら公式ドキュメントを眺め、
それでもわからない部分は実際にサービスを構築して確認したりしました。

そんなこんなで受験結果としては合格ライン720点のところ739点を取得し無事合格です！

![aws-saa](/images/aws-saa.jpg)

対して何もしてないように見えますが、
AWSの研修を受けていたり、今までなんとなく触ったことがあるレベルの前提がある話なので、
これだけでよいのだと思わずに自分にあった方法で勉強すると良いとでしょう。

## どうして今までわからなかったのか

研修を行い資格までとった現在から見て、3月までの自分はどうして理解できていなかったかを振り返ります。

大きな点としては実際にアプリケーションを動かすために必要な様々な技術要素を考慮出来ていなかったというのが大きいです。

例えばEC2にアプリを乗っければいい感じに動くんでしょと思っていたこともありました。

実際にはVPC内のどのサブネットの配置して、ポートの開放やルーティング、
インターネットへの経路など考慮すべきことはたくさんあります。

こういった実際に外へ公開するのに必要な要素を理解せず、
「静的なファイルはS3。
アプリケーションはEC2/ECS。
データベースはRDS。
これさえあれば全部動く」
と考えていました。

特にインターネットに関しては、手元の開発用マシン等ではつながっているのが当たり前であるので意識の外にあったことは間違いないです。

全てうまくやってくれると思い込んでしまうのではなく、
当たり前に存在するインターネットや物理的なマシン等を考慮できるようになると
一歩進んでクラウドを理解できるとこの4か月で学びました。

## どうやって勉強していけばいいのか

実際にトレーニングを行ってもらい、自分でサービスを触り、資格取得を目指すというのが今回の私のルートでした。

しかしAWSのトレーニングは高価で個人だとなかなか出を出せないでしょう。

なので自分のお金を出せる範囲で、AWSのサービスを触って、資格取得を目指すのが現実的だと考えます。

ただ受講した後の感想になりますが、あのトレーニングには高いお金を払うだけの価値が存在するため、
本気で覚える覚悟がある方は21万円を握りしめてトレーニングを受講するのが一番良いでしょう。

勉強する中で気をつけることとしては以下です。

- 自分がやりたいサービスに対して使おうとしているサービスが適切か
- そのサービスはどこまでの事をサービスとして請け負ってくれるのか
- インターネットリソースは何が必要化分かっているか（VPC, NAT, Internet Gateway, Subnet, CIDR、Security Group 1つでもピンとこなかったら要チェック）
- きちんと図示できるか 

特にインターネットリソースに関しては他のコンピューティングリソース（EC2, ECS,lambda等）やデータベースリソース（RDS, Dynamo等）に比べ、
開発時では気にし辛い部分なのでここを考慮できるかどうかが脱初心者への一歩だと考えます。

また資格を勉強する過程で
自分が知らなかった知識を広く収集したり、
細かい部分の穴埋めになったので非常に良かったです。

もちろん資格をとったからといって素晴らしいアーキテクチャを今すぐ組めるようになるわけではないので、
資格なんて無駄と考えることもありますが、資格取得を目指す中での勉強は確実に自分の糧になるので損はないでしょう。

## まとめ

- AWSが4か月でわかるようになった
- 図示は分かりやすい知識確認のツール
- インターネットリソースをよく知ろう
- 資格の勉強は馬鹿にできない
- AWSの新卒研修があって、社内に詳しい人がたくさんいて、月1万までAWSとGCPを使えるTechな会社があるらしい
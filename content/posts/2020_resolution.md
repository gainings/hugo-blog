---
title: 2019年の振り返りと2020年の抱負
date: 2019-12-27T21:12:15+09:00
tags: ["AWS", "雑記", "Go"]
---

## はじめに

仕事を納めたゲインです。

今年は1年を通して色々あったので振り返りたいと思います。

## 振り返り

### 卒業

今年の3月に長岡技術科学大学大学院を卒業しました。

卒業に関しては色々ありました。

正直思い出したくないので一言言えるのは学生相談室の人は相談内容を平気でばらすんですね。以上。

### 入社

4月には無事DMM.comに入社することが出来ました。

[新しいCTOになって](https://inside.dmm.com/entry/12/12/dmm-techvision) 初の新卒ということで、
新卒研修も大幅にアップデートされており、非常にラッキーだったなと感じています。

オンプレの話からAWS,フロントエンド(React), バックエンド(Go)、アプリ(kotlinとswift)のWeb開発。

知識のinputとして輪読や負荷試験、セキュリティなどなどの非常に抱負な内容を一流のエンジニアから学べたのは中々他の研修ではないんじゃないでしょうか。

来年度に向けて講師陣が[気合を入れている](https://inside.dmm.com/entry/2019/10/03/engineer-training-2019)ので今後入ってくる人たちはもっと良い研修を受けられると思いますし、僕も協力できるところはお手伝いしていきたいです。

### AWS

今年触った内容の中でも、AWSに関しては手厚い研修内容や非常に優秀な先輩がいた事でとても理解が進んだ内容だと思います。

[書いたブログ](https://blog-gainings.com/posts/aws_study/) がちょっとバズったりしたおかげで、
AWS公式の[トレーニング採用事例](https://aws.amazon.com/jp/training/case-studies/dmm-training/)に乗ったり、新卒の見ながら[re:Invent 2019に参加して](https://inside.dmm.com/entry/2019/12/26/aws-reinvent2019) 海外のエンジニアと交流する機会をいただけたりと学ぶには最高の環境でした。

知識の面でも、入社当時は以下のような画像にあるアーキテクチャでシステムが動いていると思っていました。

![old_3tier](/images/stupid_3tier.jpg)

しかし、様々学ぶにつれて本当は以下の画像なアーキテクチャが必要だということがわかり、
過去の自分を恥じたのと同時に、
これがわかるようになったという成長も感じました。
![3tier](/images/3tier.jpg)

その過程でSAA取ったりDVA取ったりもしました。
ラスベガスでSysOps落ちたのはすごく残念ですが。

### 仕事

様々な研修を経て7月にはSRE部に配属されました。

まぁ色々とありましたが現状はとてもいい環境に身を置かせてもらっているなと感じています。

特にメンター的な役割としている方と出会えたのも非常にラッキーで、
アルバイト時代も今もメンターの人に非常に恵まれている人生だなと感じます。

ただやはり仕事に対して自分一人で価値を出し切れていないなと思うことが多々あります。

クラウドのアーキテクチャもそうですし、そもそもDNSの仕組みがわかっていなかったり、
ネットワークの基本的な部分がわかっていなかったりというのが露呈してきました。

今だから思うのはちゃんと学生時代に勉強しておけばよかったということです。
まぁそれが出来たら苦労はしないし、周りが言っても無駄なんですけどね。

そういった基本的な知識がなくてもAWSは触れましたが、
クラウドにおんぶにだっこな状況だと、それに振り回されたりすることもあると感じました。

なのでクラウドの奥がどうなっているかをちゃんと考えれるようになりたいというのが最近の目標です。

あとまだまだ新卒のラベルが剥がれてないなとも感じています。

一度本番でやらかしちゃったことがあるのですが、そのときも「まぁ新卒だから仕方ないよ」みたいな慰めを受けました。
それ自体はありがたいのですが、仕事の結果に新卒もくそも無いのでそれを起こしてしまった事と、
それを先輩に言わせてしまったことがとても悔しいです。

新卒だから仕方ない、新卒の割にはよくやっているのようなことを言わせずに個人としてきちんと成果を出すのとそれを周りに見せていくことを今後は心がけたいです。

## 2020の抱負

来年は20卒も入ってきて新卒だからというのは通じなくなります。

なのできちんと仕事の成果に対して責任を持つこと、またそれを実現できるように技術や知識を身に着けていくことを重点的にやっていきたいです。

具体的にはk8sの知識の習得や、
コンテナ全般の知識の補充。
Linuxのプロセスやメモリ管理の話やネットワークの話などCSに近いような部分の話。
GoのアーキテクチャやIaCのベストプラクティスなどなど。
SREとして監視やSLA、SLO周りの策定に関する知識。
これらの主にサーバーに関連する部分の知識を身に着けていきたいと思います。

またその過程でAWSのSolution Architect Professionalと専門知識からなにか資格も取りたいなと思います。

あとは個人としての価値を高めていくために副業等も1件はやってみたいと思います。

現状では会社の名前と新卒ラベルで下駄を履かされているのでそれをとっとと捨てたいです。

## まとめ

いまいちまとまらん話でもありますが、今後はもっとやっていきってことです。

主にクラウド、サーバーのあたりを重点的に攻めて、社内の色んなプロジェクトを救える人材になりたいと思います。

最後に素晴らしい環境を与えてくれた会社と今まで出会った皆さんに感謝します。

来年もよろしくおねがいします。
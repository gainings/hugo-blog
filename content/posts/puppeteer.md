---
title: "puppeteerでGoogleログインしたりファイルダウンロードして遊んだ"
date: 2017-08-24T22:11:13+09:00
comments: true
---

夏休みほぼ終わりかけのゲインです．

家でダラダラしてたらQiitaからのメールで,
"先週いいねが多かった投稿ベスト20"が来てダラダラ見てたら，
[--headless時代の本命？ Chrome を Node.jsから操作するライブラリ puppeteer について](http://qiita.com/Quramy/items/26058e83e898ec2ec078?utm_source=Qiita%E3%83%8B%E3%83%A5%E3%83%BC%E3%82%B9&utm_campaign=832a462266-Qiita_newsletter_273_08_23_2017&utm_medium=email&utm_term=0_e44feaa081-832a462266-33143797)
が合って面白そうだったのでちょっと遊んでみました．
その備忘録をチョロっと書いておきます．

# 環境設定等
適当にディレクトリを作って移動する．
```
npm init
npm i -s puppeteer
```
参考元と特に代わりはないので適当に．

公式に有るサンプルでスクショを撮ってみて動かせるかチェック．

# Google認証
このままだとただのパクリ記事なのでちょっとGoogle認証でログインが必要なサイトで試してみることに．
```
const puppeteer = require('puppeteer');

//環境変数にGoogleのログイン用アドレスとパスワードを設定
const googleAddr = process.env.GOOGLE_ADDRESS
const googlePasswd = process.env.GOOGLE_PASSWD

(async () => {
	const browser = await puppeteer.launch();
	const page = await browser.newPage();
	
	// googleログインが必要なサイトのページへ行く
	await page.goto('https://hogehogefugafuga.com');
  // googleログインのボタンやリンクのセレクターをサイト内から探してくる
	await page.click("li.hogehoge-google")
	await page.waitForNavigation();
	await page.focus('input[type="email"]');
	await page.type(googleAddr);
	await page.click('input#next')
	await page.waitForSelector('input#Passwd')
	await page.focus('input#Passwd');
	await page.type(googlePasswd);
	await page.click('input#signIn')
	//3秒程度待つ
	await page.waitFor(3000)
	//色々処理
	browser.close();
})();
```
これで間違いなければリダイレクト先に移るはずです．

また2段階認証等が入っていると先へ進むのは困難なため，

セキュリティに十分考慮し自己判断で解除してください．

# ファイルをダウンロードする
リンクをクリックしたらファイルダイアログが出てきて何かを保存できる機能がある時に,

それも自動で取ってこれたら嬉しいのでなんとか取得してみます．

```
const fs = require('fs');

//ログイン処理等終了後
const downloadUrl = 'http://hogehoge.com/csv'
const result = await page.evaluate((downloadUrl) => {
	const xhr = new XMLHttpRequest();
	xhr.open("GET", downloadUrl, false);
	//この辺は適宜変更の必要あり
	xhr.overrideMimeType('text/plain; charset=Shift_JIS');
	xhr.send();
	return xhr.responseText;
},downloadUrl);

fs.writeFileSync("result.csv", result, {encoding: null});
```
今回色々試したサイトに置いてあったcsvはsjisで書かれており，何も設定せずにそのまま取得しようとすると文字化けが発生して大変だったので，`xhr.overrideMimeType()`のところでsjisを指定しました．

取得したいファイルによって変える必要がある部分は各自変えてください．

# 感想
サクッとpuppeteerを試してみました．

今回自分が裏で作ったのは毎日定時にクレジットカードの利用明細を自動でダウンロードしてきて，今月に使った金額の合計をIFTTTにpushし，
それをpushbullet経由でスマホにプッシュ通知送るようにしました．
それが良いのかちょっと良く分からないので，ソースとかを乗っけるのはとりあえず辞めておきます．

もっと色々遊んだり，実用的なことが出来そうなので今後も情報を追っかけていきたいと思います．
現状は学内専用でしか見れないファイルとかをごにょごにょして取得しようかなーと．

何か問題点や指摘がありましたらボロクソに叩いてくれるとうれしいです．

# 参考文献
[--headless時代の本命？ Chrome を Node.jsから操作するライブラリ puppeteer について](http://qiita.com/Quramy/items/26058e83e898ec2ec078?utm_source=Qiita%E3%83%8B%E3%83%A5%E3%83%BC%E3%82%B9&utm_campaign=832a462266-Qiita_newsletter_273_08_23_2017&utm_medium=email&utm_term=0_e44feaa081-832a462266-33143797)

[Puppeteer API v0.9.0](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#pageevaluatepagefunction-args)

[Javascriptでのエンコーディング](http://qiita.com/nbjiao/items/9be484793ff545bace32)



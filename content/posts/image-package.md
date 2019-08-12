---
title: Golangのimage Packageを掘り下げる
date: 2018-04-30T22:23:14+09:00
tags: ["Go"]
---

## はじめに
どうもファイルポインタの気持ちがわからない技術力並なゲインです．

無い内内定が有る内々定になったのでBlogちゃんと運用しようと思います．

今回はちょっとGolangで画像を扱う時に，わけのわからん質問を人にしてしまったので

`image package`を掘り下げていこうと思います．

## Goで画像を読み込みたかった
### pngを読み込む

プロジェクトの仕様として画像ファイルがpngでやってくるらしいです．

これを読み込んで色々と処理したいらしいです．

pngを読み込むサンプルは以下になります．

```go
package main

import (
	"fmt"
	"image/png"
	"log"
	"os"
)

func main() {
	file, err := os.Open("test1.png")
	if err != nil {
		log.Fatal(err)
	}
	img, err := png.Decode(file)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(img.Bounds().String())
}
```

これでpngを読み込むことが出来ます．

`err`になってなければ画像のxyの最小最大の座標が表示されるはずですね．

めでたしめでたし．

### jpegも読み込む

急な仕様変更で今まで`png`画像を読み込めれば良かったものを，
更に`jpeg`画像も読み込みたいという要望が出てきました．

仕方ないので対応することにしましょう．

```
package main

import (
	"fmt"
	"image/jpeg"
	"image/png"
	"log"
	"os"
)

func main() {
	file, err := os.Open("test1.png")
	if err != nil {
		log.Fatal(err)
	}
	pngImg, err := png.Decode(file)
	if err != nil {
		log.Fatal(err)
	}
	file, err = os.Open("test1.jpg")
	if err != nil {
		log.Fatal(err)
	}
	jpgImg, err := jpeg.Decode(file)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(pngImg.Bounds().String())
	fmt.Println(jpgImg.Bounds().String())
}
```

これで`jpeg`も読み込めた．めでたしめでたし．

### 更に他のフォーマットも追加する．
更に追加で要望が入り，

どんな画像形式が来るかわからないけどいい感じに読み込んで欲しいそうです

対応したく無いものの，残念ながら対応しないといけません．

今まで`png.Decode()`,`jpeg.Decode()`していた部分は別の方法で処理しなければ汎用的に処理できないでしょう．

ありがたいことには`image` パッケージにも`Decode`があるしこれが使えそうじゃないですか．

同じように早速使ってみましょう．

```
package main

import (
	"fmt"
	"image"
	"log"
	"os"
)

func main() {
	file, err := os.Open("test1.png")
	if err != nil {
		log.Fatal(err)
	}
	img, _, err := image.Decode(file)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(img.Bounds().String())
}
```

なんだGoが勝手にやってくれるのか，
じゃあ`image/png`や`image/jpeg`なんていらないですね！

...

では実行結果を見てみましょう．

```
2018/04/18 21:11:38 image: unknown format
exit status 1
```
 
 おかしい．
 
 さっきまで読めてたファイルを使っている．
 
 今回は`png.Decode()`,`jpeg.Decode()`を`image.Decode()`に置き換えただけなのになんで `unkown format` と怒られます．
 
 きっとこれはバグに違いない．
 
 ...
 
 と思いたいところだがこれが正しい動作になります．
 
 前座が長くなりましたが本題に入っていきましょう．
 
## 本題

### なぜエラーになったのか．
 
 結論から言えば，
 
 `読み込めるフォーマットとして登録されていなかったから`
 
 です．．
 
 Go側が読み込めるフォーマットとして登録されていないため，
 `unkown format` と怒られたのです．
 
 ではGo側に読み込めるフォーマットとして登録するにはどうしたら良いのでしょう．
 
 それは `image.RegisterFormat()`を読んでやればいいということになります．
 
 `image/png`や`image/jpeg`は上記の関数を`init()`内で読んでいるため， `Decode()`が使えるんです．
 
 これはアンダースコア付きでimportしても同じであるため，
 Web上に出回っているサンプルコードなどは，以下のような形で使われているのをよく見ます．
 
 ```
 import (
 	_ "image/png"
 	_ "image/jpeg"
 )
 ```
 
これによりGo側が読み込めるフォーマットを理解することが出来ました．

ではその内部実装はどうなっているのだろうか？

見ていきましょう．

### image.RegisterFormat()

まずは定義から

`image/format.go`の中の記述はこう[^GO-format]

```
// RegisterFormat registers an image format for use by Decode.
// Name is the name of the format, like "jpeg" or "png".
// Magic is the magic prefix that identifies the format's encoding. The magic
// string can contain "?" wildcards that each match any one byte.
// Decode is the function that decodes the encoded image.
// DecodeConfig is the function that decodes just its configuration.

func RegisterFormat(name, magic string, decode func(io.Reader) (Image, error), decodeConfig func(io.Reader) (Config, error))
```

実際に`image/png`で呼ばれてるのはこんな感じ．[^GO-png]

```
func init() {
	image.RegisterFormat("png", pngHeader, Decode, DecodeConfig)
}
```

ではそれぞれに対応付けて見ていきましょう．

まずは`name`．

これは画像のフォーマットを表す名前．以上．

pngのときは `png`だし，jpegのときは`jpeg`

実際に`Decode`する時に，そのフォーマットが何であったかを確認するのに使えるという認識．

次に`magic`.

これは画像のヘッダー部の識別情報を表すbyte列です．

それぞれの画像を表す識別情報は以下のようになります．

ヘッダーにはwildcardとして `?` が含まれている場合もあります．

|  画像タイプ | 識別情報(16進数表記)|
|:--:|:--:|
| jpeg | ff d8 |
| png | 89 50 4E 47 0D 0A 1A 0A |
| gif | 47 49 46 38 ? 61|
| bpm | 4D 4D 2A 00|
| webp | 52 49 46 46 ? ? ? ? 57 45 42 50 56 50 38 |
|tiff(little-endian)| 4D 4D 2A 00|
|tiff(big-endian)| 49 49 00 2A|

Goがデフォルトで使えるのは`jpeg`,`png`,`gif`になりますが，

`go get -u golang.org/x/image`することで上の画像フォーマットも使えるようになります．

パッケージを呼び出すことで，識別情報がファイルの最初に書かれている画像をGoとして扱えるフォーマットに登録できるわけです．

そのチェックは以下のようにシンプルに比較しています[^Go-format]

画像ファイルから，登録されている識別情報の長さだけ読み出して，それが一致するかどうかを1バイトずつ見ていく．

それを全てのimportされた画像ファイルの識別情報分だけ繰り返すと行った感じになります．

ここで登録されていない画像フォーマットの場合は，呼び出し元の関数で，`unknown format` を返してくれるようになっています．

```
// Match reports whether magic matches b. Magic may contain "?" wildcards.
func match(magic string, b []byte) bool {
	if len(magic) != len(b) {
		return false
	}
	for i, c := range b {
		if magic[i] != c && magic[i] != '?' {
			return false
		}
	}
	return true
}
```

次は`Decode`

decodeは,画像ファイルのイメージデータを `img.Image` インターフェイスを満たす構造体へ格納してくれます．

pngを例にすると，

RGBA形式だったりグレースケールだったり，それぞれの表現方法に合わせて，
データを読み込んでくれます．

各画像フォーマットはデータの格納方法が違うのでぜひ調べてみて下さい．

次は`DecodeConfig`

これは画像の
‐ 表現方法
- 高さ
- 幅
を構造体で返します．

これも `Decode` のようにそれぞれの画像フォーマットに合わせて上手いこと読み込んでくれます．

## 独自フォーマットを作って学ぶ

かなり `Decode` や `DecodeConfig` の部分を説明をサボってしまったので，
実際にオリジナルのフォーマットを作って読み込んでみようと思います．

新しく作る画像フォーマットは `gain`  という名前にすることにします．

これはなんでも良いです．

この `gain`画像イメージの構造は

- 1行目は `GAIN\n` から始まる
- 2行目は 画像の幅
- 3行目は 画像の高さ
- 4行目から~n行目までピクセルの情報が0 ~ 255 (Glay Scale)でスペース区切り

となっています．

実際の中身はこんな感じ．

```
GAIN
8
8
0 0 0 0 0 0 0 0
255 255 255 255 255 255 255 255 
0 0 0 0 0 0 0 0
255 255 255 255 255 255 255 255 
0 0 0 0 0 0 0 0
255 255 255 255 255 255 255 255 
0 0 0 0 0 0 0 0
255 255 255 255 255 255 255 255 
0 0 0 0 0 0 0 0
255 255 255 255 255 255 255 255 
```


さぁこれで立派な画像フォーマットが出来ました．

これを `Decode` するには`image.RegisterFormat`に`name`,`magic`,`Decode`,`DecodeConfig`を渡してやれば良いわけです．

<script src="https://gist.github.com/gainings/141f80a803dd2f6a2a3a9dacdb0e1f3a.js?file=reader.go"></script>

まずは `create.go`の`init()` の部分を御覧ください．

ここでGoに認識してもらうために`image.RegisterFormat`を使いました．

そして大事なのは `Decode` です．

上の書いてある構造で画像を読み込むための処理が書いてあります．

3行目までがヘッダー情報なので，それぞれ読み込みます．

4行目からのピクセルの情報を幅の分だけ，スペース区切りで分割して読み込みます．

それをgray scale用の構造体に `SetGray` で保持しています．

これを画像の高さの分だけ繰り返すと行った挙動になっています．

`DecodeConfig`はヘッダーの情報だけ読み込んで，
高さと幅を構造体に渡しているだけです．

さてこれを実際に `image.Decode` から読んでみましょう．

画像を作るためのコードも乗っけておくので適当な場所に配置して実行して下さい．

<script src="https://gist.github.com/gainings/141f80a803dd2f6a2a3a9dacdb0e1f3a.js?file=create.go"></script>

<script src="https://gist.github.com/gainings/141f80a803dd2f6a2a3a9dacdb0e1f3a.js?file=main.go"></script>

これで独自の画像フォーマット `gain` を `image.Decode` 経由でDecodeして `jpeg` に変換することが出来ました．

他にもGoが対応していない画像フォーマットを読み込む必要が出てきたら，

`Decoder`を作成して,`image.RegisterFormat` を使えば良いわけですね！

## 感想

- ドキュメントやGoDocをちゃんと読もう
- imageさえ使えば全ての画像フォーマットが勝手に使えると思っていた過去の俺を殴りたい
- こういう時，ソースを漁りに行けるGo最高

[^Go-format]:https://github.com/golang/go/blob/master/src/image/format.go
[^Go-png]: https://github.com/golang/go/blob/master/src/image/png/reader.go

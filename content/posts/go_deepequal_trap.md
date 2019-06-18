---
title: Goのreflect.DeepEqualでガッツリハマった話
date: 2018-12-04T19:22:17+09:00
---

## はじめに

この記事は [2018年 アドベントカレンダーとなんの関係もない記事](https://google.com)です．

どうも一回も怒られたことがないバイトリーダーのゲインです．

さて本日バイトでModel周りのテストコードを書いていた時に `reflect.DeepEqual` でガッツリハマったので，共有しておきたいと思います．


## ハマったこと

さて皆さんこちらのコードを御覧ください．
[playgroundはこちら](https://play.golang.org/p/AIpFjp5iQO7)

このコードの実行結果はどうなるでしょう．
実行せずに考えてみてください.

```
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"reflect"
)

type Hoge struct {
	Hoge string `json:"hoge"`
	Huga int    `json:"huga"`
}

func main() {
	m1 := make(map[string]interface{}, 0)
	m1["hoge"] = "hogehoge"
	m1["huga"] = 1

	m2 := make(map[string]interface{}, 0)
	m2["hoge"] = "hogehoge"
	m2["huga"] = 1

	hoge := Hoge{
		Hoge: "hogehoge",
		Huga: 1,
	}
	hogeJSON, err := json.Marshal(hoge)
	if err != nil {
		log.Fatal(err)
	}
	var m3 map[string]interface{}
	err = json.Unmarshal(hogeJSON, &m3)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Printf("m1 == m2?: %t\n", reflect.DeepEqual(m1, m2))
	fmt.Printf("m1 == m3?: %t\n", reflect.DeepEqual(m1, m3))
	fmt.Printf("m2 == m3?: %t\n", reflect.DeepEqual(m2, m3))
}
```

中身としてはm1,m2は同じキーに対して同じ値を入れた別の変数．

m3に関しては構造体を一度JSONにしてから `map[string]interface{}` にUnmarshalした変数になってます．

さてこれを実行すると以下のように表示されるでいいですか？ 

```
m1==m2?: true
m1==m3?: true
m2==m3?: true
```

...残念ながら違うんです．

午前中の私は上記になると思ってました．

実際はこうです．

```
m1 == m2?: true
m1 == m3?: false
m2 == m3?: false
```

さてどうしてこんなことになるんでしょう．

中身を確認してみましょう

ちょっと改良してこんなコードにしてみました．
[playgroundはこちら](https://play.golang.org/p/8_9bWW4cofk)

```
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"reflect"
)

type Hoge struct {
	Hoge string `json:"hoge"`
	Huga int    `json:"huga"`
}

func main() {
	m1 := make(map[string]interface{}, 0)
	m1["hoge"] = "hogehoge"
	m1["huga"] = 1

	m2 := make(map[string]interface{}, 0)
	m2["hoge"] = "hogehoge"
	m2["huga"] = 1

	hoge := Hoge{
		Hoge: "hogehoge",
		Huga: 1,
	}
	hogeJSON, err := json.Marshal(hoge)
	if err != nil {
		log.Fatal(err)
	}
	var m3 map[string]interface{}
	err = json.Unmarshal(hogeJSON, &m3)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Printf("m1 == m2?: %t\n", reflect.DeepEqual(m1, m2))
	fmt.Printf("m1 == m3?: %t\n", reflect.DeepEqual(m1, m3))
	fmt.Printf("m2 == m3?: %t\n", reflect.DeepEqual(m2, m3))

	fmt.Println("---m1---")
	for _, val := range m1 {
		fmt.Printf("%#v\n", val)
	}

	fmt.Println("---m2---")
	for _, val := range m2 {
		fmt.Printf("%#v\n", val)
	}

	fmt.Println("---m3---")
	for _, val := range m3 {
		fmt.Printf("%#v\n", val)
	}
}
```

さてこれを実行した結果は以下です． 

```
m1 == m2?: true
m1 == m3?: false
m2 == m3?: false
---m1---
"hogehoge"
1
---m2---
"hogehoge"
1
---m3---
"hogehoge"
1
```

おかしい...値も同じなのになんでmapが一致しないんだろう...

`reflect.DeepEqual` がおかしいんじゃないのか...

とか思い始めてきたので師匠エンジニアにご相談したところ一発で解決したので解説していきます．

## なぜこうなったのか

先程のコードにもう少し確認用のコードを追加してみましょう
[playgroundはこちら](https://play.golang.org/p/GgnE6JxE7tQ)

```
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"reflect"
)

type Hoge struct {
	Hoge string `json:"hoge"`
	Huga int    `json:"huga"`
}

func main() {
	m1 := make(map[string]interface{}, 0)
	m1["hoge"] = "hogehoge"
	m1["huga"] = 1

	m2 := make(map[string]interface{}, 0)
	m2["hoge"] = "hogehoge"
	m2["huga"] = 1

	hoge := Hoge{
		Hoge: "hogehoge",
		Huga: 1,
	}
	hogeJSON, err := json.Marshal(hoge)
	if err != nil {
		log.Fatal(err)
	}
	var m3 map[string]interface{}
	err = json.Unmarshal(hogeJSON, &m3)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Printf("m1 == m2?: %t\n", reflect.DeepEqual(m1, m2))
	fmt.Printf("m1 == m3?: %t\n", reflect.DeepEqual(m1, m3))
	fmt.Printf("m2 == m3?: %t\n", reflect.DeepEqual(m2, m3))

	fmt.Println("---m1---")
	for _, val := range m1 {
		fmt.Printf("val:%#v type:%T\n", val, val)
	}

	fmt.Println("---m2---")
	for _, val := range m2 {
		fmt.Printf("val:%#v type:%T\n", val, val)
	}

	fmt.Println("---m3---")
	for _, val := range m3 {
		fmt.Printf("val:%#v type:%T\n", val, val)
	}
}
```

最後の確認用のコードに `型` を表示するための `%T` を追加してみました．

このコードの実行結果はこちらです．

```
m1 == m2?: true
m1 == m3?: false
m2 == m3?: false
---m1---
val:"hogehoge" type:string
val:1 type:int
---m2---
val:"hogehoge" type:string
val:1 type:int
---m3---
val:"hogehoge" type:string
val:1 type:float64
```

おや犯人が見えてきましたね．

key `huga` に入っていた `1` が実は`float64`だったため，`reflect.DeepEqual`が`false`を返していたようです．

さて,ではどうして`json`に変換した後，それをmapにUnmarshalすると`float64`型になるのかというと，
そもそもjsonの仕様が定義されているRFC8259[^RFC8259]には数字の上限値は定義されていません.

RFC[^IEEE754]にあるとおり，の倍精度(つまり64bit)で値を丸めることが精度や運用を考慮し実装されることが多いとのことで，Goもそれに習って実装されてるとということですかね(ここ要出典)

こちらの内容はGo公式のBlog[^JSON and Go]やjson packageのGodoc[^Go doc]にも書いてあります．

とのことで`interface`に`json`が`Unmarshal`されるときはjsonのNumber型は`float64`型で割り当てられることになります．

`float64`の最大値を超える場合は`json.Number` を利用したり`big package`を使って独自の`Unmarshaler`を作成したりすると良いでしょう．

## 結論

今回は`reflect.DeepEqual`(というか`json`?)でハマった話でした．

私はこの件で推定５時間程度無駄にしているので，気をつけたい所存です．

この件の問題点を一瞬で見抜いた[@shogo82148](https://twitter.com/shogo82148)には頭が上がりません．(謝辞)

まとめは以下

- パット見の値が同じようでも型が違うことはある
- jsonのNumberはinterfaceの場合，float64にUnmarshalされる.
- 公式のドキュメントを読め
- 最強の師匠がいる環境は最高
- 公式のドキュメントを読め
- お前が思っているより公式の関数は1億倍正しく動く
- 公式のドキュメントを(ry

[^RFC8259]: https://tools.ietf.org/html/rfc8259#page-7
[^IEEE754]: https://ieeexplore.ieee.org/document/4610935/references#references
[^JSON and Go]: https://blog.golang.org/json-and-go#Decoding
[^Go doc]: https://golang.org/pkg/encoding/json/#Unmarshal

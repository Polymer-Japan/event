**以下のように修正します**

```
あらすじ
* 概要
    * WebComponentsがエコであるとは？
        * 現状の問題点
        * WebComponentを使った解決策
    * WebComponents.orgの紹介
* WebComponentsを使ってみる
    * カスタムエレメントを探してみる
        * Papar-ElelemetなどGoogle製のカスタムエレメントの紹介
        * 他のベンダー製のカスタムエレメントの紹介
    * カスタムエレメントを使ってみる
        * コード紹介
* WebComponentsを登録してみる
    * How Toの紹介
* Polymerの良さ
    * WebComponentで作るより簡単
    * WebComponentに搭載れていない機能を使える
```

#### `[自己紹介]`

#### `[前提]`

これまで私たちは多くのサービスを作り、その中で多くの部品を作ってきました。
それはフワッと浮かび上がる画像や、マウスをのせると開くメニュー、または、自動的に切り替わるスライダーなど

#### `[問題提起]`

これらは、プロジェクトが変わる度に少しの仕様の違いや、景観の違いという理由で、また０から作り直しておりました。

#### `[妥協・模索]`

これらはHTML5やCSS3、Twitter Bootstrapの登場などで、作るのが簡単にはなってきてはおりますが、数行のHTML、CSS、JSが必要になることは多いです
数行のHTML、CSS、JSも必要なく、他のプロジェクトでも共通して使えるようにするためには、どうしたらよいのでしょうか？

#### `[解決策]`

HTMLタグを作れば良いのです。

#### `[例]`

例えば、
フワッと浮かび上がる画像は、

````html
<float-img src="/image/img_01.png" alt=""></float-img>
````


マウスをのせると開くメニューは、

```html
<slide-menu>
    <menu-item href="http://foo.com/">xxxx</menu-item>
</slide-menu>
```

自動的に切り替わるスライダーは、

```html
<auto-slider duration="10" delay="5">
    <slider-item src="/image_01" order="1"></slider-item>
</auto-slider>
```

#### `[Web Componentsなんだ]`

このように独自のHTMLタグを作ってくれるのが、WebComponentになります。

#### `[Polymerなんだ]`

そして、WebComponentを簡単に作れたり、WebComponentを使えるようにしてくれるのがPolymerになります。

#### `[なぜPolymer？]`

なぜPolymerを使うのかと言うと
WebComponentsを導入していないブラウザでアクセスされた際に、WebComponentを表示するポリフィル機能があるため
WebComponentsをオリジナルで作るよりも簡単なため
WebComponentsには無い機能を使うことが出来るため

#### `[Polymerを使うメリット]`

まだWebComponentsに入っていない機能や便利な点については以下があります

* 宣言的要素の登録： `<polymer-element>`
* 宣言的継承： `<polymer-element extends="...">`
* 宣言的な双方向データバインディング： `<input id="input" value="{{foo}}">`
* 宣言イベントハンドラ： `<button on-click="{{handleClick}}">`
* 公開されたプロパティ：`xFoo.bar = 5、<x-foo bar="5">`
* プロパティの観察： `barChanged: function() {...}`
* デフォルトではPointerEvents / PointerGestures

などがあります。

#### `[結論]`

以上ことから、WebComponentsを導入する場合は、Polymerを使うべきと考えれます

#### `[バトンタッチ]`

続いてのセッションは aggre さんから、「Polymerの導入事例」について話してもらいます。それでは aggre さん、よろしくお願いします。

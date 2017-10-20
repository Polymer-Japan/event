# 歴史を振り返る

## 1999年

- HTML 4.01 勧告
- XMLHttpRequest が IE5 に実装される

## 2005年

- Ajax が有名になる
- Web2.0 というワードが発祥した

## 2007年

- iPhone が発売され、モバイルはアプリ主導に
- モバイル Web 低迷の始まり

## 2011年

- Web Components を Google が提案

## 2013年

- The Extensible Web Manifesto が発表される
- GitHub に Polymer v0.0.20130711 のリリースが作られる

## 2014年

- HTML5 勧告 （実に15年ぶりの仕様更新）
- Polymer 0.5 がリリースされる

## 2015年

- Polymer 1.0 がリリースされる
- HTTP/2 が承認される

## 2017年

- Polymer 2.0 がリリースされる
- Safari が Shadow DOM に対応した
- webcomponents.org に Custom Elements のカタログが統一化される
- Polymer 3 が発表される
- Polymer Japan が発足した
- Service Worker が Webkit で開発中のステータスに
- HTML 5.2 勧告

# Polymer

https://www.polymer-project.org/about より

- Chrome組織の中のフロントエンドチームが主導するOSSプロジェクト
- フロントエンド開発者が、Webの力を最大限に利用し、進化を促進することを支援する
	- PWA の構築を支援する
- Chrome組織内での開発なので、最新の機能を試すことができる
	- もちろん polyfill によってすべてのモダンブラウザで実行可能になっている

歴史を振り返ってわかったように web の進化は 14年もの間止まっていて、その間に今日では多くの人がインターネットを利用するときに使う環境はモバイル（ネイティブアプリ）が主役になってしまった。

「The Extensible Web Manifesto」によって、ブラウザが raw レベルの API を公開すうことで、進化を開発者と共に進めていく、その一部が Web Components です。

そして最新のプラットフォーム仕様（Web Components, Service Worker, HTTP/2）が利用できるようになった今、これらを使ったアプリケーションの構築が現実味を帯びていると考えられます。

これまでは jQuery など様々なフレームワークやライブラリによって、UIコンポーネントが提供されてきました。しかしモバイルのような限られたリソースで表示する場合、速度（DOM操作が遅かったり）や回線（速度や切断）の問題に悩まされてきました。

ネイティブな Web Components が実装されたブラウザ環境では、UIの操作や回線の問題はブラウザ（プラットフォーム）が処理することにより、開発者が悩みから解放され、より良いアプリケーションを提供できる可能性が広がります。例えばDOM操作はブラウザネイティブに処理した方が速いし、Service Workerによってオフライン対応も容易です。

ただし Web Components の構成要素の仕様は、まだ DRAFT です。例えば Polymer 1.0 から 2.0 への移行は Shadow DOM v0 から v1 への移行を必要としています。
もし JavaScript のみで Web Components を利用していたら、この変更はとても大きなものだったと思います。しかし Polymer 1.x から 2.0 への移行では、それほどの変更を必要としません。
また互換（ハイブリッド）モードが用意され、1.x と 2.x で Custom Elements のコードを共有することができます。

つまり Polymer の役割は Web Components を使ったアプリケーションやライブラリを構築する上での（ベストプラクティスとしての）ライブラリやツールであり、Web プラットフォームを使ったアプリケーションの開発をサポートするものです。

Polymerが提供する機能は以下のようなものです

- Polymer.Element スーパークラス
- イベントハンドリング（クリックとタップの差異吸収や、ブラウザ互換など）
- データシステム（双方向バインディング、Observer, Computed properties）
- ヘルパーエレメント（dom-if, dom-repeat）
- CLIツール（ジェネレータ、テスティング）

# 日本でのこれまで

2014年ごろWeb Componentsが話題になることが多く、Polymer 0.5が出たのも重なり、流行りに敏感な層が多くさわっていたと思います。
それらの記事は Blog や Qiita などに残っています。
1.0 がリリースされた当時、それらの記事がネガティブだったのと、1.0での変更が大きかったので、1.0 自体が大きく話題にならなかったと考えています。またブラウザのサポート状況がよくなかったのも影響していると思います。

ただし2015年からの2年で少しずつ状況は変わってきているという認識があります。
個人では業務でその頃から商用利用していたので、今年までの変化を体感しているつもりです。

# WebComponent から Polymer へ

TODO : Google I/O '17 の monica セッションから、HTMLElement -> Polymer.Element までのレイヤーと差分のコードからPolymerを使ったときをイメージさせる
https://www.youtube.com/watch?v=assSM3rlvZ8

# 未来

先日行われた Polymer Summit 2017 で Polymer 3 が発表されました。
現在はテクニカルプレビュー（お試し版）のため、商用利用などは推奨されません。
リリースは2年後を予定しています。

Polymer Summit の少し前に開催された Google I/O '17 では Safari が Shadow DOMをネイティブサポートされたことが発表されました。
これにより Web Components の仕様実装で進んでいないのは HTML Imports となっています。

Polymer 3 では、これまでに課題とされてきた箇所について、対応しています。

## HTML Imports

例のサポート表 xxx

HTML Imports 自体はブラウザーの実装状況が良くなく、今後もそれらのブラウザで実装される見通しはないと言えます。
これはブラウザでモジュールをインポートする仕組みとして ES Module import と HTML imports が同時に話し合われていたという背景があると思います。
HTML imports と比べ、ES Module import は Web Components とは関係なくブラウザ上で動作する様々な Java Script モジュールエコシステムの構築に役立ちます。
多くのブラウザはすでに import に対応しているので、Web Components の世界を広めるための決定（方針転換）としては良いのではないかと思います。

一方で、HTML Templates の記述は HTML in JS になってしまうことは Polymer Summitでの発表中にも大きな反響がありました。

さらに、HTML Imports はルートのHTMLから辿ったとき、同一HTMLは一度しかロードされないという利点があったのですが、このあたりも ES module import に変わることで影響があるかもしれません。

こういった問題については Polymer 3が製品版に近づくにつれて、考慮されていくのではないかと思います。
また、この話題に関連して lit-html というライブラリが開発中ですが、これについては本日は詳しくは触れません。

## Bower

歴史では触れませんでしたが、Polymer が生まれた2013年あたりは、フロントエンドの依存解決には Bower を使うのが一般的でした。しかし今日 Bower は非推奨となってます。

Polymer 3ではnpmリポジトリを使うようになります。コマンドは yarn を使います。
yarn を使う理由は flat 構造のインストールをするためです。

現時点でも Webpack を利用するような方法が共有されています。
Google や Vaadin など有力どころの Custom Elements はすでに npm からのインストールできるようになっています。
ただし webcomponents.org に登録されている Custom Elements のすべてが Bower 以外のインストール方法に対応しているわけではないので、現時点では Bower のみ（もしくは併用）することが良いかもしれません。
もちろん 3rd ベンダーの Custom Elements を使わない場合は、すぐにでも移行できますね。

## SEO and Share

最後にSEOやSNS共有です。Googleの検索エンジンのバックグラウンドは Chrome のため、Web Components が解釈可能とされていますが、他の検索エンジンやFacebook、Twitterなどの共有はうまくいきません。

これについての課題や、その解決方法などは、本日の最終セッションで @aggre さんが解説します。

続いてのセッションは kawakami さんから、「なぜPolymerを使うのか」について話してもらいます。それでは kawakami さん、よろしくお願いします。



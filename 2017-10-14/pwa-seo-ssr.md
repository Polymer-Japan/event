# PWA, SEO, SSR

このセッションでは Polymer を使った PWA の実装、SEO 問題とその解決としての SSR をお話します。

## PWA

Progressive Web Apps( PWA, PWAs ) とは、OS ネイティブアプリと同様の体験を Web App でも提供可能にする、いくつかの技術の総称です。

今すぐにフルスペックの PWA が使えるのは Android とデスクトップの Chrome ですが、Safari と Edge でも基礎技術が In Development になり、近々有効になるのではないかと思います。

時期 Windows Store で PWA によるアプリが配信可能になるという発表もありました。

Chrome, Safari, Edge, Firefox のすべてで PWA が利用できる日が近づいており、今ホットなトピックかと思います。

PWA を構成する技術についてここでは詳細を省きますが、大まかにこのような実装が求められています。

- Service Workera
- ホームスクリーンへの追加
- スプラッシュスクリーン
- HTTPS

なかでも大きな比重を持つのが *Service Worker* です。そしてプログラミングが求められるのも Service Worker だけです。Service Worker 以外の実装は JSON を作成してホスティングしておくだけで利用可能です。SSL も昨今では無料かつ管理不要な PaaS が増えていますね。

### Polymer CLI で Service Worker をつくる

PWA の構成技術のうち、プログラミングが求められる Service Worker も Polymer CLI を使うとコマンドラインで作成できます。

- polymer init
- polymer build 

必要なファイルが自動で生成されていることが確認できるはずです。

すでに PWA 対応ですので、あとは開発したいアプリケーションに応じて、テンプレートを編集するだけで PWA が作成できます。Service Worker として需要なのは次のファイルです。この成果物をコピーして既存プロジェクトに応用してもいいでしょう。

- ./build/es6-unbundled/service-worker.js
- ./build/es6-unbundled/manifest.json

## SEO/Bot Friendly

SEO は多くのサービスにおいて重要な施策となっているかと思います。

Polymer に限らず、JavaScript による UI ライブラリが共通して抱えている課題もまた SEO 、もとい Bot Friendly です。

### Bot

SEO を考える上では、Bot がどのようにウェブサイトのコンテンツを理解するのかを考える必要があります。

多くの Bot は、私たちが普段使うブラウザでウェブサイトを閲覧するのとは異なる振る舞いをします。

私たちがブラウザでウェブサイトを閲覧するとき、ブラウザは文字列としての HTML を解釈し、JavaScript を実行し、CSS でペイントします。

これによって、サーバーから取得される文字列が例えばこのようなものだとしても、

```html
<hello-world></hello-world>
```

最終的にこのようにアプリケーションとして成立するものが画面に表示できます。

```html
<hello-world>
    #shadow-root
    <style>
        h1 {
        	color: blue
        }
    </style>
    <h1>Hello, world!</h1>
</hello-world>
```

これらの処理が私たちが知覚できないほど高速に行われることで、あたかも初めから完全な HTML が表示されたかのように見えるようになります。

しかし Bot の場合は違います。

Bot は JavaScript を実行できないことが多く、たいていは文字列としての HTML をパースするだけです。

JavaScript によって UI を構築する Polymer やその他のライブラリで構築されたウェブサイトは、Bot にとってまったく中身のないウェブサイトとして捉えられてしまいます。

例外として Google の Bot はバックエンドが Chrome で実装されているので JavaScript も実行します。

Google だけをターゲットとするなら問題はありませんが、たとえば Facebook や Twitter でシェアされたとき、Slack で URL を投稿したとき、またはウェブサイトを評価する第3者機関やウェブサービスなども考慮する必要があるウェブサイトを運用する場合、やはり Bot 向けの対策が必要になります。

この対策が SSR です。

### SSR

SSR, Server-Side Rendering は、Bot の代わりにサーバーサイドで JavaScript を実行しておき、完成された HTML をレスポンスする技術です。

SSR には、Node.js 上でフロントエンドの JavaScript を実行する方法と、サーバーにインストールした Chrome などのブラウザでアプリケーションごと実行する方法の 2 つがあります。

Polymer 自体は SSR の機能を提供していませんが、一般的な手法を使って SSR することができます。

## Node.js

Node.js 方式は高速に動作するのが利点です。

Node.js にはブラウザにおける window オブジェクトが存在しないので、window をエミュレートするためのライブラリを使って仮想的なブラウザ上でフロントエンド用の JavaScript を実行します。

Skate.js の提供する SSR 用のメソッドを使うと、Web Components もサーバーサイドで実行できます。

```js
require('@skatejs/ssr/register');
const render = require('@skatejs/ssr');
class Hello extends HTMLElement {
	connectedCallback () {
		const shadowRoot = this.attachShadow({ mode: 'open' });
		shadowRoot.innerHTML = '<span>Hello, world!</span>';
	}
}
customElements.define('x-hello', Hello);
const hello = new Hello();
render(hello).then(console.log);
```

Node.js 方式は高速な代わりに、ブラウザを完全にエミュレートすることはできないため、気をつけないと正しく動作させることができません。

フロントエンドのための JavaScript がサーバーサイドでも動作できるよう、ユニバーサル JavaScript として設計されている必要があります。

## Browser in Server

ブラウザ方式はブラウザで動作するアプリケーションならそのまま動作するのが利点です。

ヘッドレス Chrome などのバイナリファイルをサーバーに置いて、本当のブラウザ上で JavaScript を実行します。

ブラウザにおけるテストだけをパスすれば SSR も安全となるため開発が容易です。また、ブラウザでしか実装されていない新しい API への対応もブラウザで動くならすぐに使うことができます。

ブラウザ方式は開発が容易な代わりに、ブラウザインスタンスの起動などオーバーヘッドが大きいため動作が遅いことが欠点です。

ブラウザ方式の SSR の場合は結果をキャッシュしておいて、定期的にキャッシュをリフレッシュするような仕組みが合わせて必要になるでしょう。

## ShadowDOM

一般的な手法でサーバーサイドで HTML を生成する方法を紹介しました。

しかし Polymer は古典的な HTML ではなく、ShadowDOM を扱います。

ShadowDOM はスコープが閉じているため、HTML を生成できてもまなおドキュメントとして成立した文字列を取得することができません。

ShadowDOM は文字列として表現することが難しいため、Polymer は古典的な HTML でコンポーネントを表現するオプションを持っています。

### ShadyDOM

Polymer を使ったアプリケーションであれば、URL に `?dom=shady` というパラメータを追加するだけで古典的な HTML でコンポーネントを表現することができます。

JavaScript による制御も、次のようにできます。

```js
window.ShadyDOM = {force: true}; 
```

### Rendertron

Rendertron という Docker を使うと、ブラウザ方式の SSR が簡単に実装できます。

Chrome チームによるプロジェクトのため、Polymer は自動的に ShadyDOM モードで動作します。

### prerender.io

サイトマップをクロールして自動的に SSR のキャッシュを作ってくれるサービスもあります。


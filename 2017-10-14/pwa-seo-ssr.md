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



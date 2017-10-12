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


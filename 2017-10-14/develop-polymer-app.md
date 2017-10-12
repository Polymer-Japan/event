# 導入事例から学ぶPolymer開発

このセッションでは、実際に Polymer を導入して開発した PWA/SPA アプリケーションで使った手法やツールなどの紹介をしていきたいと思います。

## アプリケーション構造

Polymer 1.x 系の最新版を使っています。
`polymer init` コマンドでできる基本的なディレクトリやファイルの構造を採用しています。

図：ディレクトリとレイヤーの図

開発に必要な CLI ツールなどは package.json の devDependencies に記述してインストールするようにしています。polymer-cli もここで入ります。

アプリケーションはSPAになっていて、デプロイした環境ではバックエンドのAPIサーバーが存在しますが、ローカル環境では存在しない（Dockerで動かせば動きますが）ので `sideshowcoder/canned` という fake API サーバーを使っています。
URLのパスをディレクトリ構造に見立てて、ファイルのjsonを返却できるような仕組みを持っています。

プロジェクトのディレクトリ構造は以下のとおりです

図：プロジェクトのファイル構造

## 開発フロー

Scrum を採用していて、1 sprint / 2 week となっています。
PO, デザイナ, エンジニア x 3 ぐらいの構成でした。
PO / デザイナ でユーザーストーリーとラフデザインを作ってから、エンジニア含めてウォークスルーやレビューを実施。
Scrum のスプリント計画段階で、実装する画面について Custom Elements で抜き出す箇所を検討し、その単位でタスクを切って見積もりしています。
Custom Elements ごとに必要な作業としては、

- デザインガイドの作成/更新（私たちのプロジェクトはマテリアルデザインでなく、独自に作りました）
- アイコンの作成
- 実装／テスト
- 結合テスト

のようなものがあり、すべて完了した段階で DONE になります。

実際に Custom Elements を作るときは、テストコードをなるべく書くようにしています。
もちろん、まったく必要のないようなエレメントもありますので、これはエレメントを抽出する段階で決めています。
単体テストはエレメント単位で他のエレメントやアプリケーションの依存関係を気にせず記述できるので難しくはありません。

npm の script には

- start （ローカルで実行する）
- lint （静的解析を実行する）
- test
- deploy

があり（実際には deploy:production みたいに分かれていますが）、それぞれローカルやCI環境で実行できるようになっています（もちろん deploy には認証キーなどが必要ですが）。

CI には Jenkins を使っています。

## テスト

Polymer では Custom Elements のテストに `Web Component Tester` という CLI ツールを使っています。これは Selenium ベースで動いています。
このツールは `polymer test` コマンドの内部で使われていますが、これ単独で実行することも可能です。

テストの前にビルドなどを実行したい場合は gulp タスクにした方が良いと思います。
私たちは gulp のタスクから Web Component Tester を実行するようになっています。

```js
const wct = require('web-component-tester').test;
const options = {
  plugins: {
    local: ['chrome', 'firefox'],
  }
};
wct(options).then(() => {
	console.log('テスト成功');
}).catch((error) => {
	console.log(error);
});
```

このような感じです。
これはローカル実行の例ですが、CI環境では様々なOSとブラウザでテストできるようにするため、 Sauce Labs のサービスを使っています。

Web Components は実際にブラウザを起動してテストするため、時間がかかります。Sauce Labs を使う前に Selenium Grid を構築していたこともあったのですが、メンテが大変なのでサービスに移行しました。

静的解析については `polymer lint` というCLIが利用可能ですが、私たちは gulp から実行したかったので、このコマンドの内部で使っていた polylint というツールを直接呼び出しています。

```js
gulp.task('lint', () => {
  return gulp.src(['src/**/*.html', '!src/app.html'])
    .pipe(polylint({noRecursion: true}))
    .pipe(jshint.extract('auto'))
    .pipe(jshint({expr: true}))
    .pipe(polylint.combineWithJshintResults())
  	 .pipe(jshint.reporter, 'jshint-stylish')
    .pipe(jshint.reporter, 'fail');
});
```

こんな感じです。

## デプロイ

最後にデプロイの話を少ししておきたいと思います。

私たちの商用環境はAWSを使っていて、S3 と Cloud Front サービスを利用しています。
デプロイ前にはビルドをしていますが、HTTP/2を利用するため、ファイル単位はそのままで1つにせず、ファイル内のJSやCSSをminifyすることをビルドと呼んでいます。

```js
const htmlminOptions = {
  collapseWhitespace: true,
  minifyCSS: true,
  minifyJS: true,
  removeComments: true
};

gulp.task('deploy.minify', () => {
  return gulp.src(dist('/src/**/*.html'))
    .pipe(htmlmin(htmlminOptions))
    .pipe(gulp.dest(dist('/src')));
});
```

また Service Worker のビルドも gulp から実行しています。

```js
const PolymerProject = require('polymer-build').PolymerProject;
const addServiceWorker = require('polymer-build').addServiceWorker;
const mergeStream = require('merge-stream');
const swPrecacheConfig = require(path.join(__dirname, '..', '..', 'sw-precache-config.js'));

const project = new PolymerProject(require(path.join(__dirname, '..', '..', 'polymer.json')));

gulp.task('deploy.addServiceWorker', ['deploy.stream'], () => {
  return addServiceWorker({
    buildRoot: dist(),
    project: project,
    swPrecacheConfig: swPrecacheConfig,
  });
});

gulp.task('deploy.stream', () => {
  return mergeStream(project.sources(), project.dependencies());
});
```

`polymer-build` というライブラリを使うと、polymer.json の解決などをしてくれるので、便利です。このあたりは polymer-cli の実装を参考にしたもので、もし自分でタスクを定義したい場合は参考になると思います。

最後に S3/Cloud Front へのデプロイは便利なnpmパッケージがあるので、そのまま利用しています。

```js
const awspublish = require('gulp-awspublish');
const parallelize = require("concurrent-transform");
const cloudfront = require('gulp-cloudfront-invalidate-aws-publish');

gulp.task('deploy.invalidate', () => {
  const publisher = createPublisher();
  return gulp.src(dist('**/*'))
    .pipe(parallelize(publisher.publish(headers), 10))
    .pipe(publisher.sync())
    .pipe(cloudfront(cfSettings))
    .pipe(publisher.cache())
    .pipe(awspublish.reporter());
});
```

## まとめ

このように Polymer では、これまでのフロントエンド開発と同じようなツールを使ってアプリケーションを構築していくことが可能です。
今回紹介した構築方法が、少しでも役に立てれば幸いです。

このセッションでは Polymer 単独でアプリケーションを構築する手法を紹介しました。
しかし現時点で Polymer を使うのは Web Components を作るときだけ、という想定をされている方もいるかもしれません。
他のJSフレームワークと Web Components の関連は気になりますよね。
次のセッションでは、Polymer などで作った Web Components を他のフレームワークから利用できるのか？という点について、kawakami さんから紹介してもらおうと思います。
では、よろしくお願いします。


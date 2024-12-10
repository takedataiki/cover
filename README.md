# confirm

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

## 概要
このアプリはYouTubeAPIとYouTubePlayerAPIを活用して特定のチャンネルの歌ってみた動画を連続再生するものです。

## 仕様技術
 - フロントエンド: Vue.js 3, Bootstrap5
 - API: YouTube Data API, YouTubePlayerAPI

### 使用方法
 - チャンネル検索: キーワードを入力してYouTubeのチャンネルを検索します。
 - 動画取得: キーワードに合ったチャンネルが表示されます。聞きたいチャンネルをクリックします。
 - プレイリスト作成: 選択したチャンネルの歌ってみた動画がプレイリストに追加され、再生できます。
 - 操作: プレイリスト内の動画の再生、スキップ、削除が可能です。
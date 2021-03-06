---
title: "API: generate プロパティ"
description: ユニバーサルなウェブアプリケーションから静的なウェブアプリケーションの生成について設定します。
---

<!-- title: "API: The generate Property" -->
<!-- description: Configure the generation of your universal web application to a static web application. -->

<!-- # The generate Property -->

# generate プロパティ

<!-- - Type: `Object` -->

- タイプ: `オブジェクト`

<!-- \> Configure the generation of your universal web application to a static web application. -->

> ユニバーサルなウェブアプリケーションから静的なウェブアプリケーションの生成について設定します。

<!-- When launching `nuxt generate` or calling `nuxt.generate()`, nuxt.js will use the configuration defined in the `generate` property. -->

`nuxt generate` コマンドを実行するか `nuxt.generate()` を呼び出したとき、Nuxt.js は `generete` プロパティで定義された設定を使います。

## dir

<!-- - Type: 'String' -->
<!-- - Default: `'dist'` -->

- タイプ: '文字列'
- デフォルト: `'dist'`

<!-- Directory name created by `nuxt generate`. -->

`nuxt generate` で作成されるディレクトリ名です。

## minify

<!-- - Type: `Object` -->
<!-- - Default: -->

- タイプ: '文字列'
- デフォルト:

```js
minify: {
  collapseBooleanAttributes: true,
  collapseWhitespace: true,
  decodeEntities: true,
  minifyCSS: true,
  minifyJS: true,
  processConditionalComments: true,
  removeAttributeQuotes: false,
  removeComments: false,
  removeEmptyAttributes: true,
  removeOptionalTags: true,
  removeRedundantAttributes: true,
  removeScriptTypeAttributes: false,
  removeStyleLinkTypeAttributes: false,
  removeTagWhitespace: false,
  sortAttributes: true,
  sortClassName: true,
  trimCustomFragments: true,
  useShortDoctype: true
}
```

<!-- You can change the default configuration of [html-minifier](https://github.com/kangax/html-minifier) used by nuxt.js to minify html files created during generate process. -->

generate 処理で生成される HTML ファイルをミニファイするために Nuxt.js で使われている [html-minifier](https://github.com/kangax/html-minifier) のデフォルト設定を変更することができます。

## routes

<!-- - Type: `Array` -->

- タイプ: `配列`

<!-- [Dynamic routes](/guide/routing#dynamic-routes) are ignored by the generate command. -->

generate コマンドでは [動的なルーティング](/guide/routing#動的なルーティング) は無視されます。

<!-- Example: -->

例:

```bash
-| pages/
---| index.vue
---| users/
-----| _id.vue
```

<!-- Only the route `/` will be generated by nuxt.js. -->

ルート `/` のみが Nuxt.js によって生成されます。

<!-- If you want nuxt.js to generate routes with dynamic params, you need to set an array of dynamic routes. -->

動的なパラメーターを用いたルートを生成させたい場合は、動的なルーティングの配列をセットする必要があります。

<!-- We add the mapping for `/users/:id` in `nuxt.config.js`: -->

<!-- We add routes for `/users/:id` in `nuxt.config.js`: -->

`nuxt.config.js` 内に `/users/:id` のルーティングを追加します:

```js
module.exports = {
  generate: {
    routes: [
      '/users/1',
      '/users/2',
      '/users/3'
    ]
  }
}
```

<!-- Then when we launch `nuxt generate`: -->

そして `nuxt generate` を実行します:

```bash
[nuxt] Generating...
[...]
nuxt:render Rendering url / +154ms
nuxt:render Rendering url /users/1 +12ms
nuxt:render Rendering url /users/2 +33ms
nuxt:render Rendering url /users/3 +7ms
nuxt:generate Generate file: /index.html +21ms
nuxt:generate Generate file: /users/1/index.html +31ms
nuxt:generate Generate file: /users/2/index.html +15ms
nuxt:generate Generate file: /users/3/index.html +23ms
nuxt:generate HTML Files generated in 7.6s +6ms
[nuxt] Generate done
```

<!-- Great, but what if we have **dynamic params**? -->

いいですね。しかし、もし **動的なパラメータ** が必要な場合はどうでしょう？

<!-- 1. Use a `Function` which returns a `Promise` -->
<!-- 2. Use a `Function` with a `callback(err, params)` -->

1. `Promise` を返す `関数` を使う
2. `コールバック` と一緒に `関数` を使う

<!-- ### Function which returns a Promise -->

### Promise を返す関数を使う

`nuxt.config.js`

```js
const axios = require('axios')

module.exports = {
  generate: {
    routes: function () {
      return axios.get('https://my-api/users')
      .then((res) => {
        return res.data.map((user) => {
          return '/users/' + user.id
        })
      })      
    }
  }
}
```

<!-- ### Function with a callback -->

### コールバックと一緒に関数を使う

`nuxt.config.js`

```js
const axios = require('axios')

module.exports = {
  generate: {
    routes: function (callback) {
      axios.get('https://my-api/users')
      .then((res) => {
        var routes = res.data.map((user) => {
          return '/users/' + user.id
        })
        callback(null, routes)
      })
      .catch(callback)
    }
  }
}
```

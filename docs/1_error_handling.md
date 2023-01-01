## チュートリアルのURL

https://nuxt.com/docs/getting-started/error-handling

## メモ

### ライフサイクルに応じたエラーのキャッチ方法

|コンテキスト|エラーのキャッチ方法|
|:--|:--|
|Vueのレンダリングサイクル中(SSR,SPA)|onErrorCaptured|
|サーバ,クライアントの起動時(SSR,SPA)|app:error|
|API,Nitroサーバーのライフサイクル中|なし|

#### Vueのレンダリングサイクル中
- onErrorCaptured を使用してフックする
- vue:error フックを使って、エラーがトップレベルまで伝播した際の処理も定義できる
- vueApp.config.errorHandler を使って、ハンドリングされたエラーも含めてすべてのVueエラーを受け取ることができる
  - これによって、エラー報告フレームワーク(SentryやCrashlyticsなど) にエラーを受け取らせることができる
  
```js
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.config.errorHandler = (error, context) => {
    // ...
  }
})
```

#### サーバ,クライアントの起動時
- app:error フックを使って、以下のタイミングで発生したエラーをハンドリングできる
  - Nuxtアプリケーション起動時
  - Nuxtプラグインの実行時
  - app:created, app:beforeMount フックの処理時
  - アプリのマウント(クライアント側)
    - onErrorCaptured か vue:error を使ったほうがよい
  - app:mouted フックの処理時

#### API,Nitroサーバーのライフサイクル中
- このライフサイクル中に発生したエラーに対して、サーバ側のハンドラを定義できない
- 代わりにエラー発生時にエラーページを表示することができる
  - エラーページの実態は、~/error.vue
  - エラーページを削除するときは clearError ヘルパを実行する
    - エラーページ削除時にリダイレクトするパスもセットできる
    - Nuxtプラグインがエラーをスローした場合、エラーページが消されるまで再実行されない
```js
<template>
  <button @click="handleError">Clear errors</button>
</template>
<script setup>
const props = defineProps({
  error: Object
})
const handleError = () => clearError({ redirect: '/' })
</script>
```

### ヘルパメソッド
#### useError
- ステートにエラーを設定し、コンポーネント間でリアクティブかつSSRフレンドリーなグローバルNuxtエラーを作成する

```js
const error = useError()
```

#### createError
- 追加のメタデータを持つエラーを作成する
- これは、アプリのVueとNitroの両方の部分で使用可能であり、サーバサイド・クライアントサイドのどちらでも投げられる
- サーバーサイド
  - 全画面のエラーページが表示される
  - それをクリアするには clearError を実行する
- クライアントサイド
  - 非致命的のエラーが投げられる(エラーハンドリングは手動)
  - 致命的なエラーにしたい場合は、fatal: true を設定する
```js
<script setup>
const route = useRoute()
// 映像に関するデータを取得する
const { data } = await useFetch(`/api/movies/${route.params.slug}`)
// データが取得できなければ、404エラーを投げる
if (!data.value) {
  throw createError({ statusCode: 404, statusMessage: 'Page Not Found' })
}
</script>
```

#### showError
- createErrorを使ったほうがよい
- フルスクリーンのエラーページを表示する(clearError でクリア)
  - 内部で app:error を呼び出している
- クライアントサイド
  - 任意の場所で呼び出すことができる
- サーバーサイド
  - ミドルウェア、プラグイン、 setup() 関数の内部で直接呼び出すこともできる

```js
showError("error has occurred")
showError({ statusCode: 404, statusMessage: "Page Not Found" })

```

#### clearError
- 現在処理されているNuxtエラーをクリアする
- オプションでクリアした後のリダイレクト先のパスを受け取る

### アプリ内のレンダリングエラー
- NuxtErrorBoundaryコンポーネントを使って、サイト全体をエラーページで置き換えることなくアプリ内でクライアントサイドのエラーを処理できる

```js
<template>
  <!-- some content -->
  <NuxtErrorBoundary @error="someErrorLogger">
    <!-- You use the default slot to render your content -->
    <template #error="{ error }">
      You can display the error locally here.
      <button @click="error = null">
        This will clear the error.
      </button>
    </template>
  </NuxtErrorBoundary>
</template>
```
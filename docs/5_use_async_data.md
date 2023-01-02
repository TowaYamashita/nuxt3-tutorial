## チュートリアルのURL

https://nuxt.com/docs/getting-started/data-fetching#useasyncdata
https://nuxt.com/docs/api/composables/use-async-data

## メモ

### 概要

- useAsyncData を使って、ページ・コンポーネント・プラグインの中で、非同期に解決されるデータにアクセスできる
- URLを受け取ってデータを取得した後に複雑なロジックを持つ場合は、useAsyncData が最適
  - URLを受け取ってデータを取得するだけなら useFetch でよい

### シグネチャ

#### パラメータ

|パラメータ名|必須かどうか|説明|デフォルト値|
|:--|:--|:--|:--|
|key|YES|fetchしたデータをキャッシュする用の一意のキー|useAsyncData のインスタンスのファイル名と行番号に対応する一意のキーが生成される|
|handler|YES|非同期関数||
|lazy|NO|クライアントサイドのナビゲーションをブロックする代わりに、ルートをロードした後に handler を解決するかどうか|false|
|default|NO|非同期関数が解決する前に、データのデフォルト値を設定するファクトリ関数||
|server|NO|サーバ側でデータを取得するかどうか|true|
|transform|NO|handler の結果を解決した後に変更するために使用することができる関数||
|pick|NO|handler の解決結果から、この配列で指定されたキーだけを取り出す||
|watch|NO|反応するソースを監視して自動更新するかどうか||
|immediate|NO|リクエストぞ即時に実行するかどうか|true|

#### 返り値

|返り値名|説明|
|:--|:--|
|data|渡された非同期関数の結果|
|pending|データがまだ取得中であるかどうか|
|refresh|ハンドラ関数から返されたデータをリフレッシュするために使用することができる関数|
|excute|ハンドラ関数から返されたデータをリフレッシュするために使用することができる関数(immediate が false の場合は、これを実行しないと非同期処理が実行されない)|
|error|データ取得に失敗した場合のエラーオブジェクト|

- サーバーでデータを取得していない場合（例: server が false の場合）、ハイドレーションが完了するまでデータは取得されないため、クライアント側でuseAsyncDataを待っても、`<script setup>` 内ではデータは null のまま

> ハイドレーションとは?
>> Vueがサーバーから送信された静的HTMLを引き継ぎ、クライアント側のデータ変更に対応できる動的DOMに変換するクライアント側のプロセスのこと

### 例: PV数を表示する

```ts
// sever/api/count.ts

let counter = 0
export default () => {
  counter++
  return JSON.stringify(counter)
}
```

```js
// app.vue

<script setup>
const { data } = await useAsyncData('count', () => $fetch('/api/count'))
</script>

<template>
  Page visits: {{ data }}
</template>
```
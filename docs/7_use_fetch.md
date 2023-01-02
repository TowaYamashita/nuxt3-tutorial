## チュートリアルのURL

https://nuxt.com/docs/getting-started/data-fetching#usefetch
https://nuxt.com/docs/api/composables/use-fetch

## メモ

### 概要

- useFetch を使って、非同期に解決されるデータにアクセスできる(以下の便利機能あり)
  - URL とフェッチオプションに基づいてキーを自動的に生成
  - サーバルートに基づいてリクエスト URL の型ヒントを提供
  - API レスポンス型を推論
- useAsyncData と $fetch のラッパー

### シグネチャ

#### パラメータ

|パラメータ名|必須かどうか|説明|デフォルト値|
|:--|:--|:--|:--|
|url|YES|データの取得先のURL||
|key|NO|fetchしたデータをキャッシュする用の一意のキー|useAsyncData のインスタンスのファイル名と行番号に対応する一意のキーが生成される|
|method|NO|HTTPメソッド名||
|query|NO|クエリストリング||
|params|NO|クエリストリング(queryのエイリアス)||
|body|NO|リクエストボディ||
|headers|NO|リクエストヘッダ||
|baseURL|NO|リクエストのベースURL||
|handler|YES|非同期関数||
|lazy|NO|クライアントサイドのナビゲーションをブロックする代わりに、ルートをロードした後に handler を解決するかどうか|false|
|default|NO|非同期関数が解決する前に、データのデフォルト値を設定するファクトリ関数||
|server|NO|サーバ側でデータを取得するかどうか|true|
|transform|NO|handler の結果を解決した後に変更するために使用することができる関数||
|pick|NO|handler の解決結果から、この配列で指定されたキーだけを取り出す||
|watch|NO|反応するソースを監視して自動更新するかどうか||
|immediate|NO|リクエストぞ即時に実行するかどうか|true|

> 以下のオプションは、リアクティブな値(computed や ref)を指定でき、それらが更新された場合は新しいリクエストが作成される
>> method, query, params, body, headers, baseURL

> パラメータに関数を引数として渡した場合、仮に関数の実行結果が一緒の場合でもキャッシュが効かないため注意
>> キャッシュさせたいなら、独自のキーを作って key に指定する必要がある

#### 返り値

|返り値名|説明|
|:--|:--|
|data|渡された非同期関数の結果|
|pending|データがまだ取得中であるかどうか|
|refresh|ハンドラ関数から返されたデータをリフレッシュするために使用することができる関数|
|excute|ハンドラ関数から返されたデータをリフレッシュするために使用することができる関数(immediate が false の場合は、これを実行しないと非同期処理が実行されない)|
|error|データ取得に失敗した場合のエラーオブジェクト|

- サーバーでデータを取得していない場合（例: server が false の場合）、ハイドレーションが完了するまでデータは取得されないため、クライアント側でuseAsyncDataを待っても、`<script setup>` 内ではデータは null のまま

### 例1: クエリストリングを含んだURLからデータ取得

```js
// クエリストリングを含んだ最終的なURLは、https://api.nuxtjs.dev/mountains?param1=value1&param2=value2

const param1 = ref('value1')
const { data, pending, error, refresh } = await useFetch('https://api.nuxtjs.dev/mountains',{
    query: { param1, param2: 'value2' }
})
```

### 例2: インターセプターを使用したログイン処理のハンドリング

```js
const { data, pending, error, refresh } = await useFetch('/api/auth/login', {
  onRequest({ request, options }) {
    // リクエストを実行する前にヘッダーの組み立てを行う
    options.headers = options.headers || {}
    options.headers.authorization = '...'
  },
  onRequestError({ request, options, error }) {
    // リクエストした際にエラーが発生した時の処理
  },
  onResponse({ request, response, options }) {
    // レスポンスが返ってきたら、データだけ返す
    return response._data
  },
  onResponseError({ request, response, options }) {
    // レスポンスが返ってくる際にエラーが発生した時の処理
  }
})
```
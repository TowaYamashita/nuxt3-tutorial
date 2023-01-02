## チュートリアルのURL

https://nuxt.com/docs/api/composables/use-cookie

## メモ

### 概要

- useAsyncData を使って、ページ・コンポーネント・プラグインの中で、非同期に解決されるデータにアクセスできる
- URLを受け取ってデータを取得した後に複雑なロジックを持つ場合は、useAsyncData が最適
  - URLを受け取ってデータを取得するだけなら useFetch でよい

- useCookie を使って、ページ・コンポーネント・プラグインの中で、クッキーを読み書きできる
  - setup または LifeCycleHooks の間しか動作できない
  - Cookie の値を自動で json形式にシリアライズ・デシリアライズする

### シグネチャ

#### パラメータ

|パラメータ名|必須かどうか|説明|デフォルト値|
|:--|:--|:--|:--|
|name|YES|Cookieを参照するための一意のキー||
|maxAge|NO|Cookie が有効な秒数|設定されていないためいつまでも有効|
|expires|NO|Cookie の有効期限を示す Date オブジェクト|設定されていないためいつまでも有効|
|httpOnly|NO|HttpOnly属性をセットするかどうか|false|
|secure|NO|Secure属性をセットするかどうか|false|
|domain|NO|Domain属性にセットするドメイン||
|path|NO|Path属性にセットするパス||
|sameSite|NO|SameSite属性にセットするbool値か文字列値||
|encode|NO|Cookie の値をエンコードするために使用する関数|JSON.stringify + encodeURIComponent|
|decode|NO|Cookie の値をデコードするために使用する関数|decodeURIComponent + destr|
|default|NO|デフォルト値。Refを返すこともできる||
|watch|NO|Cookie 参照データを監視するためのブール値または文字列|true|

> sameSite
>> true: SameSite 属性を Strict に設定する(同サイト強制)
>> false: SameSite 属性を設定しない
>> lax: SameSite 属性を Lax に設定する(緩やかな同サイト強制)
>> none: SameSite 属性を None に設定する(明示的なクロスサイトクッキー)
>> strict: SameSite属性をStrictに設定する(厳格な同サイト強制)

> Refとは？
>> プリミティブな値（string,number など）を引数にとって、リアクティブなデータを定義するクラス

> watch
>> true: トップレベルのプロパティとそのネストされたプロパティの変更を監視する
>> shallow: トップレベルのプロパティのみ変更を監視する
>> false: クッキー参照データの変更を監視しない

### 例1: サーバAPIのルートに Cookie を設定する

```ts
export default defineEventHandler(event => {
  let counter = getCookie(event, 'counter') || 0

  setCookie(event, 'counter', ++counter)

  return { counter }
})
```

### 例2: ランダムの整数を Cookie に追加したり保存したりする

```js
<template>
  <div>
    <h1>List</h1>
    <pre>{{ list }}</pre>
    <button @click="add">Add</button>
    <button @click="save">Save</button>
  </div>
</template>

<script setup>
const list = useCookie(
  'list',
  {
    default: () => [],
    watch: 'shallow'
  }
)

function add() {
  // watch が shallow であるため、 Cookie で保持している配列の中の要素が変化しても表示が更新されない
  list.value?.push(Math.round(Math.random() * 1000))
}

function save() {
  if (list.value && list.value !== null) {
    // 配列の中身を別の配列に移すことで、表示の更新を行わせる
    list.value = [...list.value]
  }
}
</script>
```
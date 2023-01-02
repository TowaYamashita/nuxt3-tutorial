## チュートリアルのURL

https://nuxt.com/docs/guide/directory-structure/composables

## メモ

### composablesディレクトリの役割

- コンポーザブルとは、 状態を持つロジックをカプセル化して再利用するための関数のこと
  - 例: ページ上のマウスの現在位置のトラッキング
  - Vue の Composition API を活用する
- このディレクトリに置かれたVueコンポーザブルは Nuxt によって自動的にインポートされる

- `nuxi prepare`, `nuxi dev`, `nuxi build` のいづれかを実行して、.nuxt/imports.d.ts という型宣言ファイルを生成する必要がある
  - 開発サーバが起動していない状態で生成しようとすると、「Cannot fing name 'useBar'」のようなエラーが投げられる

### 使い方

#### コンポーザブルの作成方法

```ts
// composables/useFoo.ts

// 名前付きエクスポートを使用してコンポーザブルを作成する場合
export const useFoo = () => {
  return useState('foo', () => 'bar')
}

// デフォルトのエクスポートを使用してコンポーザブルを作成する場合
// 拡張子なしのファイル名のキャメルケース(今回はuseFoo)で参照できる
export default function () {
  return useState('foo', () => 'bar')
}

export const useFoo = () => {
  // NuxtAppインスタンスに提供されたヘルパにコンポーザブルからアクセスできる
  const nuxtApp = useNuxtApp()
  
  // 別のファイルで作成したコンポーザブル(例: useBar)も使える
  const bar = useBar()
}
```

### 作成したコンポーザブルの置き場所
- composables ディレクトリの最上位にあるファイルのみをスキャンする
- ネストしたモジュールに対しても自動インポートを機能させるには、以下のどちらかを使ってそれが置かれているディレクトリも含めるようスキャナーを構成する必要がある
  
```js
// composables/index.ts

// 必要なコンポーザブルを再度エクスポートする
export { utils } from './nested/utils.ts'
```

```js
// nuxt.config.js

export default defineNuxtConfig({
  imports: {
    dirs: [
      // composablesディレクトリの最上位に置かれたファイル
      'composables',
      // composablesディレクトリ配下でネストされたモジュール(一部)
      'composables/*/index.{ts,js,mjs,mts}',
      // composablesディレクトリ配下のファイルすべて
      'composables/**'
    ]
  }
})
```

#### コンポーザブルの使用方法
```js
// app.vue

<template>
  <div>
    {{ foo }}
  </div>
</template>

<script setup>
// 自動でインポートされるので、手動でインポートする必要が無い
const foo = useFoo()
</script>
```
## チュートリアルのURL

https://nuxt.com/docs/guide/directory-structure/pages

## メモ

### pagesディレクトリの役割

- アプリケーション内の各ページのファイルを置く
- /pages ディレクトリ配下に置かれたファイルは、Vue Router を使用したファイルベースのルーティングが定義される

> app.vue だけを使う場合は vue-router は含まれないためアプリケーションのバンドルサイズを小さくすることができます。
> app.vue だけを使う場合は、nuxt.config で pages: true を設定するか、app/router.options.ts を作成しない

#### ページについて

- ページは Vue コンポーネントで 拡張子として .vue, .js, .jsx, .ts, .tsx を使うことができる
- pages/index.vue ファイルは、アプリケーションの / ルートにマップされる
- app.vue を使用している場合は、NuxtPage コンポーネントを使用して、現在のページを表示することを確認する必要がある
- ページ間のルート遷移を可能にするには、ページは単一のルート要素を持つ必要がある (HTMLコメントも要素)

```js
// pages/index.vue

<template>
  <div>
    Page content
  </div>
</template>


// ts か tsx の場合は、defineComponent を使う(以下の例は ts)
export default defineComponent({
  render () {
    return h('h1', 'Index page')
  }
})
```

### ルートパラメータ

```js
// 以下のようなディレクトリ構成
// pages/
// --| index.vue
// --| users-[group]/
// ---| [id].vue

// pages/users-[group]/[id].vue

<template>
  // 例: /users-admins/123 にアクセスすると、<p>admins - 123</p> がレンダリングされる
  <p>{{ $route.params.group }} - {{ $route.params.id }}</p>
</template>

// グローバルな useRoute 関数を使って、Composition API を使ってルートにアクセスできる
<script setup>
const route = useRoute()
// ルートパラメータの内、 group が admin かつ id が 存在しない場合、ログに何かしら表示する
// 例: /users-admin/ にアクセスすると、ログに何かしら出る
if (route.params.group === 'admins' && !route.params.id) {
  console.log('Warning! Make sure user is authenticated!')
}
</script>
```

### キャッチオールルート

- [...slug].vue のような名前のファイルを使って、そのパスの下のすべてのルートにマッチさせることができる

```js
// pages/[...slug].vue

// 例: /hello/world にアクセスすると、<p>["hello", "world"]</p> がレンダリングされる
<template>
  <p>{{ $route.params.slug }}</p>
</template>
```

### ネストされたルート

- child.vue のコンポーネントを表示するには、pages/parent.vue の中に NuxtPage コンポーネントを挿入する必要がある
- NuxtPage コンポーネントが再レンダリングされるタイミングをより細かく制御したい場合（例えば、遷移のため）は以下のどちらかを行う必要がある
  - 1. pageKey プロパティを通じて文字列または関数を渡す
  - 2. definePageMeta を通じてキー値を定義する

```
// 以下のようなディレクトリ構成
// pages/
// -| parent/
// ---| child.vue
// -| parent.vue
```

#### 1. pageKey プロパティを通じて文字列または関数を渡す

```js
// pages/parent.vue
<template>
  <div>
    <h1>I am the parent view</h1>
    <NuxtPage :page-key="someKey" />
  </div>
</template>
```

#### 2. definePageMeta を通じてキー値を定義する

```js
// pages/child.vue
<script setup>
definePageMeta({
  key: route => route.fullPath
})
</script>
```

### ルートのメタデータ

- definePageMeta を使って、アプリ内の各ルートに対してメタデータを定義できる
  - script と script setup の両方で動作できる
- route.meta を使って、定義したメタデータにアクセスできる

```js
<script setup>
definePageMeta({
  title: 'My home page'
})
const route = useRoute()
console.log(route.meta.title) // My home page
</script>
```

- defineEmits, defineProps, definePageMeta でメタデータを定義する場合は、コンポーネントで定義された値は参照できない(インポートされたバインディングは参照できる)
```js
<script setup>
import { someData } from '~/utils/example'

const title = ref('')

definePageMeta({
  title,  // This will create an error
  someData
})
</script>
```

- タイプセーフにメタデータを追加する

```ts
// index.d.ts

declare module '#app' {
  interface PageMeta {
    pageType?: string
  }
}

// 型を拡張する際には、常に何かをインポート/エクスポートすることが重要
export {}
```

- 特別なメタデータ

|||
|:--|:--|
|alias|ページエイリアス。文字列または文字列の配列|
|keepAlive|KeepAliveコンポーネントでラップするかどうか。ブール値。動的な子ルートを持つ親ルートでルート変更に渡ってページ状態を保持する場合に便利|
|key||
|layout|レンダリングする際に使うレイアウト。false(レイアウト無効化),文字列(レンダリング名)|
|layoutTransition, pageTransition|ラップされたtransitionコンポーネントに渡すプロパティ。false(transitionコンポーネントの無効化)|
|middleware|ページを読み込む前に適用するミドルウェア。文字列,関数,それらの配列|
|name|ルート名。文字列|
|path|パスマッチャー。ファイル名だけだと複雑で表現できないパターンだと path を使う|

- カスタムメタデータの入力

### ナビゲーション

- [これ](./10_nuxt_link.md) に記載しているため省略

### ルータオプションの定義

```ts
// app/router.options.ts

import type { RouterConfig } from '@nuxt/schema'

export default <RouterConfig> {
  routes: (_routes) => [
    {
      name: 'home',
      path: '/',
      component: () => import('~/pages/home.vue')
    }
  ],
}
```

- nuxt.config の option には JSON シリアライズ可能なオプションのみ設定可能
  - linkActiveClass, linkExactActiveClass, end, sensitive, strict, hashMode

> hashMode
>> - true にすることで、ハッシュ履歴を有効化できる(SPAモード)
>> - SPAモードでは、ルーターは内部で渡される実際のURLの前にハッシュ文字(#)を使用するが、hashMode を有効にすると、URLがサーバーに送信されることはなく、SSRはサポートされなくなる

```js
// nuxt.config

export default defineNuxtConfig({
  ssr: false,
  router: {
    options: {
      hashMode: true
    }
  }
})
```

### プログラムによるナビゲーション

- navigateTo() を使って、プログラムによるナビゲーションが可能
  - 例: ユーザーからの入力を受け取り、アプリケーション全体で動的にナビゲートするのに適する

> navigateToは常にawaitするか、関数から結果を返すことでチェーンするようにする必要がある

```js
<script setup>
const router = useRouter();
const name = ref('');
const type = ref(1);
function navigate(){
  return navigateTo({
    path: '/search',
    query: {
      name: name.value,
      type: type.value
    }
  })
}
</script>
```
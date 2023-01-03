## チュートリアルのURL

https://nuxt.com/docs/guide/directory-structure/layouts

## メモ

### layoutsディレクトリの役割

- アプリケーション全体で使用できる共通UI・共通レイアウトコンポーネントを置く場所
- このディレクトリに置かれたコンポーネントは Nuxt によって使用時に非同期でインポートされる

- レイアウトを使用する場合は以下のどちらかで行う
  - app.vueに `NuxtLayout` を追加し、ページのメタデータの一部としてレイアウトプロパティを設定する
    - ~/pages統合を使っている場合
  - 手動で `NuxtLayout` のpropとして指定する

- レイアウト名はケバブケース(例: someLayout は some-layout) で参照できる)。
- Nuxtがレイアウト変更間の遷移を適用できるように、レイアウトは単一のルート要素を持つ必要がある

> レイアウトが1つしかない場合は、レイアウトを使わないほうがよい

### デフォルトレイアウトの有効化

```js
// ~/layouts/default.vue

<template>
  <div>
    Some default layout shared across all pages
    <slot />
  </div>
</template>
```

```js
// app.vue

<template>
  <NuxtLayout>
    some page content
  </NuxtLayout>
</template>

// レイアウトを使わない場合だと以下のように書く
// <template>
//   <div>
//     Some default layout shared across all pages
//     some page content
//   </div>
// </template>
```

### 別のレイアウトを設定する

- デフォルトのレイアウトを直接オーバライドしたり、ページごとにレイアウトを設定できる

```
layouts/
--| default.vue
--| custom.vue
```

```js
// app.vue

<template>
  <NuxtLayout :name="layout">
    <NuxtPage />
  </NuxtLayout>
</template>

<script setup>
// デフォルトのレイアウトとして、`custom.vue` でオーバライドするよう設定
const layout = "custom";
</script>
```

```js
// page/index.vue

<script>
// レイアウトとして、`custom.vue` を使うよう設定
definePageMeta({
  layout: "custom",
});
</script>

```

### レイアウトを動的に変更する

```js
<template>
  <div>
    // ボタンを押すと、レイアウトが `custom.vue` に切り替わる
    <button @click="enableCustomLayout">Update layout</button>
  </div>
</template>

<script setup>
function enableCustomLayout () {
  setPageLayout('custom')
}
// デフォルトではレイアウトを使わないよう設定
definePageMeta({
  layout: false,
});
</script>
```

### レイアウトの特定の要素を上書きする
- layout: falseを設定しページ内で NuxtLayout コンポーネントを使用することで、レイアウトの特定の要素を上書きできる

> ページ内で NuxtLayout を使用する場合は、ルート要素でないことを確認するか、レイアウト/ページ遷移を無効にする必要がある

```js
// layouts/custom.vue

<template>
  <div>
    <header>
      <slot name="header">
        Default header content
      </slot>
    </header>
    <main>
      <slot />
    </main>
  </div>
</template>
```

```js
// pages/index.vue

<template>
  <div>
    <NuxtLayout name="custom">
      // `custom.vue` の ヘッダー部分を上書きする
      <template #header> Some header template content. </template>

      // `custom.vue` の main 要素で囲まれた slot に表示する
      The rest of the page
    </NuxtLayout>
  </div>
</template>

<script setup>
definePageMeta({
  layout: false,
});
</script>
```
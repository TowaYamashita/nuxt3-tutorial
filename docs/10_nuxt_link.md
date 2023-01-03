## チュートリアルのURL

https://nuxt.com/docs/api/components/nuxt-link

## メモ

### 概要

- NuxtLink コンポーネントを使って、アプリケーション内のあらゆる種類のリンクを処理できる
  - Vue Routerの RouterLink コンポーネントと HTML の a タグの両方の代替
  - リンクが内部か外部かを Nuxt が判断し、利用可能な最適化機能（プリフェッチ、デフォルト属性など）を使って適切にレンダリングしてくれる

### 使い方

#### 外部リンク(ウェブサイトへの遷移)

```js
<template>
  <NuxtLink to="https://nuxtjs.org">
    Nuxt website
  </NuxtLink>
  <!-- a タグを使って書くならこう -->
  <!-- <a href="https://nuxtjs.org" rel="noopener noreferrer">Nuxt website</a> -->
</template>
```

#### 内部リンク(アプリケーションの別ページへの遷移)

```js
<template>
  <NuxtLink to="/about">
    About page
  </NuxtLink>
</template>
```

### props

|||
|:-|:--|
|to|遷移先。任意のURL か Vue Routerのルートロケーションオブジェクト|
|href|to のエイリアス。to と href を両方使うと href は無視される|
|target|リンクに適用するtarget属性の値|
|rel|リンクに適用するrel属性の値(外部リンクの場合は 「noopenner noreferrer」)|
|noRel|リンクにrel属性を追加するかどうか|
|activeClass|アクティブなリンクに適用するクラス(Vue Router における active-class と同じで、デフォルト値もVue Router のデフォルト)|
|exactActiveClass|正確にアクティブなリンクに適用するクラス(Vue Router における exact-active-class と同じで、デフォルト値もVue Router のデフォルト)|
|relace|内部リンクに対して Vue Router の replace と同じ|
|ariaCurrentValue|アクティブなリンクに適用するaria-current属性の値(Vue Router における aria-current-group と同じ)|
|external|外部リンクとみなすかどうか(true:外部リンク, false:内部リンク)|
|prefetch,noPrefetch|ビューポート内のリンクのプリフェッチを有効にするかどうか|
|prefetchedClass|プリフェッチされたリンクに適用するクラス|
|custom|Nuxt Link 内のコンテンツを a 要素でラップするかどうか (Vue Router における custom と同じ)|

### オーバライド

- defineNuxtLink を使って、独自のリンクコンポーネントを作成できる

```js
// <MyNuxtLink /> として参照できる
export default defineNuxtLink({
  componentName: 'MyNuxtLink',
})
```

|プロパティ名|型|説明|デフォルト値|
|:--|:--|:--|:--|
|componentName|string|独自のリンクコンポーネント名||
|externalRelAttribute|string|外部リンクに適用されるrel属性のデフォルト値。空文字を入れて無効化できる|noopener noreferrer|
|activeClass|string|アクティブなリンクに適用されるデフォルトクラス|Vue Router のデフォルト(router-link-active)|
|exactActiveClass|string|正確にアクティブなリンクに適用されるデフォルトクラス|Vue Router のデフォルト(router-link-exact-active)|
|prefetchedClass|string|プリフェッチされたリンクに適用されるデフォルトクラス||
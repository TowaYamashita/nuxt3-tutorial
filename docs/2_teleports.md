## チュートリアルのURL

https://nuxt.com/docs/api/components/teleports

## メモ

### Teleport コンポーネントって何？

- Teleport コンポーネントを使うことで、それで囲んだコンポーネントをDOM内の別の場所にテレポートさせることができる。
- to には CSS セレクタ文字列か実際の DOM ノードを渡す必要がある。
-  body へのテレポートのみを SSR でサポートしており、他のターゲットについては <ClientOnly> ラッパーを使用してクライアントサイドでサポートしている。

### Teleport コンポーネントの使い所

- フルスクリーンモーダルを実装する際に使うと以下の理由から効果的である
  - モーダル以外の要素によって、モーダルのレイアウトが崩れてしまうのを防げる
  - モーダルの z-index の値より大きい値を指定した要素がモーダルにかぶってしまうのを防げる

### 例: モーダル表示
```js
<template>
  <button @click="open = true">
    Open Modal
  </button>
  <Teleport to="body">
    <div v-if="open" class="modal">
      <p>Hello from the modal!</p>
      <button @click="open = false">
        Close
      </button>
    </div>
  </Teleport>
</template>
```

### 例: クライアントサイドでのTeleport使用
```js
<template>
  <ClientOnly>
    <Teleport to="#some-selector">
      <!-- content -->
    </Teleport>
  </ClientOnly>
</template>
```

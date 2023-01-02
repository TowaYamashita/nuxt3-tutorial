## チュートリアルのURL

https://nuxt.com/docs/api/composables/use-state
https://nuxt.com/docs/getting-started/state-management

## メモ

### useState

#### 概要

- useState を使って、コンポーネント間でリアクティブでSSRフレンドリな共有状態を作成することができる
- useState を使って保持した値はSSRの後（クライアントサイドのハイドレーションの間）保存され、ユニークなキーを使ってすべてのコンポーネントで共有される

> useState は setup または LifeCycleHooks の間だけ使える
> json形式に変換できないものは、useStateで管理できない

#### シグネチャ

|パラメータ名|必須かどうか|説明|デフォルト値|
|:--|:--|:--|:--|
|key|YES|データ取得がリクエスト間で重複排除する用の一意のキー|useState のインスタンスのファイル名と行番号に対応する一意のキーが生成される|
|init|NO|初期値(Refを渡すこともできる)||

> TypeScript を使っている場合のみ、useState で管理する値の型をジェネリクスで指定できる

### 例1: ボタンを押すたび数字が増減する

```js
<script setup>
// 初期値はランダムな整数を保持する
// useState('counter') を使用する他のコンポーネントでも状態の変化が反映される
const counter = useState('counter', () => Math.round(Math.random() * 1000))
</script>

<template>
  <div>
    Counter: {{ counter }}
    // 押下するたび数値が1ずつ増える
    <button @click="counter++">
      +
    </button>
    // 押下するたび数値が1ずつ減る
    <button @click="counter--">
      -
    </button>
  </div>
</template>
```

### 例2: 色をアプリ全体に共有する

```ts
// composables/states.ts

export const useColor = () => useState<string>('color', () => 'pink')
```

```js
// app.vue

<script setup>
  // コンポーザブルとして定義した useColor を使用する
const color = useColor() 
</script>

<template>
  <p>Current color: {{ color }}</p>
</template>
```
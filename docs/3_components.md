## チュートリアルのURL

https://nuxt.com/docs/guide/directory-structure/components

## メモ

### componentsディレクトリの役割

- ページまたは他のコンポーネント内にインポートできるすべての Vue コンポーネントを置く場所
- このディレクトリに置かれたVueコンポーネントは Nuxt によって自動的にインポートされる

```js
// components ディレクトリが以下のようになっている場合、それぞれのファイルのコンポーネントをわざわざインポートしなくても使える
// components/
// --| TheHeader.vue
// --| base/
// ----| foo/
// ------| Button.vue

<template>
  <div>
    <TheHeader />
    <slot />
    <BaseFooButton.vue />
  </div>
</template>
```

### コンポーネントの読み込み方
#### 動的コンポーネント
- Vue の `<component :is="someComputedComponent">` 構文を使用したい場合は、resolveComponent ヘルパーを使用する必要がある
- resolveComponent には変数ではなくコンポーネント名の文字列を渡す必要がある

```js
<template>
  <component :is="clickable ? MyButton : 'div'" />
</template>

<script setup>
const MyButton = resolveComponent('MyButton')
</script>
```

#### 遅延読み込み
- コンポーネント名の先頭に Lazy を付けることで、そのコンポーネントが必要になるまで読み込みを遅延できる
  - 常に必要ではないコンポーネントに対して有用
```js
<template>
  <div>
    <h1>Mountains</h1>
    <LazyMountainsList v-if="show" />
    <button v-if="!show" @click="show = true">Show List</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      show: false
    }
  }
}
</script>
```

#### 自動インポートの無効化
- #componentsからコンポーネントをインポートすることで、Nuxtの自動インポート機能を回避したいできる

```js
<template>
  <div>
    <h1>Mountains</h1>
    <LazyMountainsList v-if="show" />
    <button v-if="!show" @click="show = true">Show List</button>
    <NuxtLink to="/">Home</NuxtLink>
  </div>
</template>

<script setup>
  import { NuxtLink, LazyMountainsList } from '#components'
  const show = ref(false)
</script>

```

### コンポーネントの種類
#### ClientOnly コンポーネント
- ClientOnly で囲まれたコンポーネントは、クライアント側のみレンダリングする
- そのコンポーネントがクライアント側でマウントされるまでは、スロットが代わりに使われる

```js
<template>
  <div>
    <Sidebar />
    <!-- サーバサイドではspanとしてレンダリングされる -->
    <ClientOnly fallbackTag="span">
      <!-- Comment コンポーネントはクライアント側でのみレンダリングされる -->
      <Comments />
      <template #fallback>
        <!-- サーバサイドで「Loading comments...」とレンダリングされる -->
        <p>Loading comments...</p>
      </template>
    </ClientOnly>
  </div>
</template>
```

#### .client / .server コンポーネント
- .client コンポーネントは、クライアント側でのみレンダリングされる
  - Nuxtの自動インポートか #components インポートでのみ機能する
  - onMounted() を使用してレンダリングされたテンプレートにアクセスするには、onMounted()フックのコールバックに `await nextTick()` を追加する必要がある
- .server コンポーネントは、.client コンポーネントのフォールバック

```js
// 以下のようなディレクトリ構成の場合、
// サーバサイドで Comments.server をレンダリングし
// クライアントサイドにマウントされると Comments.client をレンダリングする
// components/
// --| Comments.client.vue
// --| Comments.server.vue

<template>
  <div>
    <Comments />
  </div>
</template>

```

#### DevOnly コンポーネント
- DevOnly で囲まれたコンポーネントは、開発時のみレンダリングする 

```js
<template>
  <div>
    <Sidebar />
    <DevOnly>
      <!-- DebugBar は開発時のみレンダリングされる -->
      <LazyDebugBar />
    </DevOnly>
  </div>
</template>

```


### コンポーネントライブラリの作成方法

- components:dirs フックを使って、ライブラリをインポートできるにして、nuxt.config.js の modules に登録すればok
  - HMR(Fluterでいうホットリロード。変更がすぐ反映される) もサポートしている。
- 例: 以下のようなディレクトリ構成で、awesome-ui という名前のコンポーネントライブラリを作成する
```
| node_modules/
---| awesome-ui/
------| components/
---------| Alert.vue
---------| Button.vue
------| nuxt.js
| pages/
---| index.vue
| nuxt.config.js
```

```js
// node_modules/awesome-ui/nuxt.js
import { defineNuxtModule } from '@nuxt/kit'
import { fileURLToPath } from 'node:url'

export default defineNuxtModule({
  hooks: {
    'components:dirs'(dirs) {
      dirs.push({
        // node_modules/awesome-ui/components ディレクトリ配下のVueコンポーネントを追加する
        path: fileURLToPath(new URL('./components', import.meta.url)),
        prefix: 'awesome'
      })
    }
  }
})
```

```js
// nuxt.config.js
export default {
  modules: ['awesome-ui/nuxt']
}
```

```js
// pages/index.vue
<template>
  <div>
    // awesome-ui のコンポーネントを使う場合は、プレフィクスを付ける
    My <AwesomeButton>UI button</AwesomeButton>!
    <awesome-alert>Here's an alert!</awesome-alert>
  </div>
</template>
```
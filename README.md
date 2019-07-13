# Vue.js Learning 

https://jp.vuejs.org/v2/guide/

## はじめに

### 宣言的レンダリング/条件分岐とループ/ユーザ入力の制御

```JavaScript
v-on:click="seen != seen"
v-if="seen"
v-for="todo in todos"
```

- sink: v-bind
- sink&source: v-model

ポイント：

DOMとデータを関連付けて、データを操作することでVue.jsが裏側でDOMの変更を行っている。

### コンポーネントによる構成

Vue.jsの重要な抽象概念：**コンポーネントシステム**

設計思想：

小さく自己完結的で、再利用可能なコンポーネントの組み合わせる。

コンポーネントの例：

```JavaScript
// todo-item と呼ばれる新しいコンポーネントを定義
Vue.component('todo-item', {
  template: '<li>This is a todo</li>'
})
```

コンポーネントの呼び出しの例：

```HTML
<ol>
  <!-- todos 配列にある各 todo に対して todo-item コンポーネントのインスタンスを作成する -->
  <todo-item></todo-item>
</ol>
```

このままでは This is a todo が描画されるだけで面白みがない。なので...

```JavaScript
Vue.component('todo-item', {
  // todo-item コンポーネントはカスタム属性のような "プロパティ" で受け取ります。
  // このプロパティは todo と呼ばれます。
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
```

## Vueインスタンス

```JavaScript
var vm = new Vue({
    // Option
})
```

Vueアプリケーションは `new Vue()` で作成されたVueインスタンス(root Vue instance)で構成され、必要に応じてネストされたツリーや再利用可能なコンポーネントで形成される。

Todoアプリの場合:

```Text
ルートインスタンス
└─ Todoリスト
   ├─ Todoアイテム
   │  ├─ Todo削除ボタン
   │  └─ Todo編集ボタン
   └─ Todoリストフッター
      ├─ Todoクリアボタン
      └─ Todoリスト統計
```

メモ:

```
ルートインスタンス
└─ ゲストリスト
   ├─ ゲストアイテム
   │  ├─ ゲスト削除ボタン
   │  └─ ゲスト編集ボタン
   ├─ ゲスト追加ボタン
```

### データとメソッド

```JavaScript
// データオブジェクト
var data = { a: 1 }

// Vue インスタンスにオブジェクトを追加する
var vm = new Vue({
  data: data
})

// インスタンスのプロパティを取得すると、
// 元のデータからそのプロパティが返されます
vm.a == data.a // => true

// プロパティへの代入は、元のデータにも反映されます
vm.a = 2
data.a // => 2

// ... そして、その逆もまたしかりです
data.a = 3
vm.a // => 3
```

## インスタンスライフサイクルフック

> 各 Vue インスタンスは、生成時に一連の初期化を行います。例えば、データの監視のセットアップやテンプレートのコンパイル、DOM へのインスタンスのマウント、データが変化したときの DOM の更新などがあります。その初期化の過程で、特定の段階でユーザー自身のコードを追加する、いくつかの ライフサイクルフック(lifecycle hooks) と呼ばれる関数を実行します。

> 例えば、created フックはインスタンスが生成された後にコードを実行したいときに使われます。

```JavaScript
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` は vm インスタンスを指します
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

**=> タイミングで独自の処理を入れるには フック を利用する**

## テンプレート構文

**算出プロパティ：**

テンプレートで複雑な式をわかりにくくなる。こうしたロジックには算出プロパティを利用する。

```HTML
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
<script>
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 算出 getter 関数
    reversedMessage: function () {
      // `this` は vm インスタンスを指します
      return this.message.split('').reverse().join('')
    }
  }
})
</script>
```

**算出プロパティとメソッド**

メソッド呼び出しは再描画が起きると常に更新を実行。算出プロパティは依存関係によってキャッシュされる。つまり `message` が更新されない限りは更新されない。

**ウォッチャー**

データが変わるのに応じて非同期やコストの高い処理（APIへのリクエストなど）を実行したいときに便利。

→変更を検知してすぐにリクエストを送信するより、遅延をはさんだほうがいい。

> この場合では、watch オプションを利用することで、非同期処理( API のアクセス)の実行や、処理をどのくらいの頻度で実行するかを制御したり、最終的な answer が取得できるまでは中間の状態にしておく、といったことが可能になっています。**これらはいずれも算出プロパティでは実現できません。**


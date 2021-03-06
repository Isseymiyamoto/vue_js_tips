# vue_js_tips
vue.jsに関する基礎知識をまとめる


## 読み込み

CDNを用いる場合は、scriptタグで囲む(bodyの閉じタグの手前で)
```
<script src="https://unpkg.com/vue@2.5.21"></script>
```

## vueコンストラクタ

vueインスタンスを生成する際に、コンストラクタ(swiftでいうinitで渡す引数部分)にオブジェクトを渡す

ex.
```
new Vue({
  el: '#app',
  data: {
    message: "hello world"
  }
})
```

### オブジェクト内の各オプションの説明

 

### elオプション: 

・ vue.jsのインスタンスを適応するDOM要素を指定する(マウントという)

・ マウント対象のDOMは、CSSセレクタかDOM要素のオブジェクトを渡して指定する。(DOM要素のオブジェクトとは、document.getElementByIdメソッドなどで取得したオブジェクトをさす)

・ マウントすることでその要素内をvueインスタンスで操作できるようになる。

・ vueインスタンスの生成時に存在しなかったDOM要素に対しては、後から動的にマウントしたい場合は以下のようにする

```
// 変数名のvmは,vue Modelの略で慣例的に使用される
const vm = new new Vue({
  data: {
    message: "hello world"
  }
})

vm.$mount = ('#app')
```

### dataオプション:

・dataオプションではvueインスタンスで扱うデータを定義する

・オブジェクトの形式で定義する。オブジェクトの各プロパティを変数としてvueで扱うことができる。

ex
```
// index.html
<div id="app">
  {{ message }}
</div>

// main.js
new Vue({
  el: '#app',
  data: {
    message: "hello world"
  }
})
```

・ html内にて、{{}}でプロパティを括ることでデータを展開できる。これをマスタッシュ記法という

・ dataオプションにはオブジェクトの他に関数を渡すこともできる。関数を渡した場合は、関数の戻り値でdataが初期化される

ex
```
new Vue({
  el: '#app',
  data: function() {
    return {
      message: "hello world" 
    }
  }
})

// functionの省略形 & dev toolにおけるconsoleからのvueインスタンスアクセス方法

const vm = new Vue({
  el: '#app',
  data() {
    return {
      message: "hello world" 
    }
  }
})

window.vm = vm

// consoleにおいて

vm.$data.message
-> "hello world"

vm.$data.message = "change"
-> "change" //かつこの部分が再描画される

```

・ vue.js ではデータへの代入と参照を監視しており、新しい値が代入されたことを検知して部分的に画面を再描画する機能が備わっている(リアクティブシステム) 


### $watchメソッド

・$watchメソッドを使用することで、データの値を監視して、データが変わったタイミングで特定のコールバック関数を実行することができる

・開発時の動作確認やログ出力などに活用できる

ex. messageの値の変更を監視する
```
// index.html
<div id="app">
  {{ message }}
</div>

// main.js
const vm = new Vue({
  el: '#app',
  data() {
    return {
      message: "Hello, world!" 
    }
  }
})

window.vm = vm

vm.$watch(function(){
  return this.message
}, function(message){
  console.log("変更後の値: " + message)
})
```

・上記のように$watchメソッドは引数に二つの関数を受け取る

・第一引数の関数では、監視対象のデータを返す関数をセットする。上記でのthis.messageにおけるthisは、Vueインスタンスを表している(swiftでいうselfと同様)。this.messageでdataで定義したmessageにアクセスできる

・第二引数には、値が変わった場合に呼び出されるコールバック関数をセットする。コールバック関数の第一引数には、変更後の値が渡されることになる


### watchオプション

・$watchメソッドと機能は同様

・watchプロパティ内で定義したプロパティ名をデータのプロパティ名と一致させることで、データを監視する

・dataの名前と、watchに定義したメソッド名を合わせることでdataの変更時にメソッドが実行される


ex. numbersというデータをwatchプロパティで監視する、numbersの値が変更されるたびにその値に準じて、total_numが再計算される
```
// index.html

<div id="app">
  {{ message }}
</div>

// main.js

const vm = new Vue({
  el: '#app',
  data: {
   numbers: [],
   total_num: 0
  },
  watch: {
    numbers(value){
      this.total_num = 0
      this.numbers.forEach(number => {
          this.total_num += number
      })
    }
  }
})

window.vm = vm
```

### ディレクティブ

・HTMLタグの属性として設定できるvue.js独自の拡張機能

### v-bindディレクティブ

・HTMLタグの属性値にデータの値を設定できる

ex. HTMLタグのtitle属性にメッセージのデータの値を設定する(title属性を設定するとマウスポインタを合わせた場合に、属性値のテキストがポップアップで表示される)

```

<div id="app">
  <p v-bind:title = "message">
  マウスポインタを当ててください
  </p>
</div>

// main.js

const vm = new Vue({
  el: '#app',
  data() {
    return {
      message: "hello world" 
    }
  }
})

window.vm = vm

```

・v-bind:の後に属性値を指定する。上記ではtitle属性。

・その後に=""で任意の文字列を指定している。v-bindでは、""で渡された文字列は文字列として評価されるのではなく、javascriptの式として評価される

・上記のように書くことで、vmのmessageプロパティの値を使用することができる

・省略記法では、上記を例にとると、
```
<p :title="message"></p>
```
と書くこともできる


### フィルター機能

・マスタッシュ記法でデータを表示する際に、データを加工するフィルターの機能

// 便利さを解説するために、マスタッシュ記法のなかに式を展開する場合とフィルター機能両方のコードを以下に描いてみる

ex. マスタッシュの中で直で式を描いた場合(正規表現で変換)
```

<div id="app">
<p> {{ sum.toString().replace(/(\d)(?=(\d{3})+$)/g, '$1') }}円
  </p>
</div>

// main.js

const vm = new Vue({
  el: '#app',
  data() {
    return {
      sum: 3000000  
    }
  }
})

window.vm = vm

```

ex. フィルター機能を用いた場合
```

<div id="app">
<p> {{ sum | numberWithDelimiter }}円
  </p>
</div>

// main.js

const vm = new Vue({
  el: '#app',
  data() {
    return {
      sum: 3000000  
    }
  },
  filters: {
    numberWithDelimiter(value){
      if(!value) return '0'
      return value.toString().replace(/(\d)(?=(\d{3})+$)/g, '$1')
    }
  }
})

window.vm = vm

```

・フィルターを定義するには、vueインスタンスのfiltersオプション内に定義する

・マスタッシュの中でフィルターの機能を利用する際には、| で要素を区切って使用するフィルター名を記載する。左側に記載されたものがフィルターの引数に渡されることになる

・複数のフィルターを使用するには、| で区切って他のフィルターを使用することができる。左から右へと引数が順に渡されることになる

・表示の際のデータの加工はなるべくフィルターで定義するようにする


### computedプロパティ

・算出プロパティとも呼ばれ、特定のデータを加工したものをvueインスタンスのプロパティとして公開するもの

// buttonの表示を変える

```

<div id="app">
<button :disabled="button_disabled">{{ button_label }}</button>
</div>


// main.js

const vm = new Vue({
  el: '#app',
  data() {
    return {
      button_disabled: true
    }
  },
  computed: {
    button_label(){
    return this.button_disabled ? "無効" : "有効"
    }
}
})

window.vm = vm

```

・computedプロパティは、データが変更されない限りキャッシュの値を返すようになっている→何度も処理や計算を行わず効率的


### v-onディレクティブとmethodsプロパティ

・v-onディレクティブ：HTML要素で発生したイベントを検知して、主に特定のメソッドを呼び出す際に使用される
ex. buttonがクリックされたらフォームの内容を送信する

・methodsプロパティ：v-onディレクティブによって呼び出されるメソッドを定義しておくもの。methodsプロパティで定義したメソッドはv-onディレクティブ以外からも呼び出すことはできるが、基本的にはv-onとセットで考える

・v-on:click="メソッド名"でクリックした際に当該メソッドを呼び出すことになる

ex. consoleにログを出力する

```

// html

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
  </head>
  <body>
    <div id="app">
      <button v-on:click="clickLog">
        ログ
      </button>
    </div>

    <script src="https://unpkg.com/vue@2.5.21"></script>
    <script src="main.js"></script>
  </body>
</html>

// main.js

const vm = new Vue({
  el: "#app",
  data() {
    return {
      message: "hello world!"
    };
  },
  methods: {
    clickLog() {
      console.log("clicked");
    }
  }
});

window.vm = vm;



```

ex. イベントオブジェクトを受け取って出力(jsのみ、htmlは上と同じ)

```
const vm = new Vue({
  el: "#app",
  data() {
    return {
      message: "hello world!"
    };
  },
  methods: {
    clickLog(event) {
      console.log(event);
      console.log(event.target)
    }
  }
});

window.vm = vm;


```

・method内でもthisを使用することでvueインスタンスにアクセスすることができる

・トリガーにできるイベントは、click以外にもたくさんある

・v-onの省略記法　 v-on:トリガー名="メソッド名"　→ @トリガー名="メソッド名"

```
<button @click="clickLog"></button>
```

### v-ifディレクティブとv-showディレクティブ

・HTML要素の表示、非表示を切り替える際に使用するv-ifディレクティブとv-showディレクティブ

・v-showではcssの値を変更することで表示非表示を変更しているが、v-ifでは要素そのものを削除、作成を行っている

・頻繁に表示、非表示が切り替わる場合は、v-show, ほとんど切り替えが生じない場合は、v-if


// ex. チェックがされている場合に日付入力の項目を表示し、反対では非表示にする(v-showの部分をv-ifにすることでも同様の効果が得られる)

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
  </head>
  <body>
    <div id="app">
      <div>
        <label>
          <input type="checkbox" v-model="reserve">予約する
        </label>
      </div>
      <div v-show="reserve">
        予約日：<input type="date">
      </div>
    </div>

    <script src="https://unpkg.com/vue@2.5.21"></script>
    <script src="main.js"></script>
  </body>
</html>


// main.js

const vm = new Vue({
  el: "#app",
  data() {
    return {
      reserve: false
    };
  },
  methods: {
    clickLog(event) {
      console.log(event);
      console.log(event.target)
    }
  }
});

window.vm = vm;


```

### v-if, v-else-if, v-elseディレクティブを使用した条件分岐

・条件分岐における表示の出しわけについて

・v-if="message" → messageのテキストデータがあるか確認

・v-elseはv-ifから始まる要素に隣り合わせる必要がある


// ex message dataによって表示を分岐

```
<div id="app">
  <div v-if="message">
    {{ message }}
  </div>
  <div v-else-if="message === ''">
    メッセージが空文字です
  </div>
  <div v-else>
    メッセージがありません
  </div>
</div>

//main.js

const vm = new Vue({
  el: "#app",
  data() {
    return {
      message: "hello world!"
    };
  }
});

window.vm = vm;


```

### v-forディレクティブ

・配列のデータをレンダリングする際に使用する

・複数あるデータを一覧表示する際に頻繁に使用される


// ex . todos配列を用いて、todoを表示する

```

<div id="app">
  <h2>Todos:</h2>
  <ol>
    <li v-for="todo in todos">
      <label>
        <input
          type="checkbox"
          v-on:change="toggle(todo)"
          v-bind:checked="todo.done"
        />

        <del v-if="todo.done">
          {{ todo.text }}
        </del>
        <span v-else>
          {{ todo.text }}
        </span>
      </label>
    </li>
  </ol>
</div>

// main.js

const vm = new Vue({
  el: "#app",
  data: {
    todos: [
      { text: "Learn Javascript", done: false },
      { text: "Learn Vue", done: false },
      { text: "Play around Xcode playground", done: true },
      { text: "Learn Swift", done: true }
    ]
  },
  methods: {
    toggle: function(todo) {
      todo.done = !todo.done;
    }
  }
});

window.vm = vm;


```

・keyの指定について、vue.jsが繰り返し処理にて各要素を区別するためにkeyを設定することが推奨されている

・keyは一意に決定されるuniqueなidが好ましい

・以下のようにkeyを指定する。以下では、todoで同じ文言が来る可能性があるのであまりよろしいkeyの設定とは言えない

```
<li v-for="todo in todos" :key="todo.text">
```


### v-modelディレクティブ

・主にフォームの入力値とvueインスタンスのデータを連動させるために使用する

・双方向バインディングという概念：フォームとjavascriptのデータが同期され、双方向に変更が反映されるというもの

ex. フォームの送信

```
<div id="app">
  <div>
    <label>
      <span>名前：</span>
      <input v-model="name" />
    </label>
  </div>
  <div>
    <label>
      <span>メールアドレス：</span>
      <input v-model="email" />
    </label>
  </div>
  <div>
    <label>
      <span>お問い合わせ内容：</span>
      <input v-model="text" />
    </label>
  </div>
  <button @click="submit">送信</button>
</div>

// main.js

const vm = new Vue({
  el: "#app",
  data: {
    name: "ISSEY MIYAKAE",
    email: "test@gmail.com",
    text: "xxxについて"
  },
  methods: {
    submit() {
      const inquiry = `
      次の問い合わせ内容を送信しました。

      [名前]
      ${this.name}
      [メールアドレス]
      ${this.email}
      [お問い合わせ内容]
      ${this.text}
      `;
      alert(inquiry);
    }
  }
});

window.vm = vm;

```

### templateオプション

・Vueインスタンスのオプションにテンプレートを記載できるtemplateオプション

//ex

```
<div id="app"></div>

//main.js

const vm = new Vue({
  el: "#app",
  template: `
  <div v-if="message">
    {{ message }}
  </div>
  <div v-else-if="message === ''">
    メッセージが空文字です
  </div>
  <div v-else>
    メッセージがありません
  </div>
  `,
  data() {
    return {
      message: "hello world!"
    };
  }
});

window.vm = vm;

```


### Vueインスタンスのライフサイクルについて

・Vueインスタンスが作成されて廃棄されるまでの一連のライフサイクル

[ライフサイクルダイアグラム](https://jp.vuejs.org/v2/guide/instance.html)


### ライフサイクルフックで呼び出されるメソッドの定義方法

// ex.ライフサイクルフックで実行される順番で記述してみる

```

<div id="app">
  {{ message }}
</div>


// main.js

const vm = new Vue({
  el: '#app',
  data() {
    return {
      message: 'こんにちは',
      interval_id: null
    }
  },
  beforeCreate() {
    console.log('Vueインスタンス作成前')
  },
  created() {
    console.log('Vueインスタンス作成後')
    this.message = "インスタンスが作成されました"
    
    let seconds = 1
    this.interval_id = setInterval(() => {
      console.log(`${seconds++}秒経過`)
    }, 1000)
  },
  beforeMount() {
    console.log('マウント前')
  },
  mounted() {
    console.log('マウント後') 
  },
  beforeUpdate() {
    console.log('再描画前')
  },
  updated() {
    console.log('再描画後')
  },
  beforeDestroy() {
    console.log('Vueインスタンス削除前')
    
    clearInterval(this.interval_id)
  },
  destroyed() {
    console.log('Vueインスタンス削除後')
  }
})

window.vm = vm

```

・created(){} → この時点でデータを扱うことができる、よくあるのがサーバーと通信して取得したデータをvueインスタンスのデータに格納するケース

・vm.$destroy()した際に、実行中の関数を止めることも上で行っている


### コンポーネント

・画面を構成する要素を個々のUIパーツに分割したもの

・Vue.jsでは個々のコンポーネントがVueインスタンスになっており、コンポーネントの組み合わせによって画面を作成していく

・コンポーネントに分割することで、複数の画面で再利用でき、コードの見通しが良くなるといったメリットがある


### コンポーネントの作成方法

・コンポーネントではvueインスタンスと違い、関数の戻り値でデータのオブジェクトを設定する

・コンポーネントメソッドによるコンポーネントの登録は、Vueインスタンスの生成より前に登録する必要がある

ex
```
<div id="app">
  <user-list></user-list>
</div>

// main.js

Vue.component("user-list", {
  data() {
    return {
      users: [
        { id: 1, name: "ユーザー1" },
        { id: 2, name: "ユーザー2" },
        { id: 3, name: "ユーザー3" },
        { id: 4, name: "ユーザー4" },
        { id: 5, name: "ユーザー5" }
      ]
    };
  },
  template: `
  <ul>
    <li v-for="user in users" :key="user.id">
      {{ user.name }}
    </li>
  </ul>
  `
});

const vm = new Vue({
  el: "#app"
});

```

### コンポーネントの親子構造について

・ルートのVueインスタンスに対して、Vueインスタンスのテンプレートからコンポーネントが呼び出されている

・テンプレートの先頭に複数尾要素が並んでいるとエラーになってしまう → テンプレート直下では一つの要素しか書けない

//ex. テンプレート内にテンプレートを使用する

```
<div id="app">
  <user-list></user-list>
</div>

// main.js

Vue.component("list-title", {
  template: `
  <h2>ユーザーリスト</h2>
  `
});

Vue.component("user-list", {
  data() {
    return {
      users: [
        { id: 1, name: "ユーザー1" },
        { id: 2, name: "ユーザー2" },
        { id: 3, name: "ユーザー3" },
        { id: 4, name: "ユーザー4" },
        { id: 5, name: "ユーザー5" }
      ]
    };
  },
  template: `
  <div>
    <list-title></list-title>
    <ul>
      <li v-for="user in users" :key="user.id">
        {{ user.name }}
      </li>
    </ul>
  </div>
  `
});

const vm = new Vue({
  el: "#app"
});


```

### コンポーネントのローカル登録とグローバル登録

・Vue.componentで登録したコンポーネントはグローバル登録、どこからでも使える → 使わない場合でもコンポーネントの読み込みが発生してしまい、ビルドの時間がかかるなどの弊害あり

・ローカル登録はVueインスタンスのcomponentsオプションで登録する → 登録したvueインスタンス配下でしか使用できない

・ローカル登録では、components: { コンポーネント名: コンポーネントのオブジェクト } の形式で登録する

・ローカル登録の場合は、登録先コンポーネントないのテンプレートからしか呼び出すことができない

・特に理由がなけレバ、ローカル登録が推奨

ex. 上記コードをローカル登録に変更した場合
```
<div id="app">
  <user-list></user-list>
</div>

// main.js

const ListTitle = {
  template: `
  <h2>ユーザーリスト</h2>
  `
};

const UserList = {
  components: {
    "list-title": ListTitle
  },
  data() {
    return {
      users: [
        { id: 1, name: "ユーザー1" },
        { id: 2, name: "ユーザー2" },
        { id: 3, name: "ユーザー3" },
        { id: 4, name: "ユーザー4" },
        { id: 5, name: "ユーザー5" }
      ]
    };
  },
  template: `
  <div>
    <list-title></list-title>
    <ul>
      <li v-for="user in users" :key="user.id">
        {{ user.name }}
      </li>
    </ul>
  </div>
  `
};

const vm = new Vue({
  el: "#app",
  components: {
    "user-list": UserList
  }
});



```

### 親コンポーネントからこコンポーネントへのデータの伝播

・基本的に、コンポーネント内で定義したデータは他のコンポーネントから参照したり、書き換えたりすることができない → propsを用いて親コンポーネントから子コンポーネントにデータを渡す必要がある

・親コンポーネントからデータを受け取るprops → データを直接変更できないという特徴がある


// ex. 

Object型のuserというプロパティを親コンポーネントから受け取ることができる

```
<div id="app">
  <user-list></user-list>
</div>

// main.js

const ListTitle = {
  template: `
  <h2>ユーザーリスト</h2>
  `
};

const UserDetail = {
  props: {
    user: {
      type: Object
    }
  },
  template: `
  <div>
    <h2>選択中のユーザー</h2>
    {{ user.name }}
  </div>
  `
};

const UserList = {
  components: {
    "list-title": ListTitle,
    "user-detail": UserDetail
  },
  data() {
    return {
      users: [
        { id: 1, name: "ユーザー1" },
        { id: 2, name: "ユーザー2" },
        { id: 3, name: "ユーザー3" },
        { id: 4, name: "ユーザー4" },
        { id: 5, name: "ユーザー5" }
      ],
      selected_user: {}
    };
  },
  template: `
  <div>
    <list-title></list-title>
    <ul>
      <li v-for="user in users" :key="user.id" @click='selected_user = user'>
        {{ user.name }}
      </li>
    </ul>
    <user-detail :user='selected_user'></user-detail>
  </div>
  `
};

const vm = new Vue({
  el: "#app",
  components: {
    "user-list": UserList
  }
});


```

### sync修飾子を使用したデータの渡し方

・v-bindにつけることで書き方を簡略化することができる

```
<script src="https://unpkg.com/vue@2.5.21"></script>

<div id="app">
  <user-detail></user-detail>
</div>

// main.js

const UserForm = {
  template: `
    <div>
      <div>ユーザー名変更フォーム</div>  
      <input v-model='user_name' />
      <button @click='update'>名前変更</button>
    </div>
  `,
  props: {
    userName: { type: String, required: true }
  },
  data() {
    return {
      user_name: this.userName
    }
  },
  methods: {
    update () {
      this.$emit('update:userName', this.user_name)
    } 
  }
}

const UserDetail = {
  components: {
    'user-form': UserForm
  },
  data() {
    return {
      user_name: 'ヤマダ タロウ'
    }
  },
  template: `
    <div>
      <div>
        <span>ユーザー名： {{ user_name }}</span>
      </div>
      <div>
        <user-form :user-name.sync='user_name'></user-form>
      </div>
    </div>
  `
}

const vm = new Vue({
  el: '#app',
  components: {
    'user-detail': UserDetail
  }
})
```

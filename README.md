# vue_js_tips
vue.jsに関する基礎知識をまとめる


## 読み込み

CDNを用いる場合は、scriptタグで囲む
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

 

#### elオプション: 

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

#### dataオプション:

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


#### $watchメソッド

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


#### watchオプション

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





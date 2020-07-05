## 環境構築

### node.js

https://nodejs.org/ja/
より、LTS(Long Term Support)版をインストールすることを推奨。

node.js のバージョンを上げたい場合

> npm install --global yarn

### Yarn

node.js をインストールした時点で npm というパッケージマネージャーはインストールされている。
しかし Yarn の方が高速かつ信頼度が高い。

> npm install --global yarn

### create-react-app

React を使用して開発を行う場合、従来は Babel や Webpack などをマニュアルでインストールするのが面倒だった。
こういった手間を減らす為に作成されたツールが create-react-app。

> yarn global add create-react-app

> create-react-app ../現在のディレクトリ

### react.fragment

div を付けたくない時に使う

### jsx

jsx はそのままでは変換できず、トランスパイラによって変換する必要がある。
その変換作業をトランスパイリング、変換することをトランスパイルという

jsx は内部的に babel によって javascript にトランスパイルしてくれている。
ES6 だと const を babel が var に変換してくれる。

### webpack

webpack はモジュールバンドラと呼ばれる。
ソースコードを束ねてブラウザで実行できる静的な js ファイルを出力するもの

app.js
bar.js

webpack.config.js
entry: app.js
output bundle.js

page.html で bundle.js に指定して使用。

### component

関数の定義によって作成する functional コンポーネント

クラス定義によって作成する class コンポーネント

これはクラスコンポーネント

```js
class App extends Component {}
```

これは関数コンポーネント

```js
const App = () => {
  return <Cat />;
};

const Cat = () => {
  return <div>Meow!</div>;
};
```

### props

コンポーネントの属性の事
props.name のような使い方
数値や配列、関数などで使用できる。

```js
const App = () => {
  return <User name={'Taro'} />;
};

const User = (props) => {
  return <div>Hi! I am {props.name}</div>;
};
```

のように使う

```js
return (
  <div>
    <User name={'Taro'} age={10} />
    <User name={'Hanako'} age={5} />
  </div>
);
```

この書き方は重複が多いので、

```js
const App = () => {
  const profile = [
    { name: 'Taro', age: 10 },
    { name: 'Hanako', age: 5 },
  ];
  return (
    <div>
      {profile.map((profile) => {
        return <User name={profile.name} age={profile.age} />;
      })}
    </div>
  );
};
```

のようにもかける。

react では virtualdom というものが存在していて、
変更した箇所のみ変更が反映される。
key を指定するには以下の通り。

```js
profile.map((profile, index) => {
  return <User name={profile.name} age={profile.age} key={index} />;
});
```

react の props にはデフォルト props というモノが存在する。
親のコンポーネント App から、子のコンポーネント User に値を渡す時、props が使用できる。

```js
User.defaultProps = {
  age: 2,
};
```

{ name: "Taro", age: 10 },
{ name: "Hanako", age: 5 },
{ name: "NoName" }→age が指定されない時、初期値で 2 が入る

### prop-types

プロパティの型のタイプを定義する方法

```js
User.propTypes = {
  name: PropTypes.string,
  age: PropTypes.number,
};
```

{ name: "Hanako", age: "5" }

age は数値型なので、コンソールにエラーがでる。

age を必須にする書き方。

```js
age: PropTypes.number.isRequired;
```

### state

react のコンポーネントではコンポーネント内部で状態を持つことができる。この状態を state と呼ぶことができる。

props がコンポーネントの外から値を渡されたのに対して、state はクラスコンポーネント内部でのみ使用される。

props は変更不可(imutable)
state は変更可能(mutable)

確認方法
console.log(this.state)

state に状態を持たせるには
コンポーネントの初期化時に使用される constructor メソッドを使用する。constructor は props を受け取ることができる。

```js
constructor(props) {
super(props)
this.state
}
```

return で返す jsx は親が一つでなければならない。
だから、div が react.fragment でくくる。

初期化時に
this.state = { count: 0 }
で状態を直接変更したが、
これは constructor 以外で使ってはいけない。

constructor 以外で state を変える場合は setState を使用する。

setState が走ると自動的に render()が走り、再描画が走る。

### redux

コンポーネントの階層が大きくなったときに状態を共有しやすくする。

コンベンション：redux アプリケーションをコーディングする際の習慣

> yarn add redux react-redux

### action

action アプリケーションの中で何が起きたかということを示すデータの事、javascript の object

type というキーと、type に対応する値を持つのが特徴。
type の値はユニークである必要がある。

INCREMENT 側の action

```js
{
  type: 'INCREMENT';
}

{
  type: 'DECREMENT';
}
```

action を返す関数を action creator という。

```js
const increment = () => {
  return {
    type: 'INCREMENT',
  };
};
const decrement = () => {
  return {
    type: 'DECREMENT',
  };
};
```

もしくはこういう書き方

```js
const increment = () => ({
  type: 'INCREMENT',
});

const decrement = () => ({
  type: 'DECREMENT',
});
```

action creator を export して view を担当するコンポーネント側で import する。
あるイベントを掴んだ時に当該の action creator を invoke して
適切な状態遷移を実行させるための仕組み。

### reducer

redux の基本機能の一つ
action 発生時に、そのアクションの type に応じてどう状態(state)を変化させるのか。

reducers フォルダの index.js は全ての reducer を一つに結合する役割。
結合を行う為の機能が redux には存在するので import する。
結合したものを後で利用するので、export しておく。
reducer は関数として定義し、引数は二つ持つ。
default 時には state は値を持たないので、initialState を使用する。

```js
import { combineReducers } from 'redux';

export default combineReducers({ foo, bar, baz });

const initialState = { value: 0 };
export default (state = initialState, action) => {};
```

### store

store を作成する為の関数である createstore が redux にはあるので import

作成した store を全てのコンポーネントを渡す機能を持つ provider と呼ばれる特殊なコンポーネントが react-redux にはあるので import

store はアプリケーション内部で一つのものであり、state は store に集約されている。
const store = createStore(reducer)

store がアプリケーションのどのコンポーネントからも参照できるようにするのが provider

既存のコンポーネントを provider コンポーネントでラップして、store という属性に store を渡すと、アプリケーション内部の全階層の内部で store が利用可能になる。
この仕組みにより、渡したいデータを親から子から孫まで、props を使用する必要があった。
provider を使えばこの問題が解決する。

```js
<Provider store={store}>
  <App />
</Provider>,
```

### connect

react-redux が提供する connect 関数を利用して、state や action と component の関連付けを行い、view のイベントで状態を遷移させ遷移後の値を画面に再描画する。

constructor はコンポーネントの初期化時に実行されるコールバックで
コールバック内で状態を初期化していたが、redux ではその働きを reducer で行う為、
コンストラクタ自体が不要。

また、ボタンクリックイベント時に行っていた setstate による状態変化は、
actioncreator で処理の名称を確保し、そこから reducer 内の状態変化を呼ぶ為、
不要。

インスタンスの props には状態や action を渡していくので、props を変数に入れておく。state と action をコンポーネントに関連付ける。

connect 関数を使って、引数にコンポーネントを渡す。
export default connect(mapStateToProps, mapDispatchToProps)(App)

### mapStateToProps 　

state の情報からコンポーネントで必要なものを取り出し、コンポーネント内の props として mapping する機能を持つ関数
引数には、状態の toplevel を示す state を買いて、どういったオブジェクトを props として対応させるのかを戻り値として書く。

### mapDispatchToProps 　

ある action が発生したときに、reducer にタイプに応じた状態遷移を実行させるための関数が dispatch。この dispatch 関数を引数において宣言する。
このコンポーネントではボタンのクリック時に該当アクションを dispatch に渡して実行させることで状態遷移をさせることができる。
increment をキーに、increment 関数を引数にもつ dispatch 関数を値に定義した mapDispatchToProps を定義する。

今回は import した increment を dispatch の引数に渡す。

```js
import { increment, decrement } from '../actions';

const mapDispatchToProps = (dispatch) => ({
  increment: () => dispatch(increment()),
  decrement: () => dispatch(decrement()),
});
```

こんな書き方もできる。

```js
const mapDispatchToProps = { increment, decrement };
```

### axios

axios 　外部のサーバに http リクエストを送る

### redux-thunk

https://github.com/reduxjs/redux-thunk#motivation
actioncreator で非同期処理を実行する。
actioncreator が action の変わりに関数を返すことができるようになる。

今までは action を返していたが、
関数を返せるようになる。内部の関数は dispatch と getstate を引数に渡せる。

```js
export const readEvents = () => ({
  type: READ_EVENTS,
});

export const readEvents = () => () => ({});
```

コンポーネントにはマウントされた時に呼ばれる componentDidMount() がある。

redux-thunk はミドルウェアに該当するもので、middleware を適用するための関数を redux からインポートする。(applyMiddleware)

createStore の第二引数に applyMiddleware(thunk)を渡すことで、store に組み込まれる。

axios はそのままだと戻り値が promise になってしまうので、async、await を使う。

### json

[
{id: 1, title: "Let's have an event 1!", body: "This is the body for event 1."}
]

上記のような json(配列構造で、それぞれの要素がオブジェクトになっている)だと、state で管理しづらい。id=2 の要素を取り出す際に、全ての要素をフルスキャンしなければいけない。
{
1: {id: 1, title: "Let's have an event 1!", body: "This is the body for event 1."}
}
こんな構造で、各オブジェクトが独立していると管理しやすい。lodash で実現可能。各要素のオブジェクトの id の値をキーに持つ一つのオブジェクトの塊。

import \_ from 'lodash'

console.log(action.response.data)
console.log(\_.mapKeys(action.response.data, 'id'))

### react-router-dom

画面遷移の為のリンク機能を持つ

入力フオーム
redux-form

<Link to="/events/new">New Events</Link>
LinkはRouterでラップする必要がある。

<Provider store={store}>
  <BrowserRouter>
    <Switch>
      <Route exact path="/" component={EventsIndex} />
    </Switch>
  </BrowserRouter>
  {/* <EventsIndex /> */}
</Provider>,

redux form
https://redux-form.com/8.3.0/docs/gettingstarted.md/#getting-started-with-code-redux-form-code-

①Form Reducer
Redux Form が提供する Reducer。
CreateStore に渡して使う。

②Form Component
Redux Form が提供する Form ComponenF という関数がある。
この関数を使って作られる ContactForm をコンポーネントとして使う。

③ フィールドコンポーネント
これを使って入力フォームを作る。

④submit ボタン

renderField()　このメソッドに入力する値がわたってくる

### React v16.3 Context API

https://github.com/ProgrammingSamurai/react-recipes/tree/master/react-context-api

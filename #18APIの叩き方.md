# APIの叩き方
### EsLintの導入(クローン版ではすでに導入済みなので必要ない)
> Next.jsのヴァージョンが上がり、Next.js用のEsLintができたのでそれを導入する
> next.js eslintで検索　”https://nextjs.org/docs/basic-features/eslint”
```
"scripts": {
  "lint": "next lint"
}
```
の"lint": "next lint"をコピー
- package.jsonに貼り付け
```js
"scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
```
> next.jsを最新版にヴァージョンアップ No.11以上にする。
> % yarn add next@latest
>としてyarn lint
-  % yarn lintとすると
> yarn add --dev eslint-config-nextをインストールするように言われるので
-  % yarn add --dev eslint-config-nextを入力
> package.jsonに
```js
"devDependencies": {
    "eslint": "^7.29.0",
    "eslint-config-next": "^11.0.1"
  }
```
が追加される
> ここでもう一度 % yarn lintを入力すると
- eslintrcファイルが生成される
```
{
  "extends": "next"
}
```
- api と　FooterでWarningが出ているので
  - api
> pagesフォルダのapiをフォルダごと削除する(今はまだ必要ないので)
  - Footer
```js
Thanks by.{' '}
  <img src="/vercel.svg" alt="Vercel Logo" className={classes.logo} />
```
> の部分は今は必要がないのでEsLintを無効化しておく
```js
<img src="/vercel.svg" alt="Vercel Logo" className={classes.logo} />
```
> を選択して"コマンドキー"　＋　”ピリオドキー”
- 一行目の
> eslint-disable-next-line @next/next/no-img-elementを選択して
```js
Thanks by.{' '}
  {/* eslint-disable-next-line @next/next/no-img-element */}
  <img src="/vercel.svg" alt="Vercel Logo" className={classes.logo} />
```
- として、EsLintのエラーを消す。
---
- ここまでを一旦コミット
  - ⬆️ Next.jsのアップグレードとEsLintの導入
  -  🔥不必要なファイルの削除
  -  🚨EsLintのエラーの解決
---



## パッケージを使わず自力でAPIを叩く記述方法(ここから始める)
### index.jsxでローカルホストの起動
### indexの不要部分の削除
```js
 {props.isShow ? <h1>{props.count}</h1> : null}
      <button href="/about" onClick={props.handleClick}>
        ボタン
      </button>
      <button onClick={props.handleDisplay}>
        {props.isShow ? "非表示" : "表示"}
      </button>

      <input type="text" value={props.text} onChange={props.handleChange} />
      <button onClick={props.handleAdd}>追加</button>
      <ul>
        {props.array.map((item) => {
          return <li key={item}>{item}</li>;
        })}
      </ul>

      <Main page="index" />

      <Footer />
```
>この部分を全て削除
- index,aboutの位置調整
  - webツールズを開いて確認
  - justify-content: center
> Home.module.cssからjustify-content: centerを削除
### {JSON}Placeholderを利用してAPIを叩く
- Resources の　/postsを使う
>　stateを用意して、非同期処理を行なって取得した結果を入れていき、それをreturn に入れて表示させる
- まずは、stateの準備
  - 初期値は空配列
```js
const Home = (props) => {
  const [posts, setPosts] = useState([]);
```
<!-- react-resはここから -->

- 次にAPIの叩き方
- その下に
```js
const getPosts = useCallback(() =>{
		
	},[]);
```
> として、
- さらに、非同期なのでasync, awaitをつけて、ブラウザが持っている非同期を叩くための関数fetchを使う
- - async,awaitでfetchを使うとResponseオブジェクトが帰ってくる
  
```js
const getPosts = useCallback(async () => {
    const res = await fetch("https://jsonplaceholder.typicode.com/posts");

},[]);
```
- とすると、APIをみてくれるので、それをjsonという使いやすい形式に変換する
```js
const json = await res.json();
```
- getPostsを呼び出すところが必要なので
```js
useEffect(() => {
    getPosts();
  }, [getPosts]);
```
- として、logで一度確認する
```js
const Home = (props) => {
  const [posts, setPosts] = useState([]);
// useState([]);で[]空配列を忘れると最後のmapが回らずエラーとなる
  const getPosts = useCallback(async () => {
    const res = await fetch("https://jsonplaceholder.typicode.com/posts");
    const json = await res.json();
    console.log(json);
  }, []);
  useEffect(() => {
    getPosts();
  }, [getPosts]);
  ```
> consoleを確認すると100件のPostsがjsonに取得できている
- この取得できた、postsをjsonにいれる
  - console.log(json); を setPosts(json);に変える
```js
const getPosts = useCallback(async () => {
    const res = await fetch("https://jsonplaceholder.typicode.com/posts");
    const json = await res.json();
    setPosts(json);
  }, []);
```
> とすると、setPostsに取得したjsonが入る
- それをブラウザに表示する
```js
<ol>
  {posts.map((post) => {
  return <li key={post.id}>{post.title}</li>;
  })}
</ol>
```
> これで表示される。
- return の上にconsole.log(posts);を記述すると、最初に空配列が入っているのが確認できる。
- 
---
- ここで、prettierを効かせる
  - setting.jsonに
  ```js
  "editor.formatOnSave": true
  ```
  を追加記述

---
### ここまでの説明

> このコンポーネントが呼び出されたら、
```
useEffect(() => {
    getPosts();
  }, [getPosts]);
```
- useEffectの中で、getPosts()が呼び出される
```js
const getPosts = useCallback(async () => {
    const res = await fetch("https://jsonplaceholder.typicode.com/posts");
```
- getPostsの中でfetchをすることによって、res(レスポンス)が返ってくる。
- それをアプリケーション側で使いたいjsonの形式に変換する
```js
const json = await res.json();
    setPosts(json);
```
>　そうすることによって、配列にデータが入っている状態になるので、それを
```
  const [posts, setPosts] = useState([]);
```
> のpostsのステートに入れる。
- これで、postsが空配列から、データが100件入った状態になる。
---
> postsが０より大きければ表示させる
```js
  <ol>
    {posts.map((post) => {
       return <li key={post.id}>{post.title}</li>;
    })}
  </ol>
```
> postsが１以上になった時に、postsを表示させるために
```js
{posts.length > 0 ? (
  <ol>
    {posts.map((post) => {
       return <li key={post.id}>{post.title}</li>;
    })}
  </ol>
  ) : null };
```
> とする
---
> コミット
- 🔧 Vscode保存時に自動整形される様に変更
- ✨ APIを叩いてみました















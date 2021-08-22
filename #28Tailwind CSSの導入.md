### Tailwind CSS の導入
- "editor.quickSuggestions": { "strings": true }の設定
#### Tailwind CSS の統合

> Tailwind CSS の検索 https://tailwindcss.com/の実行

- get started → installation → next.js
- Install Tailwind via npm から
- - tailwindcss@latest postcss@latest autoprefixer@latest のコピー
- - ターミナルに yarn add -D tailwindcss@latest postcss@latest autoprefixer@latest 　と入力
    > これが完了すれば　 Create your configuration files の npx tailwindcss init -p 　をコピー
- ターミナルに npx tailwindcss init -p を入力
  > これで tailwind.config.js と postcss.config.js が作られている
- Configure Tailwind to remove unused styles in production から
  > purge: ['./pages/**/*.{js,ts,jsx,tsx}', './components/**/*.{js,ts,jsx,tsx}'],の部分をコピーしえて
- tailwind.config.js に貼り付け　 pureg: [],は削除する
- さらに不要な部分を削除して purge: ['./src/**/*.{js,ts,jsx,tsx}'], とする
  > Import Tailwind directly in your JS 　から　 import 'tailwindcss/tailwind.css'を pages/\_app.jsx にインポートする必要がある
- \_app.jsx の既存の CSS はいらないので一旦コメントアウト　// import "src/styles/globals.css";
  > ここで　 yarn dev Tailwind が効いている状態になっている
- Tailwind には JIT モードというものがある
- - tailwind.config.js に mode:"jit", と記述

```js
module.exports = {
	mode: "jit",
	purge: ["./src/**/*.{js,ts,jsx,tsx}"],
	darkMode: false, // or 'media' or 'class'
	theme: {
		extend: {},
	},
	variants: {
		extend: {},
	},
	plugins: [],
};
```

> ターミナル終了して、再度　 yarn dev

- \_app.jsx で確認

```js
<Layout>
	<div className="text-red-500">test</div>
	<Component {...pageProps} />
</Layout>
```

> とすると、test が赤字になっているのが確認できる

```js
<div className="text-red-500">test</div>
```

> の部分は削除

---

> コミット

- ➕ Tailwind CSS 用のセットアップ

---

#### 既存のものと置き換えていく

- styles フォルダ自体がいらないので削除

#### Layout のセットアップ

```jsx
return <div className={classes.container}>{props.children}</div>;
```

> の部分を一旦下のようにして、tailwind で css を当てていく

```jsx
return <div>{props.children}</div>;
```
> Layout.module.css　から下の部分をコピーして、Layout/index,jsxにコメントアウトして貼り付け
```js
  min-height: 100vh;
  max-width: 600px;
  margin: 0 auto;
  padding: 0 0.5rem;
  display: flex;
  flex-direction: column;
  align-items: center;
```
> Layout.module.cssを削除
> import classes from "src/components/Layout/Layout.module.css";の部分も削除
```js
<div className="flex flex-col items-center mx-auto px-2 min-h-screen max-w-2xl ">{props.children}</div>;
```
> とする
---
> TailWindの補完が効かなかったので、
- tailwind.config.jsに
- setting.jsonに補完を効かせたい拡張子を指定
"tailwindCSS.includeLanguages": {
        "plaintext": "vue"
}
> 貼り付けたら補完が効くようになった
---
- 次にHeader部分
- Header.module.cssから
```js
  width: 100%;
  height: 100px;
  border-bottom: 1px solid #eaeaea;
  display: flex;
  justify-content: center;
  align-items: center; 
```
> を切り取り、Header/index.jsxにコメントアウトで貼り付け
- Header/index.jsxの<header className={classes.header}>を書き換え
- - <header className="flex justify-center items-center border-b min-w-full h-24">とする
> .anchor {
  padding: 8px 24px;
  display: inline-block;
  font-size: 1.2rem;
} 部分の変更
```js
  <a className={classes.anchor}>{item.label}</a>の部分を
  <a className="inline-block py-2 px-6 text-xl">{item.label}</a> 
```
> に変更
> .anchor:hover,
.anchor:focus,
.anchor:active {
  color: #0070f3; 
}の部分の変更
```js
<a className="inline-block py-2 px-6 text-xl hover:text-blue-500 focus:text-blue-500 active:text-blue-500">{item.label}</a>となる
```
> import classes from "src/components/Header/Header.module.css";は削除
> Header.module.cssも削除
---
> コミット
- 💄 TailWind Cssを使って既存のスタイルの書き換え





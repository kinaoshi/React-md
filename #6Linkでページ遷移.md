# #6

## Link コンポーネントを使ってページ遷移

### componentsの下層に Header.js の作成

- 下のように記述

```
export function Header() {
  return (
    <header>
      <a href="/">Index</a>
      <a href="/about">About</a>
    </header>
  );
}
```
* index.js,about.jsでHeaderの呼出
  * これでindex,aboutが表示される
* indexページのディベロッパーツールで背景色をアクアマリンに変更して、aboutページに遷移させる
  * aboutページでは背景色は読み込まれていない
  * これを解決するのがLinkコンポーネント
### Linkコンポーネントの実装
*  header.jsにLinkコンポーネントのインポート
```js
import Link from "next/link";
```
return以下の部分を
```js
<header>
      <Link  href="/">
      <a>Index</a>
      </Link>
      <Link  href="/about">
      <a>About</a>
      </Link>
```
とする
---
### スタイリング
* Header.module.cssの作成
* Header.jsでHeader.module.cssの読み込み
```js
import classes from "./Header.module.css";
```
* クラスネームをheaderとする
```js
<header className={classes.header}>
```
* cssの記述
```js
.header {
  width: 100%;
  height: 100px;
  border-bottom: 1px solid #eaeaea;
  display: flex;
  justify-content: center;
  align-items: center;
}
```
 * classNameはaタグにも当てる
```js
.anchor {
  padding: 8px 24px;
  display: inline-block;
  font-size: 1.2rem;
}
.anchor:hover,
.anchor:focus,
.anchor:active {
  color: #0070f3;

}
```
- この場合、Linkではなくaタグにanchorを当てる
```js
export function Header() {
  return(
    <header className={classes.header}>
      <Link  href="/">
      <a className={classes.anchor}>Index</a>
      </Link>
      <Link  href="/about">
      <a className={classes.anchor}>About</a>
      </Link>

    </header>
  )
}
```
cssは最終的に
```js
.header {
  width: 100%;
  height: 100px;
  border-bottom: 1px solid #eaeaea;
  display: flex;
  justify-content: center;
  align-items: center;
}
.anchor {
  margin-left: 8px;
  border: 1px solid #ee8686;
  border-radius: 5px;
  color: rgb(131, 28, 73);
  display: inline-block;
  padding: 8px 24px;
  font-size: 1.2rem;
}
.anchor:hover,
.anchor:focus,
.anchor:active {
  color: #f30000;
  border-color: #0070f3;
}
```
とした
---
> Linkコンポーネントについては
> https://nextjs.org/docs/api-reference/next/link
> を参照
---
コミット
コミット名 - ✨ ヘッダーの追加







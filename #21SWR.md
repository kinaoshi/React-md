## SWRを使った非同期処理
- SWR ライブラリのドキュメントからインストール
- Posts/index.jsxのページから
  - index.jsx/postsに % yarn add swr
  - useSWR をインポート
    - import useSWR from 'swr'
  - const { data, error } = useSWR('/api/user/123', fetcher)をコピー
  - export const Posts = () => {　の次に貼り付け
    - fetcherは一旦省略
```js
export const Posts = () => {
  const { data, error } = useSWR('/api/user/123')
  const [state, dispatch] = useReducer(reducer, initialState);
```
> '/api/user/123'の部分をAPIの"https://jsonplaceholder.typicode.com/posts"に変更
- ここで、{ data, error }に何が入っているかlogで確認
```js
const { data, error } = useSWR("https://jsonplaceholder.typicode.com/posts")
  console.log({ data, error });
```
---
> SWRのバージョンが更新され、今までの方法では、動かなくなっている。
- 対策1
- - useSWRの第２引数に入れる
```js
const fetcher = (...args) => fetch(...args).then(res => res.json())
```
- 対策2
  - 全部のuseSWRに設定するのは面倒なので、SWRConfigでデフォルトで設定する。
```js
import { SWRConfig } from 'swr'

const MyApp = ({ Component, pageProps }) => {
  return (
    <SWRConfig 
      value={{
        fetcher: (resource, init) => fetch(resource, init).then(res => res.json())
      }}
    >
      <Head>
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <Layout>
        <Component {...pageProps} />
      </Layout>
    </SWRConfig>
  );
};

export default MyApp;
```
> こんな感じ！
---

- これで、console.log({ data, error });として、data,error に何が入っているかを確認
> 次をコメントアウト
```js
// const [state, dispatch] = useReducer(reducer, initialState);
  // const getPosts = useCallback(async () => {
  //   try {
  //     const res = await fetch("https://jsonplaceholder.typicode.com/posts");
  //     if (!res.ok) {
  //       throw new Error("エラーが発生したため、データの取得に失敗しました。");
  //     }
  //     const json = await res.json();
  //     dispatch({ type: "end", data: json });
  //   } catch (error) {
  //     dispatch({ type: "error", error });
  //   }
  // }, []);
  // useEffect(() => {
  //   getPosts();
  // }, [getPosts]);
```
> 次にif文の書き換え
```js
if (state.loading) {
    return <div>ローディング中</div>;
  }
```
> の部分を
```js
if (!error && !data) {
    return <div>ローディング中</div>;
  }
```
> エラーがなく、かつ、dataが無い状態に
```js
if (state.error) {
    return <div>{state.error.message}</div>;
  }
```
> の部分を
```js
if (error) {
    return <div>{error.message}</div>;
  }
```
> に
```js
if (state.data.length === 0) {
    return <div>データは空です</div>;
  }
```
> の部分を
```js
if (data.length === 0) {
    return <div>データは空です</div>;
  }
```
> に
```js
return (
    <ol>
      {state.data.map((post) => {
        return <li key={post.id}>{post.title}</li>;
      })}
    </ol>
  );
```
> の部分を
```js
return (
    <ol>
      {data.map((post) => {
        return <li key={post.id}>{post.title}</li>;
      })}
    </ol>
  );
```
> に変更するとlogも３つあったものが２個に減っている
- 不要部分のコメントアウト
```js
// import { useCallback, useEffect, useReducer } from "react";

// const initialState = {
//   data: [],
//   loading: true,
//   error: null,
// };
// const reducer = (state, action) => {
//   switch (action.type) {
//     case "end":
//       return {
//         ...state,
//         data: action.data,
//         loading: false,
//       };
//     case "error":
//       return {
//         ...state,
//         loading: false,
//         error: action.error,
//       };
//     default: {
//       throw new Error("no such action type!");
//     }
//   }
// };
```
```js
// import { useCallback, useEffect, useReducer } from "react";

// const initialState = {
//     data: [],
//     loading: true,
//     error: null,
// }
// const reducer = (state, action) => {
//   switch (action.type) {
//     case "end":
//       return {
//         ...state,
//         data: action.data,
//         loading: false,
//       };
//     case "error":
//       return {
//         ...state,
//         loading: false,
//         error: action.error,
//       };
//     default:
//       throw new Error("no such action type!");
//   }
// }
```
```js
// const [state, dispatch] = useReducer(reducer, initialState);
```
> コメントアウト部分の削除、不要部分の削除
- - -
### クイックスタート
> const fetcher = (...args) => fetch(...args).then(res => res.json())の部分について
```js
useSWR("https://jsonplaceholder.typicode.com/postsa");
```
> のところで、エラーを発生させるとブラウザでは、エラーとなり、エラーメッセージは表示されない
> 上の、httpをコマンド+クリックすると{}の空配列になっているのでmapのところでエラーになる
- これを回避するためにimport の次に
```js
const fetcher = async (url) => {
  const response = await fetch(url);

  if (!response.ok) {
  throw new Error("エラーが発生したため、データの取得に失敗しました。")
  }
  const json = await response.json();
  return json;
};
```
> を記述して
```js
const { data, error } = useSWR("https://jsonplaceholder.typicode.com/postsaaa");
```
> の第２引数にfetcherを入れる
```js
const { data, error } = useSWR("https://jsonplaceholder.typicode.com/postsaaa", fetcher);
```
> とエラーメッセージが表示される
---
### データが空の時の表示を確認する方法
- APIのIDに100以上のものを確認させる
```js
useSWR("https://jsonplaceholder.typicode.com/posts?id=101", fetcher);
```
> 101以上のデータはないので"データは空です"が表示される
---
### useSWR 自体をカスタムフックを使ってコンポーネント化
- 使いまわしやすくする
  - exportの上に
```js
const usePosts = () => {

}
```
> を記述し、その中に
```js
const { data, error } = useSWR("https://jsonplaceholder.typicode.com/posts", fetcher);
```
> を貼り付け、その下にreturn 文を記述
```js
const usePosts = () => {
  const { data, error } = useSWR("https://jsonplaceholder.typicode.com/posts", fetcher);

  return {
    data,
    error,
    isLoading: !error && !data,
    isEmpty: data && data.length === 0,
  };
};
```
> と記述する。
- 次にusePostsの記述
```js
export const { data, error } = useSWR("https://jsonplaceholder.typicode.com/posts", fetcher);
```
> を
```js
  export const Posts = () => {
  const { data, error, isLoading, isEmpty } = usePosts()
```
> に書き換えif文も
```js
if (isLoading) {
    return <div>ローディング中</div>;
  }
  if (error) {
    return <div>{error.message}</div>;
  }
  if (isEmpty) {
    return <div>データは空です</div>;
  }
```
> に書き換える
---
### usePostsを他のコンポーネントでも使える様にする
- hooksファイルの中にusePosts.jsxを作成
```js
import useSWR from "swr";

const fetcher = async (url) => {
  const response = await fetch(url);

  if (!response.ok) {
  throw new Error("エラーが発生したため、データの取得に失敗しました。")
  }
  const json = await response.json();
  return json;
};

export const usePosts = () => {
  const { data, error } = useSWR("https://jsonplaceholder.typicode.com/posts", fetcher);

  return {
    data,
    error,
    isLoading: !error && !data,
    isEmpty: data && data.length === 0,
  };
};
```
> として、Postsフォルダのindex.jsxを
```js
import { usePosts } from "src/hooks/usePosts";

export const Posts = () => {
  const { data, error, isLoading, isEmpty } = usePosts();


  if (isLoading) {
    return <div>ローディング中</div>;
  }
  if (error) {
    return <div>{error.message}</div>;
  }
  if (isEmpty) {
    return <div>データは空です</div>;
  }

  return (
    <ol>
      {data.map((post) => {
        return <li key={post.id}>{post.title}</li>;
      })}
    </ol>
  );
};
```
> とするとusePostsが使いまわしやすくなる。
---
- コミットからプッシュ
  - ➕ Add SWR...yarn.json
  - 🎨  Replace to SWR
-  デプロイ














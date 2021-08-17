## useReducer の使い方

1. useState をまとめる

```js
const [posts, setPosts] = useState([]);
const [loading, setLoading] = useState(true);
const [error, setError] = useState(null);
```

> この部分を簡略化して書く 2. useReducer の使い方を学ぶ
> useReducer は考え方が大切、Redux にもよく使われるのでその予習として

---

### useState をまとめる

- レンダリングの回数を確認する
  > if (loading) {return ＜ div>ローディング中</div ＞;
  > }文の前に

```js
console.log("foo");
```

> を記述して確認すると３回レンダリングされているのが確認できる

- ３回表示されている"foo"を１回減らす
  > まとめる

```js
// const [posts, setPosts] = useState([]);
// const [loading, setLoading] = useState(true);
// const [error, setError] = useState(null);
```

- ここをコメントアウト
- その下に
  ```js
  const [state, setState] = useState({
    data: [],
    loading: true,
    error: null,
  });
  ```
  > として、setState を使って、オブジェクトの値を書き換える

```js
if (loading) {
  return <div>ローディング中</div>;
}
if (error) {
  return <div>{error.message}</div>;
}
if (posts.length === 0) {
  return <div>データは空です</div>;
}
return (
  <ol>
    {posts.map((post) => {
      return <li key={post.id}>{post.title}</li>;
    })}
  </ol>
);
```

> の部分は、

```js
if (state.loading) {
  return <div>ローディング中</div>;
}
if (state.error) {
  return <div>{state.error.message}</div>;
}
if (state.data.length === 0) {
  return <div>データは空です</div>;
}

return (
  <ol>
    {state.data.map((post) => {
      return <li key={post.id}>{post.title}</li>;
    })}
  </ol>
);
```

> と書き換える。このままでは、表示はエラーとなるので

```js
const json = await res.json();
      // setPosts(json);
    } catch (error) {
      // setError(error);
    }
    // setLoading(false);
```

> としてエラー表示を消す

```js
const json = await res.json();
setState(prevState => {
  return {
    data: json,
    loading: false,
    error: null,
  };
});
// setPosts(json);
```

> と記述すると、データが取得でき、foo の繰り返しが２回になり、一回減っているのが確認できる。

- オブジェクトが複数ある時

```js
data: json,
loading: false,
error: null,
```

> スプレット演算子を使って、書き直したいところだけを書き直す手法

```js
setState((prevState) => {
  return {
    ...prevState,
    data: json,
    loading: false,
    // error: null,
  };
});
```

> とする。エラー部分の記述についても

```js
catch (error) {
  setState((prevState) => {
    return {
      ...prevState,
      loading: false,
      error: error,
    };
  });
  // setError(error);
}
```

> エラーを発生させて確認する。

- キーと同じ値の場合省略できる
  - error: error,は

```js
error,
```

> とできる

---

2. useReducer の使い方を学ぶ

- React 公式サイトの中の useReducer を参照
  > (state, action) => newState のところが大切。
- state と action を受け取り => newState,新しいステートとして受け取る。
  - これを reducer というので、その使い方を useReducer という
- const [state, dispatch] = useReducer(reducer, initialArg, init);第１引数 reducer,第２引数 initialArg,第３引数は今回は使用しない。

- export const Posts = () => {　の上に

```js
const initialState = {
  data: [],
  loading: true,
  error: null,
};
```

と記述して、その下に

```js
const reducer = (state, action) => {
  switch (action.type) {
    case "end":
      return {
        ...state,
        data: action.data,
        loading: false,
      };
    case "error":
      return {
        ...state,
        loading: false,
        error: action.error,
      };
    default:
      throw new Error("no such action type!");
  }
};
```

と記述
- import に

```js
import { useCallback, useEffect, useReducer } from "react";
```

- useReducer を追加
- exportのしたの
```js
const [state, setState] = useState({
  data: [],
  loading: true,
  error: null,
});
```

の部分を

```js
const [state, setState] = useReducer();

```
- として、第 1 引数にreducer、第 2 引数にinitialStateを入れる

```js
const [state, setState] = useReducer(reducer, initialState);
```

- setState を dispatch に変更

```js
const [state, dispatch] = useReducer(reducer, initialState);
```

> 不要部分をコメントアウト

```js
const json = await res.json();
  // setState((prevState) => {
  //   return {
  //     ...prevState,
  //     data: json,
  //     loading: false,
  //   };
  // });
} catch (error) {
  // setState((prevState) => {
  //   return {
  //     ...prevState,
  //     loading: false,
  //     error,
  //   };
  // });
}
```

> これでブラウザに表示させるとローディング中と表示される

- 次に

```js
dispatch({ type: "end", data: json });
```

> を const json = await res.json();の次に記述するとデータが表示される

- これを error でも記述する

```js
const json = await res.json();
       dispatch({ type: "end", data: json})

    } catch (error) {
      dispatch({ type: "error", error})
    }
```

- と記述するとエラーの際は表示される

---

### initialState 考え方

> initialState は data が空で、loading が true で、error が null の状態
> この状態で

```js
const [state, dispatch] = useReducer(reducer, initialState);
```

> state に initialState が入ってくる。

- dispatch({ type: "end", data: json})ここが dispatch にデータが入ってくる処理

---

### reducer について

> reducer とは現在の state と新しい action を受け取って、新しい state を返すもの。
> その新しい action のところを担うのが dispatch。
> dispatch を使って新しい action を発生させ、既存の state と組み合わせて、新しい state を生み出す。

```js
dispatch({ type: "end", data: json });
```

> この dispatch が

```js
const reducer = (state, action) =>
```

> この action のなかに入ってくる。

- action の中身を見てみる

```js
case "end":{
  console.log(action)
  return {
    ...state,
    data: action.data,
    loading: false,
  }}
```

> action の中に array としてデータが 100 県入っているのが確認できる。

- ...state,は

```js
data: [],
loading: true,
error: null,
```

> と全く同じこと

```js
  default:
  throw new Error("no such action type!")
```
は
```js
default:{
      throw new Error("no such action type!")
    }
```
> とオブジェクトにしても良い。
---
コミットとプッシュ
- ⚡️ useReducerを使いました。



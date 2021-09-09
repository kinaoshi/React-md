## CWS と SSR の学習

### SWR のバージョンアップ

- yarn upgrade swr --latest
  > とすることのもできるがもっと、便利なコマンドがある。
- yarn upgrade-interactive --latest と入力すると
- - クローンの場合は、 先に yarn を入力
- 最新版が出ていたら、選択しながらインストールできる
  > 今回は"A"キーで全部選択
  > スペースキーで個別選択できる
- 選択後リターンキーを押すとインストールが始まる
  > 終了後 もう一度 yarn をインストールして yarn dev で確認
- 新しいヴァージョンの SWR は第二引数に fetcher が必要なので、SWR ごとに fetcher をつける
- あるいは、一括入力する
- - 一括入力は、\_app.jsx に記入する

```js
const fetcher = async (...args) => {
	const res = await fetch(...args);
	const json = await res.json();
	return json;
};

const MyApp = ({ Component, pageProps }) => {
	return (
		<>
			<Head>
				<link rel="icon" href="/favicon.ico" />
			</Head>
			<SWRConfig
				value={{
					fetcher,
				}}
			>
				<Layout>
					<Component {...pageProps} />
				</Layout>
			</SWRConfig>
		</>
	);
};
```

> と記述

- ここでコマンド + C の後、yarn build で build が通るかどうかを確認する

---

> 問題なければ一旦コミット

- json と yarn.lock
- ⬆️ 依存関係のアップグレード
- コミットしたものをもとに戻す方法
- - ターミナルで、git reset --soft HEAD^ を入力するともとに戻る
- \_app.jsx
- 👽️ SWR の v1 対応

---

### SSR の学習

> Users の子ページの user のページのソースコードを見ると＜ div ＞ Loading...＜/div ＞はあるが、個人名などのアカウント情報が反映されていない。

- components/User/index.jsx を確認すると
- - return ＜ div ＞ Loading...＜/div ＞;があり、ここの Loading がソースコードに反映されている。
    > hooks/user.jsx に isLoading: !data && !error, とあり、これが true になっているので、＜ div ＞ Loading...＜/div ＞が反映される。
- これを解消するのが SSR(サーバーサイドレンダリング)
  > サーバー側で fetch を行なって、あらかじめデータを取得して、そのデータをあらかじめコンポーネント側に渡すことによって、最初から情報を表示されることができる。これがサーバサイドレンダリング。
- コードの記述
- - users/[id]/index.jsx に

```js
export const getServerSideProps = async (ctx) => {
	const { id } = ctx.query;
	const user = await fetch(`https://jsonplaceholder.typicode.com/users/${id}`);
	const userData = await user.json();

	return {
		props: {
			userData,
		},
	};
};

const UsersId = (props) => {
	console.log(props);
	return (
		<div>
			<Header />
			<UserComponent />
		</div>
	);
};
```

> と記述してログを確認すると、個人 Data が取得できている。

- const { id } = ctx.query; query というところに{id}文字列が入ってくる
- - この id 文字列は posts/[id]と連動している
- - ctx.query; の中に入ってくるのが、users/1 番が入ってくる
- (`https://jsonplaceholder.typicode.com/users/${id}`);で取得した data を
- - user.json();で json 化してここで得たデータを userData として、props に渡している
- - props: {userData,},
- ここで props として渡されたデータは
- - const UsersId = (props) のところで、UsersId に渡される。

```js
return (
	<div>
		{props.userData.name}
		<Header />
		<UserComponent />
	</div>
);
```

> とするとブラウザの上部に名前が表示される。
> これで、一応個人情報が最初からソースコードに反映されるようになったが、全てのコードにこれを記載していくのは大変なので、これを解決するために SWRConfig を使う。

- user/[id]/index.jsx の

```js
<div>
	<Header />
	<UserComponent />
</div>
```

> を

```js
<SWRConfig value = {{ fallback }}>

	<Header />
	<UserComponent />
</SWRConfig>
```
> と変更



- デフォルトで最初の値を決めることができる。
- 書き換え

```js
export const getServerSideProps = async (ctx) => {
	const { id } = ctx.query;
	const API_URL = `https://jsonplaceholder.typicode.com/users/${id}`;
	const user = await fetch(API_URL);
	const userData = await user.json();

	return {
		props: {
			fallback: {
				[API_URL]: userData,
			},
		},
	};
};
```
> として、最後にconstでpropsをfallbackに渡す
- const { fallback } = props;
```js
const UsersId = (props) => {
	const { fallback } = props;
	return (
		<SWRConfig value={{ fallback }}>
			<Header />
			<UserComponent />
		</SWRConfig>
	);
};
export default UsersId;
```


> として、ソースコードを確認すると個人情報が取得できている。

- components/User/index.jsx で

```js
const { data, error, isLoading } = useUser();
console.log(data);
```

> としてログを確認すると、最初の undefined が消えて個人情報が取れている

- ただし、SSR は表示までに負荷がかかり、遅くなるので、あまり多用するものではない。

---

> コミット

- ✨ ユーザー詳細ぺーじで SSR を実践

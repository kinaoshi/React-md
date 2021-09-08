####

1. src/components/Posts/index.jsxからPostsComponent に飛んでcomponents/Posts/index をスタイリング

   > return 文の＜ ol ＞を＜ ul ＞に変更して

```js
<ul className="space-y-4">
```

> として間隔 を広くして、表示が title だけなので、ここに body も表示できるようにする

```js
<a>{post.title}</a>
```

> の部分を

```js
 <a>
  <h1>{post.title}</h1>
  <p>{post.body}</p>
</a>
```

> として、css をあてる

```js
<a className="block  group">
	<h1 className="font-bold group-hover:text-blue-500">{post.title}</h1>
	<p className="text-sm text-gray-500 group-hover:text-blue-400">{post.body}</p>
</a>
//  aタグの範囲になったら、hoverが効くようにgroupをつける
```

- 次に、アンダーラインの下の余白を少し開ける
  > src/Comments/Header/index.jsx の

```js
<header className="flex justify-center items-center border-b min-w-full h-24">
```

> の次に mb-4 を入れる

2. src/Components/Users/index.jsx のスタイリング
   > user の方はカード型のデザインにする

- ol を ul に変更
- - カラム数を 3 にしてみる

```js
<ul className="grid grid-cols-3">
```

> グリッドのアイテム間の間隔を調整

```js
<ul className="grid grid-cols-3 gap-4">

<a className="block bg-red-200">{`${user.name} (${user.
```

> # として gap を使ってみる

- {`${user.name} (${user.email})`}の部分は

```js
<h1>{user.name}</h1>
<div>{user.email}</div>
```

> に変更して、（）を消す

```js
<a className="block bg-red-200">
	<h1>{user.name}</h1>
	<div>{user.email}</div>
</a>
```

> の部分を

```js
<a className="block p-2 shadow rounded hover:bg-gray-100">
	<h1 className="font-bold">{user.name}</h1>
	<div className="text-sm">{user.email}</div>
</a>
```

> とする

3. src/Components/Comments/index.jsx のスタイリング

   > posts に似たような感じにする

- ol を ul に変更して　 className="space-y-2"とする

```js
<li key={comment.id} className="border-b pb-2">
	<Link href={`/comments/${comment.id}`}>
		<a className="block  text-sm hover:text-blue-500">{comment.body}</a>
	</Link>
</li>
```

> とする

4. posts/[id]/index.jsx の中の Post をクリックして

- src/Components/ Post/index.jsx に飛び
  > ＜/Head ＞の下を

```js
<UserByUserId id={data.userId} />
<h1 className="text-xl font-bold">{data?.title}</h1>
<p className="text-gray-900">{data?.body}</p>
```

> に変更

- ＜ CommentsByPostId id={data.id} /＞に移動して
  > ３の src/Comments/Comments/index.jsx のスタイリングで作った

```js
<ul className="space-y-2">
	{data.map((comment) => {
		return (
			<li key={comment.id} className="border-b pb-2">
				<Link href={`/comments/${comment.id}`}>
					<a className="block  hover:text-blue-500">{comment.body}</a>
				</Link>
			</li>
		);
	})}
</ul>
```

> をコピーして、＜ ol ＞～＜/ol ＞の部分と置き換える

- src/Components/Post/index.jsx に戻って p タグの下に

```js
<h2 className="text-lg font-bold">コメント一覧</h2>
```

> を追加

- 全体的に文字を大きくする

```js
<h1 className="text-3xl font-bold">{data?.title}</h1>
<p className="text-xl text-gray-900 mt-2">{data?.body}</p>
<h2 className="text-xl font-bold mt-10">コメント一覧</h2>
```

> src/Components/Posts/index.jsx も文字を大きくする

- h1 タグに"text-xl"
- p ダグの"text-sm"を"text-lg"に変更
  > src/Comments/Users/index.jsx も文字を大きくする
- ul タグの"grid-cols-3"をgrid-cols-2にgap-4 を gap-6に変更
- a タグの p-2 を p-4 に
- h1 タグに"text-xl truncate"を追加
- div タグの"text-sm を"text-lg truncate"に変更
  > src/Components/Comments/index.jsx についても
- a タグの text-sm を削除
  > components/Post/index.jsx のコメント一覧の下にもう少し余白をつける

```js
<CommentsByPostId id={data.id} />
```

> の部分を

```js
<div className="mt-2">
	<CommentsByPostId id={data.id} />
</div>
```

> に変更

- users の user の css を変更
- Components/User/index.jsx の変更
- - {data.name}は"className="font-bold text-3xl"
- - "詳細"は className="text-xl font-bold mt-10"
- - ul タグは className="list-inside list-disc mt-2 text-xl"
- - h2 タグ"投稿"はclassName="text-xl font-bold mt-10"
- li タグは

```js
<li>ID番号：{data.id}</li>
<li>アカウント名：{data.username}</li>
<li>メール：{data.email}</li>
<li>電話番号{data.phone}</li>
<li>住所：{data.address.city}</li>
<li>Webサイト：{data.website}</li>
<li>勤務先：{data.company.name}</li>
```

> とする
> PostsByUserId を選択して、呼び出し元(PostsByUserId.jsx)に飛んで

```js
<ol>
	{data.map((post) => {
		return (
			<li key={post.id}>
				<Link href={`/posts/${post.id}`}>
					<a>{post.title}</a>
				</Link>
			</li>
		);
	})}
</ol>
```

> の部分を

- posts/index.jsx の

```js
<ul className="space-y-4">
	{data.map((post) => {
		return (
			<li key={post.id}>
				<Link href={`/posts/${post.id}`}>
					<a className="block  group">
						<h1 className="text-xl font-bold group-hover:text-blue-500">
							{post.title}
						</h1>
						<p className="text-lg text-gray-500 group-hover:text-blue-400">
							{post.body}
						</p>
					</a>
				</Link>
			</li>
		);
	})}
</ul>
```

> をコピーして、置き換える

- 投稿の一覧に余白を少しつける

```js
<div className="mt-2">
	<PostsByUserId id={data.id} />
</div>
```

> comments の Comment の文字を大きくする

- Components/Comment/index.jsx の中の
- - h1 タグに"text-3xl font-bold"
- その上に

```js
<div className="text-lg">
	{data.name} ({data.email})
</div>
```

> を追加して、ul,li タグは削除する

- h2 タグに　 className="text-xl font-bold mt-10"を記述
- 下の記事の PostByCommentId に移動する(コマンド+ クリック)
- src/Components/Post/PostByCommentId.jsxの中を
```js
<a className="text-lg hover:text-blue-500">{data?.title}</a>
```
> に変更
---
> コミット
- 💄 全体的デザイン

# #14
## useStateで配列を扱う
- const [isShow, setIsShow] = useState(true);の次に
```js
  const [array,setArray ] = useState([]);
```
を記述
- inputタグの次に
```js
<ul>
 {array.map(item=>{
   return(
   <li key={item}>{item}</li>
   )
 })}
 </ul>
 ```
 ここでsetStateの空配列に
 [1,2,3]と入れるとブラウザに配列が表示される
 - ulタグの上にbuttonタグを配置
```
<button onClick={handleAdd}>追加</button>
```
- setTextタグの次に
```js
const handleAdd=useCallback(()=>{
  alert(123);
},[]);
```
- setStateの配列[1,2,3]を空配列に戻す
- alert(123)を
  setArray([1,2,3]);に変更して、追加ボタンをクリックするとアラート1,2,3が表示される
- 追加ボタンを押すと配列が一つずつ増えるようにする
---
* 一旦リファクタリング
* * prevIsShow, setIsShow((prevIsShow) => !prevIsShow);
* * prevArray, setArray((prevArray) => {
* * prevCount, setCount((PrevCount) => PrevCount + 1);
---
- setArray(1,2,3)を
```js
const handleAdd=useCallback(()=>{
setArray((prevArray)=>{
  const newArray=[...prevArray,1];
  return newArray;})
},[]);
```
とすると追加ボタンをクリックするたびに"1"が追加される
- inputテキスト欄に記載した文字列を追加ボタンをクリックすることによって配列として追加
```js
setArray((prevArray)=>{
  const newArray=[...prevArray,text];
  return newArray;
},[text]);
```
---
> スプレット構文を使う理由について
- 破壊的メソッドと非破壊的メソッドについて
  - JSで破壊的メソッドを使うのはNG。それを避けるためにスプレット構文を使う
  - 配列でもオブジェクトでもスプレット構文を使う。スプレット構文を使わず、そのままで
  ```
  const foo = {};
  foo.aaa = 1;
   foo
  ```
  とすると　{aaa: 1}　となり、元のオブジェクトが変更されてしまう

> なぜ、破壊的メソッドがダメなのか。
> これを理解するためにミュータブル、イミュータブルについての理解が必要
- ミュータブルは一度作成した値を変更できるもの。イミュータブルは一度作成した値が変更できないもの
- JSにおいては文字列や数値はデフォルトでイミュータブルになっているが配列とオブジェクトはデフォルトでミュータブルになっている。つまり値を作成した後でも変更できてしまう。
-  配列でもオブジェクトでもイミュータブルに扱いたいのでスプレット構文を使う必要がある

---


- 重複する文字列が入力された場合アラートを表示して、ブラウザには表示されないようにする
```js
if(prevArray.some((item)=>item===text)){
  alert("同じ要素が既に存在します");
  return prevArray;
}
```
> を setArray((prevArray) の下に追加する
```js
const newArray=[...prevArray,text];
  return newArray;
```
の部分は
```js
return [...prevArray,text];
```
と出来る

---
GitHubにプッシュしてVercelにデプロイ
- リファクタリングは名前の変更部分と配列に関する部分の２つに分ける
- まず名前の変更部分を選択して、選択した範囲をステージして、先にこの部分だけコミットする
  
  > "control + Z" でステージ範囲をコミット
- - ♻️状態を変更するときにprevをつけて名前をわかりやすくした

- 次に今回の配列に関する部分をコミットする
- - ✨ useStateで配列を扱ってみた


git config --global user.name 'kinaoshi'
git config --global user.email 'ku.928.maido@gmail.com'

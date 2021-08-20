# #5
## css Modulesを学ぶ
- css Modulesを使うことによってcss の管理が容易になる
  - 命名の問題を解決してくれる。同じ名前を使っても良い
  - NEXT.jsのBuilt-In CSS Supportに記載されている
    - Adding Component-Level CSSの'CSS Modules'を確認
    - ＜div className={styles.container}＞のstylesで一意の名前が自動的に割り振られている
  - 使えるのはclassセレクタのみ(className)

* classNameのstylesはclassesで統一する
(名前は自由)
### Home.module.cssをコンポーネント化
* componentsの下階層にFooter.module.cssを作成
  * Home.module.cssをコピーしcomponentsに貼り付け
  * 名前をFooter.module.cssに変更
    * Home.Module.cssからFooterに関する部分をコピペ
  * Footer.module.cssのimportの変更
    * import classes from "./Footer.module.css";とする
  * Home.module.cssからFooterに関する記述を削除
* Headline.module.cssでも同様にする
* Links.module.cssでも同様にする
* Main.module.cssも同様にする
---
コミット
コミット名　💄 スタイルファイルの分割
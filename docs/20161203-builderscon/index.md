# CSS設計を破綻させない

builderscon tokyo 2016 [@kubosho_](https://twitter.com/kubosho_)

---
CSS設計を破綻させないというテーマで話します。よろしくお願いします。
さてさっそく質問です。

-----
## CSSを破綻させたことありますか？

---

挙手を求める

-----
## 自己紹介

- kubosho_
- フロントエンドデベロッパー
- [Goodpatch](http://goodpatch.com/jp)所属
- [https://github.com/kubosho](https://github.com/kubosho/)
- [https://twitter.com/kubosho_](https://twitter.com/kubosho_)
- [http://blog.kubosho.com](http://blog.kubosho.com/)

---
自己紹介です。株式会社グッドパッチというところでProttというプロトタイピングツールのWeb版のUI実装をやっています。
ところでCSSですが、自分は割と好きです。

-----
## CSSは割と好き

CSSを適用すると素っ気ない見た目からかっこよいかわいい見た目になるのが良い

---
（読み上げ）
のですが、世の中ではCSSに死を！と言われたり、CSS勉強するのがだるいと言われたりします。なぜなのか。
ということで話すことです。

-----
## 話すこと

- CSS設計はなぜ破綻するか
- CSS設計はどのようにしたらいいのか
- CSSの状態をツールを使って解析する

---
CSS設計はなぜ破綻するのかから、設計手法、またCSSの状態を解析するツールを紹介します。

-----

## CSSは破綻しやすい

OOCSSの提唱者Nicole Sulliban氏も["CSS is too fragile"](http://www.andoh.org/2009/11/web-directions-east-2009-nicole.html)と言った

---
OOCSSという結構昔からある、もしかしたらCSS設計で最初に有名になったかもしれないものがありますが、その提唱者がCSS is too fragileと2008年のイベントで言いました。
さてここでCSSの特性について復習します。

-----

## CSSの特性

![CSSのルールセット構造](img/css-rule-structure.png)

[CSS ルールセット構造図 · terkel\.jp](http://terkel.jp/archives/2011/09/css-rule-structure/)より引用

---
はじめにルールセットについてです。CSSはセレクタ・プロパティ・値の定義を束ねたものがルールセットと呼ばれます。

-----

## CSSの特性

- 記述を間違えてもエラーにならない
- ルールセットを書く順序は関係ある（常に関係あるとは言ってない）
- 同じプロパティが定義されていれば順序・詳細度・重要度にもとづいて上書きされる

---
記述を間違えてもエラーがでません。ブラウザで見て崩れていることに気づきます。
また、ルールセットを書く順序は関係あります。ただ常に関係あるとは言ってないです。
最後に同じプロパティが定義されている場合は順序・詳細度・重要度にもとづいて上書きされます。
CSSをややこしくしているのはここです。意識して書かないと容易に破綻します。

-----

## 意識して書かないと容易に破綻する

---
（読み上げ）
ではどのようなことが破綻と言われるのか見ていきましょう

-----
<!--code-->
## CSSの破綻

ルールセット定義の重複が複数ある

```css
.button {
  border: 1px solid #ccc;
}

/* ...長いコードの後や別ファイルなど... */

.button {
  border: 1px solid #666;
}
```

---
はじめにルールセット定義の重複が複数あることです。これはCSSフレームワーク、たとえばBootstrapなどを使うと起こりやすいというか、ほぼ避けれないものです。
このような重複が多くなるとどんな見た目になるのか予測できなくなって破綻します。

-----
<!--code-->
## CSSの破綻

前に書いたセレクタの詳細度が高い

```css
 /* 詳細度はa=0, b=4, c=0なので0.4.0となる */
.container .form .form-group .form-submit-button {}

/*
  詳細度はa=0, b=1, c=0なので0.1.0となる
  上書きできない。つらい
*/
.form__submit-button {}
```

---
次に前に書いたセレクタの詳細度が強くて上書きできないことです。
これがたくさんあるとimportantの嵐になります。

-----
<!--code-->
## CSSの破綻

命名規則がバラバラ

```css
.account-login-button {}

.commentSubmitButton {}

.form_submit_button {}
```
---
次に命名規則、特に単語の区切りがケバブケース、キャメルケース、スネークケースとバラけていることです。
なぜ秩序が保たれなかったのか…

-----
<!--code-->
## CSSの破綻

同じ見た目になるのか分からない

```html
<a class="button button-submit">送信</a>
<button type="button" class="button button-submit">送信</button>
```

---
最後に`a`要素でボタンっぽい見た目を実現させたいことが多いのですが、この時同じ見た目になるのかが分からないと辛いです。

-----
## CSSの破綻

- 詳細度が管理されていない
- ルールセットの分割粒度が明確ではない
- 命名規則が決まっていない
- 同じ見た目になるのか分からない

---
ここまでCSSが破綻する4つの理由について話しました。
（項目読み上げ）です。
さてここからこれらを解決する方法を言っていきます。
まずは詳細度を管理します。

-----
## 詳細度を管理する

- Sassなどのプリプロセッサを使う場合はファイル内で下に行くにつれて詳細度が高くなるようにする
- セレクタやIDセレクタを書きすぎないようにする

---
詳細度、これは先ほども言いましたが強いセレクタ、たとえばIDセレクタを使ったり、セレクタの定義が多いとスタイルが上書きできなくなって破綻します。
例を見てみます。

-----
<!--code-->
## 詳細度を管理する

```css
 /* 詳細度はa=1, b=3, c=0なので1.3.0となる */
.container #contents .form .form-button {
  margin: 0;
}

/* ... */

 /* 詳細度はa=0, b=2, c=0なので0.2.0となる */
.comment-form .form-button {
  /* 上書きできない＼(^o^)／ */
  margin: 10px auto;
}
```

---
ページの基礎のルールセットを定義する場所でこのようなセレクタを書いてしまうと、あとから大変です。
では詳細度を管理した例を見てみましょう。

-----
<!--code-->

## 詳細度を管理する

```css
 /* 詳細度はa=0, b=2, c=0なので0.2.0となる */
.form .form-button {
  margin: 0;
}

/* ... */

/*
  詳細度はa=0, b=2, c=0なので0.2.0となる
  記述の順序が後なので値が上書きされる
*/
.comment-form .form-button {
  /* 上書きできる */
  margin: 10px auto;
}
```

---
先ほどと違ってform共通のスタイルの詳細度を低くしました。
これで下のほうで書いた値で上書きできて秩序が保たれます。
次にルールセットの分割粒度を明確にすることについて話します。

-----
## ルールセットの分割粒度を明確にする

- さまざまな方法が提案されている
- あるページ内の要素に対してスタイルを適用するときに、ルールセットをどこに置けばいいのか迷わないようにするのは重要

---
あるページ内の要素に対してスタイルを適用するときに、ルールセットをどこに置けばいいのか迷わないようにするのはCSS設計をする上で重要です。
重要なせいかさまざまな方法が提案されています。
ではブログを作っているというていでルールセットをいろんな方法でどこに置いているのか見てみましょう。

-----
<!--code-->
## FLOCSS

```sh
/styles
├─ foundation - reset.cssやnormalize.cssなど
├─ layout - ヘッダーやフッターなど
└─ object
    ├─ component - ボタンやフォームなど再利用できるもの
    ├─ project - 個別記事などにそのプロジェクト固有のルールセット
    └─ utility - marginやpaddingの調整など
```

---
はじめにFLOCSSです。FLOCSSはfoundation、layout、objectという大まかな分け方があります。
objectの中ではさらにcomponent、project、utilityと分かれています

-----
<!--code-->
## SMACSS

```sh
/styles
├─ base - reset.cssやnormalize.cssなど
├─ layout - ヘッダーやフッターなど
├─ module - ボタンやフォームなど再利用できるもの
├─ state - UIの状態
└─ theme - ユーザーがテーマなどを設定するときに使う
```

---
次にSMACSSです。SMACSSはbase、layout、module、state、themeという感じで分かれています。
FLOCSSではUIの状態は定義する場所は決まっていませんでしたが、SMACSSはstate以下に`is-***`という感じでUIの状態に応じたスタイルを置きます。

-----
<!--code-->
## ECSS

```sh
/assets
├─ structure
├─ top
│  ├ js
│  └ css
├─ single
├─ categories
...
```

---
最後にECSSです。ECSSは毛色が違い、サイト全体で共通で使うヘッダーやフッターを除き、ルールセット定義は各ページごとにおこないます。
つまり再利用を諦めた設計です。
今まで共通の要素を以下に適切な場所に置くのかを考えていたのに、ページごとにcssフォルダを作ってその中で定義するのが今までとだいぶ違います。
次に命名規則について話します。

-----
## 命名規則を決める

- 命名規則はさまざまなものがある
- チーム内で使う命名規則を一致させることが重要

---
命名規則もいろいろなものがありますが、これもチーム内で使う命名規則を一致させることが重要です。
では命名規則を見てみましょう。

-----
## MindBEMding

`.block__element--modifier`

---
はじめにMindBEMdingです。MindBEMdingは元となる要素をblockとして、子要素をelementとします。
例として記事のヘッダーとパンくずリストで見てみましょう。

-----
## MindBEMding

- `.article__header`
- `.breadcrumbs__item--current`

-----
## ECSS

`.nsp-Component_ChildNode`

---
次にECSSです。ECSSはページごとにCSSを分けると話しましたが、そのページ名を短縮した形で名前空間にします。
そしてページ内の要素を示す名前をつなげて、子要素の名前をアンダースコアでつなげます。
やはり例として記事のヘッダーとパンくずリストで見てみましょう。

-----
## ECSS

- `.top-Article_Header`
- `.bc-ItemCurrent`

-----
## SMACSS

- base: `body, a...`
- layout: `layout-***, l-***`
- module: `item-***, form-***`
- state: `is-***`
- theme: `theme-***`
---
最後に、SMACSSです。SMACSSは全体に関わるスタイルは要素名でスタイルを定義します。たとえばbody要素やリンクなどです
SMACSSはヘッダーやフッターなどページ内の大まかな区域を示すものはレイアウトとして、layout-またはl-というプリフィックスを付けます。
モジュールは特定のプリフィックスはつけない代わりにそのモジュールを示すクラス名をプリフィックスのように扱います。
たとえばフォームを構成するものだったら、form-というクラス名から始まるでしょう。
そしてUIの状態を示す場合、たとえばメッセージの見た目を正常処理と異常処理で分ける場合はis-から始まるクラス名を使います。
最後に同じ見た目になるようにするということを話します。

-----
## 同じ見た目になるようにする

- `a` 要素と `button` 要素で同じクラス名を使うことは割とある
- `a` 要素でボタンっぽい見た目を実現させたいことが多い

---
（読み上げ）
多いのですが、a要素とbutton要素はブラウザ既定のスタイルシートで適用された見た目が全く違います

-----
## 同じ見た目になるようにする

`a` 要素と `button` 要素だと見た目は全く違う

<a href="#">送信</a>
<button type="button">送信</button>

---
これを同じ見た目になるようにCSSを書くとこうなります。

-----
<!--code-->
## 同じ見た目になるようにする

```css
.button--submit {
  display: inline-block;
  padding: 6px 12px;
  border: 1px solid #4cae4c;
  background-color: #5cb85c;
  color: #fff;
  font-family: sans-serif;
  font-size: 1rem;
  line-height: 1.4;
  text-decoration: none;
  cursor: pointer;
}
```

-----
## 同じ見た目になるようにする

<style>
[layout] .button--submit {
  display: inline-block;
  padding: 6px 12px;
  border: 1px solid #4cae4c;
  background-color: #5cb85c;
  color: #fff !important;
  font-family: sans-serif;
  font-size: 1rem;
  line-height: 1.42857143;
  text-decoration: none;
  cursor: pointer;
}
</style>
<a href="#" class="button--submit">送信</a>
<button type="button" class="button--submit">送信</button>

---
無事同じ見た目になりました。

-----
## 同じ見た目になるようにする

- ブラウザの既定のプロパティと値指定を把握する
- Bootstrapではどのように指定しているのか見る
- そのルールセットがどんなHTML要素で使われそうか予測する

-----
## 一番大事なこと

-----
## デザイナーとの認識合わせ

[デザインの意図を正確に理解した上で書かれたCSSは破綻しない](http://morishitter.hatenablog.com/entry/2016/07/29/204642)

---
（読み上げ）
という言葉があります。

-----
## デザイナーとの認識合わせ

- だいたいはプロジェクトごとに一からCSSを書くことになる
- SketchやPhotoshopを見てデザイナーと意見を交わし合う
- デザイナーとの質問や提案を通して意図の認識を合わせる

---
（読み上げ）
これにより、不必要なCSSの定義を減らすことができる…かもしれません

-----

## CSSの破綻を起こさないためには

- 詳細度を管理しよう
- ルールセットの分割粒度を明確にしよう
- 命名規則を決めよう
- 同じ見た目になるようにしよう
- デザイナーと意図の認識を合わせよう

---
（読み上げ）
つまり

-----

## チームで議論して良いCSS設計を考えよう

---
（読み上げ）
ということになります。
さてここからはCSSの状態を解析するためのツールを紹介します。

-----
## CSSの状態を解析するためのツールの紹介

-----
## 定義されているCSSのさまざまなデータを見る

- [CSS Stats](http://www.cssstats.com/)
- [StyleStats](http://www.stylestats.org/)

-----
## CSS Stats

![CSS Stats](img/cssstats-github.png)

-----
## StyleStats

![StyleStats](img/stylestats-github.png)

-----

## どちらも同じようなデータが見られる

- CSSのファイルサイズ
- ルールセットの数
- 文字サイズの一覧
- 色の一覧

など...

-----

## それぞれでしか見られないデータがある

- CSS Stats
  - 詳細度グラフ
  - `z-index`の値の定義
  - Media Queriesの値の定義

-----

## それぞれでしか見られないデータがある

- StyleStats
  - CSSの複雑度
  - 最も詳細度の高いセレクタ
  - プロパティの使用頻度のグラフ

---
また、CSSの詳細度だけを分かりやすくしてくれるツールもあります。

-----
## セレクタの詳細度を可視化

[CSS Specificity Graph Generator](https://jonassebastianohlsson.com/specificity-graph/)

---
では実際にいくつか実例を見てみましょう

-----
## とあるアプリで1から書いたCSS

![とあるアプリで1から書いたCSSの詳細度](img/toaru-app-v.png)

-----
## とあるアプリで1から書いたCSS

- CSSファイル・ディレクトリ構成として[FLOCSS](https://github.com/hiloki/flocss)を採用
- 命名規則として[BEM](https://en.bem.info/methodology/naming-convention/)を採用 （`block_element-Modifier` の形式）
- 詳しくは[神獄のヴァルハラゲートのCSS設計 \- I'm kubosho\_](http://blog.kubosho.com/entry/2014/12/09/valhalla-gate-css-architecture)で

---
- ページごとにスタイルが違うところが多々あったり、大人な事情でIDセレクタを使っている
- Android向けにCSSを調整しているところがあるので少し詳細度が上がっているところがある

-----
## AbemaTV

![AbemaTV](img/abematv.png)

-----
## AbemaTV

- Reactによるコンポーネント設計手法として[Atomic Design](http://atomicdesign.bradfrost.com)を採用している
- また[CSS Modules](https://github.com/css-modules/css-modules)を採用している

-----
## とあるアプリのCSS

![とあるアプリのCSSの詳細度](img/toaru-app-p.png)

-----
## とあるアプリのCSS

- CSSが破綻しているという状況
- どうしてこうなった

---
もはやどうしようもできない状況。どうしてこうなった！

-----
## まとめ

- CSSの特性を把握した上で設計の方針を定めよう
- CSSを設計するときはチームと議論しよう
- CSSの状態をツールを使って解析しよう

---
（読み上げ）
これで終わりです。ありがとうございました。

-----
## Q&A

- すでに破綻しているCSS（プロジェクトに途中から入ってそこのCSSが破綻している）はどうやって改善したらいいのか？
  - 自分もどうしたらいいのか教えてほしいです
  - 今のところチームで話し合って設計指針を決めて1から書き直すしかないかなと思います
- CSSの詳細度がソートされていることを保証するものはあるか？
  - [inuscript/sort\-specificity: Sort css selector for specificity](https://github.com/inuscript/sort-specificity)というそれっぽいものはあった
  - ソートされたセレクタを出力するものみたいなので、頑張ればなんとかできるかも？

-----

## 参考資料

- [僕はCSSを見殺しにした - dskd](http://dskd.jp/archives/54.html)
- [Atomic Design powered by React @ AbemaTV](http://www.slideshare.net/ygoto3q/atomic-desigin-powered-by-react-abematv)
- [AbemaTVのランタイムパフォーマンスのAudit \- 1000ch\.net](https://1000ch.net/posts/2016/abematv-runtime-perf-audit.html)
- [The Specificity Graph – CSS Wizardry – CSS, OOCSS, front\-end architecture, performance and more, by Harry Roberts](http://csswizardry.com/2014/10/the-specificity-graph/)
- [CSS specificity graphs in practice \| Blog \| Decade City](https://decadecity.net/blog/2014/11/26/css-specificity-graphs)
- [Enduring CSSの設計思想 \- ECSSが目指す設計 \| CodeGrid](https://app.codegrid.net/entry/2016-ecss-1)
- [SMACSSによるCSSの設計 \- ベースとレイアウト \| CodeGrid](https://app.codegrid.net/entry/smacss-1)
- [破綻しにくいCSS設計の法則 15 \- Qiita](http://qiita.com/BYODKM/items/b8f545453f656270212a)
- [CSSが破綻する4つの理由 \- Qiita](http://qiita.com/BYODKM/items/8c777db2d89f4e830c93)

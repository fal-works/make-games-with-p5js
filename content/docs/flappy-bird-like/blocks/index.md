---
title: ブロックを複数にする
type: docs
weight: 50
---

# ブロックを複数にする

{{< hint info >}}
この先の内容を取り入れる前に、前項 [見た目をアレンジ](../40-visuals) で改造した結果のソースコードを別名保存しておくと良いでしょう。

このページのサンプルコードは、[ブロック1個だけ実装](../30-block) のものをベースにしており、装飾が無い状態に戻っています。ご自分の改造結果を保ったまま増築していけると良いですが、厳しそうだったら、サンプルコードを全文コピペして装飾の無い状態に戻すのもOKです。
{{< /hint >}}

## やること

1. ブロックを配列に格納するように変える
1. `draw()` の中で、ブロックの配列に対して以下を行う
    - 一定の時間間隔で、上下ペアでブロックを作成して配列に追加する
    - 画面左端に消えたブロックがあったら削除する
1. `draw()` の中で、配列内の各ブロックについて以下を行う
    - 位置の更新
    - 描画

{{< expand "補足： 配列の基本">}}
百聞不如一見

※ `console.log()` は値をコンソールに出力します。  
　p5.js Web Editor の場合だと画面左下に結果が表示される

```javascript { linenos=false }　
let countries = []; // 中身のない空っぽの配列を作成

countries.push("Japan"); // push で配列に値を追加
countries.push("USA");
countries.push("Germany");

// 配列[番号] で要素を取得できる。番号は 0 始まり
console.log(countries[2]); // この場合 "Germany" を出力

// for...of 構文。配列の各要素を取り出して country と名付けている
for (let country of countries) console.log(country); // 全要素が出力される
```

この **for...of 構文**は、次のように普通の for で書いたときと結果は同じです。
```javascript { linenos=false }　
for (let i = 0; i < countries.length; i += 1) console.log(countries[i]);
```

配列の `push()` は可変長引数なので、次のように一度に追加しても良いです。
```javascript { linenos=false }　
countries.push("Japan", "USA", "Germany");
```

またこの例だと、そもそも初期化と同時にまとめて登録しておくこともできます。
```javascript { linenos=false }　
let countries = ["Japan", "USA", "Germany"];
```
{{< /expand >}}

{{< expand "補足： 配列の filter()">}}
`filter()` を使うと、条件に合致した要素だけを含む新たな配列を作ることができます。

例えば、このような配列と関数があったとします。
```javascript { linenos=false }　
/** 元となる数列 */
let numbers = [10, 20, 30, 40];

/** 値が 20 より大きければ true、そうでなければ false を返す */
function greaterThan20(n) {
  return n > 20;
}
```

このとき、`filter()` を使って、「20 より大きい値」だけを含む新たな配列が作れます。
```javascript { linenos=false }　
let newNumbers = numbers.filter(greaterThan20); // 結果は新たな配列 [30, 40]
```

つまり `filter()` は、「値に応じて `true` か `false` を返す関数」を引数として与えると、それを使って配列の各要素をチェックし、フィルタリングしてくれるのです。

※ 厳密には `true`/`false` というより truthy/falsy で判定

なお、念のため…… `return n > 20;` のところは、次のように書くのと同じ結果です。
```javascript { linenos=false }　
if (n > 20) return true;
else return false;
```
{{< /expand >}}


## 実装

変数 `block` が複数形の `blocks` に変わっていることに注意

```javascript { hl_lines=["48-52", "59-60", "62-67", "78-79", "83-85", 89, 97], linenostart=1 }
// ---- エンティティ関連の関数 ---------------------------------------------

// 全エンティティ共通

function updatePosition(entity) {
  entity.x += entity.vx;
  entity.y += entity.vy;
}

// プレイヤーエンティティ用

function createPlayer() {
  return {
    x: 200,
    y: 300,
    vx: 0,
    vy: 0
  };
}

function applyGravity(entity) {
  entity.vy += 0.15;
}

function applyJump(entity) {
  entity.vy = -5;
}

function drawPlayer(entity) {
  square(entity.x, entity.y, 40);
}

// ブロックエンティティ用

function createBlock(y) {
  return {
    x: 900,
    y,
    vx: -2,
    vy: 0
  };
}

function drawBlock(entity) {
  rect(entity.x, entity.y, 80, 400);
}

function blockIsAlive(entity) {
  // ブロックの位置が生存圏内なら true を返す。
  // -100 は適当な値（ブロックが見えなくなる位置であればよい）
  return -100 < entity.x;
}

// ---- ゲーム全体に関わる部分 --------------------------------------------

/** プレイヤーエンティティ */
let player;

/** ブロックエンティティの配列 */
let blocks;

/** ブロックを上下ペアで作成し、`blocks` に追加する */
function addBlockPair() {
  let y = random(-100, 100);
  blocks.push(createBlock(y)); // 上のブロック
  blocks.push(createBlock(y + 600)); // 下のブロック
}

// ---- setup/draw 他 --------------------------------------------------

function setup() {
  createCanvas(800, 600);
  rectMode(CENTER);

  // プレイヤーを作成
  player = createPlayer();

  // ブロックの配列準備
  blocks = [];
}

function draw() {
  // ブロックの追加と削除
  if (frameCount % 120 === 1) addBlockPair(blocks); // 一定間隔で追加
  blocks = blocks.filter(blockIsAlive); // 生きているブロックだけ残す

  // 全エンティティの位置を更新
  updatePosition(player);
  for (let block of blocks) updatePosition(block);

  // プレイヤーに重力を適用
  applyGravity(player);

  // 全エンティティを描画
  background(0);
  drawPlayer(player);
  for (let block of blocks) drawBlock(block);
}

function mousePressed() {
  // プレイヤーをジャンプさせる
  applyJump(player);
}
```

{{< expand "補足： 一定間隔" >}}
```javascript { linenos=false }
if (frameCount % 120 === 1) addBlockPair(blocks);
```
のところですが、ここでは `frameCount` を `120` で割った余りを計算しています。

すると、`frameCount` が `1` のとき、`121` のとき、`241` のとき……という具合で、120 フレームに 1 回、すなわち（今は 60 FPS なので）2 秒に 1 回、ブロックの追加が行われます。

ちなみになぜ `0` とかではなくて `1` と比較しているのかというと、どうも `frameCount` は `1` から始まるようなので、最初の待ち時間が無いほうが良いと思ってこうしています。
{{< /expand >}}


{{< hint info >}}
実行してみて、上下ペアのブロックが一定間隔で現れることを確認してください。
{{< /hint >}}

{{< hint info >}}
ブロックの削除がうまくいっているかを確認するには、例えば次のようなものをソースコード下端に加えて実行すると、何かキーを押すたびに現在のブロックの数が表示されます。
```javascript { linenos=false }
function keyTyped() {
  console.log(`blocks: ${blocks.length}`);
}
```
{{< /hint >}}


{{< expand "余談： const にしないの？" >}}
JavaScript では、再代入可能な変数は `let`、再代入不可能な変数は `const` で宣言します。

しかし p5.js の公式サンプルコードは、おそらく初学者を混乱させないためか `let` で統一しているようなので、この資料でもそれに倣っています。

上のサンプルコードだと、

```javascript { linenos=false }
for (let block of blocks) // ...
```

のところで `block` に再代入することはありえないので

```javascript { linenos=false }
for (const block of blocks) // ...
```

とするのが妥当ですね。

ここの `for` 文は2箇所ともそれぞれ一行しか無いので、もちろん実質的にはどっちでも変わらないのですが、原則論を言うと変数宣言は

- 基本的には `const`
- どうしても再代入したいときだけ `let`

という姿勢でいたほうが安全です。

ゲームの場合、たいていのバグは「データが想定外の仕方で変化する」ことによって起きる印象があるので、変化しないと分かっている箇所はどう間違っても変化しないように固めておき、残りの変化しうる部分に注意を向けて慎重に扱う、ということが重要になります。
{{< /expand >}}


{{< expand "余談： 配列と高階関数">}}
配列の機能は他にも色々あります。  
参考： [Array - JavaScript | MDN](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Array)

`filter()` のように、引数として別の関数を受け取るような関数というのは Processing (Java) （少なくとも 3.x 以前のバージョン）には無いものなので、そちらから来た人には新鮮かもしれません。

他にも例えば `forEach()` というのがあり、これは `for` 文の代わりに使えます。
```javascript { linenos=false }
// 次の2つは同じ結果をもたらす

for (let block of blocks) updatePosition(block);

blocks.forEach(updatePosition);
```
`forEach()` で書くと、コードがまるで文章みたいになって（「ブロックのそれぞれについて位置を更新せよ」と読める）、スマートな感じがしますね。  
パフォーマンス的には `forEach()` の方がちょっと遅いらしいという話がありますが、少なくとも今回の例で気にする必要はありません。

ちなみに、`filter()` や `forEach()` のような関数に引数として渡す関数（上の例だと `updatePosition()`）のことを「コールバック関数」と呼んだりします。
{{< /expand >}}


{{< expand "余談： オブジェクト生成破棄のコスト" >}}
## 問題

`createBlock()` は新たなオブジェクトを生成する処理ですが（`createPlayer()` も同様）、こういうのはメモリを確保するために若干の処理負荷がかかります。

さらに問題なのは破棄するときで、JavaScript では、使われなくなった（誰からも参照されなくなった）オブジェクトはガベージコレクション（略して GC）という機能によって自動的に破棄されますが、これによっても処理不可がかかります。
参考： [メモリ管理 - JavaScript | MDN](https://developer.mozilla.org/ja/docs/Web/JavaScript/Memory_Management)

`filter()` で毎度新たな配列を作り直している（古いのは GC で破棄している）のも同様です。

今回の例だと気にする必要はありませんが、たとえばスマホ向けにゲームを作ったりする場合、けっこうシビアに考えないと処理が追いつかなくてカクカクしたりします。弾幕シューティングみたいにオブジェクト数が多くてサイクルも速いと、PC でも怪しい。

GC でどのオブジェクトが破棄されるかはプログラマには正確に予測・制御できないので、そもそも破棄が行われないように気をつける必要があります。


## 対策（個々のエンティティの生成破棄）

オブジェクトプールという手法があります。十分な量のオブジェクトをあらかじめ用意しておいて（プール＝貯める）、使うときにはプールから取り出して使い終わったらプールに戻す、というやり方でオブジェクトを使い回すというものです。

良い可視化になっているかは分かりませんが、筆者が p5.js で可視化を試みてみたスケッチがこちら： [Object Pool — FAL Works](https://www.fal-works.com/creative-coding-posts/object-pool)

難点として、

- あるオブジェクトを使って、使い終わって、また使って……としたときに、  
その前後の状態を容易に区別する方法がない  
（使うたびに新たなIDを付与して区別することはあるけど、主にデバッグ目的に限る）
- オブジェクトを使い終わったとき、以下を徹底的にやらないとバグる
    - 当該オブジェクトが持っているデータのリセット
    - 当該オブジェクトを参照している他のオブジェクトへの通達とその後の処理

以上の理由でこの方法はものすごくバグを出しやすいのですが、背に腹は替えられんという感じでやむなく使ったりします。

オブジェクトプールは配列で実装することになりますが、「使っているオブジェクトのリスト」を別に作るかどうか、別に作らないとしたら使っているオブジェクトをどうやって判別するか、という点で実装上の違いが出てきます。

最も簡易的な方法は、一つ一つのオブジェクトに 生きている／死んでいる のフラグを持たせるというもので、これは全オブジェクトについて生死のチェックをループのたびに毎回やらなきゃいけませんが、そのぶん実装は楽だと思います。

他にどんなのが……という詳しい話は書籍[『Game Programming Patterns』](https://www.amazon.co.jp/dp/B015R0M8W0/)などが良いかもしれません（サンプルコードは C++ ですが）。


## 対策（配列の生成破棄）

Java の `ArrayList` が取っている方法に近いですが、十分な長さの配列と、その長さとは別に「有効な値がいくつ入っているか」を記録しておいて、有効な要素だけを対象としてループ処理をする、という方法があります。

こうすると要素が増減しても配列オブジェクト自体が生成・破棄されることはなくなります。

```javascript { linenos=false }
class ArrayList {
  constructor(initialCapacity) {
    this.array = new Array(initialCapacity);
    this.size = 0;
  }

  add(element) {
    this.array[this.size] = element;
    this.size += 1;
  }

  loop(callback) {
    const { array, size } = this;
    for (let i = 0; i < size; i += 1) callback(array[i], i, array);
  }

  // 他、filter() とかは省略
}
```

こういうデータコンテナ作るの楽しいんですけど、ふと我に返ると、なんでこんなこと手動でやらなきゃいけないんだろうな、という気分にならないでもない。


## 余談の余談

GC のパフォーマンス問題を経験すると、GC は悪！ という気分になるのですが、本来は無いと困るレベルの便利な機能なので、GC を悪く言うのは風評被害な感じがあるかもしれませんね。

あと Wikipedia の項目名だと「ガベージコレクション」となっていますが、本場の発音を考慮すると「ガベージ」ではなく「ガーベジ」や「ガービッジ」ではないか、とかがあるのでこの言葉にはそこそこ表記揺れがあります。  
個人的な考えとしては、英語がカタカナに変換されるにあたっては一定の慣習みたいなものがあり、「ガベージ」はそれに則っている雰囲気がするのでこれでもいいと思っています。
{{< /expand >}}

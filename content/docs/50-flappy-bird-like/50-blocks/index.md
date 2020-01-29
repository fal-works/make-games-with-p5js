---
title: ブロックを複数にする
type: docs
weight: 50
---

# ブロックを複数にする

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

```javascript
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
```javascript
for (let i = 0; i < countries.length; i += 1) console.log(countries[i]);
```

配列の `push()` は可変長引数なので、次のように一度に追加しても良いです。
```javascript
countries.push("Japan", "USA", "Germany");
```

またこの例だと、そもそも初期化と同時にまとめて登録しておくこともできます。
```javascript
let countries = ["Japan", "USA", "Germany"];
```
{{< /expand >}}

{{< expand "補足： 配列の filter()">}}
`filter()` を使うと、条件に合致した要素だけを含む新たな配列を作ることができます。

例えば、このような配列と関数があったとします。
```javascript
/** 元となる数列 */
let numbers = [10, 20, 30, 40];

/** 値が 20 より大きければ true、そうでなければ false を返す */
function greaterThan20(n) {
  return n > 20;
}
```

このとき、`filter()` を使って、「20 より大きい値」だけを含む新たな配列が作れます。
```javascript
let newNumbers = numbers.filter(greaterThan20); // 結果は新たな配列 [30, 40]
```

つまり `filter()` は、「配列の要素を受け取って `true` か `false` を返す関数」を引数として与えると、それを使って配列の要素をフィルタリングしてくれるのです。

※ 厳密には `true`/`false` というより truthy/falsy で判定

なお、念のため…… `return n > 20;` のところは、次のように書くのと同じ結果です。
```javascript
if (n > 20) return true;
else return false;
```
{{< /expand >}}


## 実装

```javascript
// ---- エンティティ関連の関数 --------------------------------------------------

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

// ---- ゲーム全体に関わる変数と関数 --------------------------------------------

/** プレイヤーエンティティ */
let player;

/** ブロックエンティティの配列 */
let blocks;

/**
 * ブロックを上下ペアで作成し、`blocks` に追加する
 */
function addBlockPair() {
  let y = random(-100, 100);
  blocks.push(createBlock(y)); // 上のブロック
  blocks.push(createBlock(y + 600)); // 下のブロック
}

// ---- setup/draw 他 ----------------------------------------------------------

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
  if (frameCount % 120 === 1) addBlockPair(blocks); // 一定間隔でブロック追加
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

{{< hint info >}}
各自実行してみて、上下ペアのブロックが一定間隔で現れることを確認してください。
{{< /hint >}}

{{< expand "余談： 配列と高階関数">}}
配列の機能は他にも色々あります。  
参考： [Array - JavaScript | MDN](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Array)

`filter()` のように、引数として別の関数を受け取るような関数というのは Processing (Java) （少なくとも 3.x 以前のバージョン）には無いものなので、そちらから来た人には新鮮かもしれません。

他にも例えば `forEach()` というのがあり、これは `for` 文の代わりに使えます。
```javascript
// 次の2つは同じ結果をもたらす

for (let block of blocks) updatePosition(block);

blocks.forEach(updatePosition);
```
`forEach()` で書くと、コードがまるで文章みたいになって（「ブロックのそれぞれについて位置を更新せよ」と読める）、スマートな感じがしますね。  
パフォーマンス的には `forEach()` の方がちょっと遅いらしいという話がありますが、少なくとも今回の例で気にする必要はありません。

ちなみに、`filter()` や `forEach()` のような関数に引数として渡す関数（上の例だと `updatePosition()`）のことを「コールバック関数」と呼んだりします。
{{< /expand >}}

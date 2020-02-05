---
title: 衝突判定を実装
type: docs
weight: 70
---

# 衝突判定を実装

プレイヤーがブロックに当たったらゲームオーバーとしたい

## 判定方法

まず、2者間の衝突を考える

### 考え方

四角形 A と四角形 B が衝突しているかどうかを確認する方法：

1. A, B の **x 軸** 方向での距離が十分に開いていたら、衝突していない
1. A, B の **y 軸** 方向での距離が十分に開いていたら、衝突していない
1. 以上いずれでもないなら、衝突している

[説明用スケッチ](https://fal-works.github.io/make-games-with-p5js-src/07-collision/one-on-one/) （クリックで開く）
[![説明用スケッチ](./one-on-one-collision.png)](https://fal-works.github.io/make-games-with-p5js-src/07-collision/one-on-one/)

### 2者間の衝突を判定する関数

上記をそのまま実装すると次のようになる

```javascript
/**
 * 2つのエンティティが衝突しているかどうかをチェックする
 *
 * @param entityA 衝突しているかどうかを確認したいエンティティ
 * @param entityB 同上
 * @param collisionXDistance 衝突しないギリギリのx距離
 * @param collisionYDistance 衝突しないギリギリのy距離
 * @returns 衝突していたら `true` そうでなければ `false` を返す
 */
function entitiesAreColliding(
  entityA,
  entityB,
  collisionXDistance,
  collisionYDistance
) {
  // xとy、いずれかの距離が十分開いていたら、衝突していないので false を返す

  let currentXDistance = abs(entityA.x - entityB.x); // 現在のx距離
  if (collisionXDistance <= currentXDistance) return false;

  let currentYDistance = abs(entityA.y - entityB.y); // 現在のy距離
  if (collisionYDistance <= currentYDistance) return false;

  return true; // ここまで来たら、x方向でもy方向でも重なっているので true
}
```

ここで、「衝突しないギリギリの距離」は次の通り

- x軸方向： `(A の幅の半分) + (B の幅の半分)`
- y軸方向： `(A の高さの半分) + (B の高さの半分)`


{{< expand "余談： 衝突対象の形状と大きさ">}}
## 形状について

2次元空間で2つの物体の衝突を確認する方法として、大雑把に分けると次のようなものがあります。

- 両方とも矩形（くけい）とみなし、x距離/y距離 を確認する
- 両方とも円形とみなし、中心点同士の距離を確認する
- それぞれ別個に形状の情報をもたせ、形状の組み合わせに応じて適した処理をする

※ 矩形は長方形と同じ意味。この文脈では矩形と呼ぶことの方がたぶん多い  
※ 3次元だと矩形や円の代わりに立方体や球になる

このなかで最も処理効率が良いのは、矩形同士の判定です。

例えばストⅡなどの2D格闘ゲームでも、キャラ同士の当たり判定を矩形の組み合わせで行っていたようです。  
参考： [https://game.capcom.com/cfn/sfv/column-130393.html](https://game.capcom.com/cfn/sfv/column-130393.html)

## 大きさについて

今回は表示する四角形の大きさと当たり判定に使う矩形の大きさを一致させていますが、その必然性は無いことに注意してください。

また、今回の例だと当たるとゲームオーバーになりますが、遊ぶ人に「えぇ～いまの当たってなかったでしょ！！」と思わせてしまうと満足感が下がってしまいます。逆に、当たったように見えてゲームオーバーにならなかった場合は「ラッキー！ 当たらなかった」くらいにしか思われないので、表示されているグラフィックよりも当たり判定部分の大きさを少し小さめにするくらいでちょうどよい、ということも多いです。
{{< /expand >}}


## 実装

1. 上述の衝突判定関数を用意する
1. **プレイヤー 対 各ブロック** について判定し、衝突していたらゲームオーバー

```javascript { hl_lines=["60-86", "146-152"], linenostart=1 }
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

function playerIsAlive(entity) {
  // プレイヤーの位置が生存圏内なら true を返す。
  // 600 は画面の下端
  return entity.y < 600;
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

// 複数のエンティティを処理する関数

/**
 * 2つのエンティティが衝突しているかどうかをチェックする
 *
 * @param entityA 衝突しているかどうかを確認したいエンティティ
 * @param entityB 同上
 * @param collisionXDistance 衝突しないギリギリのx距離
 * @param collisionYDistance 衝突しないギリギリのy距離
 * @returns 衝突していたら `true` そうでなければ `false` を返す
 */
function entitiesAreColliding(
  entityA,
  entityB,
  collisionXDistance,
  collisionYDistance
) {
  // xとy、いずれかの距離が十分開いていたら、衝突していないので false を返す

  let currentXDistance = abs(entityA.x - entityB.x); // 現在のx距離
  if (collisionXDistance <= currentXDistance) return false;

  let currentYDistance = abs(entityA.y - entityB.y); // 現在のy距離
  if (collisionYDistance <= currentYDistance) return false;

  return true; // ここまで来たら、x方向でもy方向でも重なっているので true
}

// ---- ゲーム全体に関わる部分 --------------------------------------------

/** プレイヤーエンティティ */
let player;

/** ブロックエンティティの配列 */
let blocks;

/** ゲームの状態。"play" か "gameover" を入れるものとする */
let gameState;

/** ブロックを上下ペアで作成し、`blocks` に追加する */
function addBlockPair() {
  let y = random(-100, 100);
  blocks.push(createBlock(y)); // 上のブロック
  blocks.push(createBlock(y + 600)); // 下のブロック
}

/** ゲームオーバー画面を表示する */
function drawGameoverScreen() {
  background(0, 192); // 透明度 192 の黒
  fill(255);
  textSize(64);
  textAlign(CENTER, CENTER); // 横に中央揃え ＆ 縦にも中央揃え
  text("GAME OVER", width / 2, height / 2); // 画面中央にテキスト表示
}

/** ゲームのリセット */
function resetGame() {
  // 状態をリセット
  gameState = "play";

  // プレイヤーを作成
  player = createPlayer();

  // ブロックの配列準備
  blocks = [];
}

/** ゲームの更新 */
function updateGame() {
  // ゲームオーバーなら更新しない
  if (gameState === "gameover") return;

  // ブロックの追加と削除
  if (frameCount % 120 === 1) addBlockPair(blocks); // 一定間隔で追加
  blocks = blocks.filter(blockIsAlive); // 生きているブロックだけ残す

  // 全エンティティの位置を更新
  updatePosition(player);
  for (let block of blocks) updatePosition(block);

  // プレイヤーに重力を適用
  applyGravity(player);

  // プレイヤーが死んでいたらゲームオーバー
  if (!playerIsAlive(player)) gameState = "gameover";

  // 衝突判定
  for (let block of blocks) {
    if (entitiesAreColliding(player, block, 20 + 40, 20 + 200)) {
      gameState = "gameover";
      break;
    }
  }
}

/** ゲームの描画 */
function drawGame() {
  // 全エンティティを描画
  background(0);
  drawPlayer(player);
  for (let block of blocks) drawBlock(block);

  // ゲームオーバー状態なら、それ用の画面を表示
  if (gameState === "gameover") drawGameoverScreen();
}

/** マウスボタンが押されたときのゲームへの影響 */
function onMousePress() {
  switch (gameState) {
    case "play":
      // プレイ中の状態ならプレイヤーをジャンプさせる
      applyJump(player);
      break;
    case "gameover":
      // ゲームオーバー状態ならリセット
      resetGame();
      break;
  }
}

// ---- setup/draw 他 --------------------------------------------------

function setup() {
  createCanvas(800, 600);
  rectMode(CENTER);

  resetGame();
}

function draw() {
  updateGame();
  drawGame();
}

function mousePressed() {
  onMousePress();
}
```


{{< hint info >}}
動かしてみてみましょう。  
プレイヤーがブロックに当たったらゲームオーバーになることを確認してください。
{{< /hint >}}


{{< expand "余談： マジックナンバー" >}}
## 問題

```javascript { linenos=false }
if (entitiesAreColliding(player, block, 20 + 40, 20 + 200)) {
  // ...
}
```

ここの第3-4引数、「衝突しないギリギリの距離（x, y）」をプレイヤーの大きさとブロックの大きさを元にして決めているわけですが、パッと見なんだかよくわからないので、あまり良い書き方ではありませんね。

これに限らず、コードのあちこちで数字を直接書いているので、「なにこの数字？」と思わせてしまう箇所が他にもあるかもしれません。

こういうソースコード中に書かれた数字を**マジックナンバー**と言います。  
参考： [マジックナンバー（プログラム） - Wikipedia](https://ja.wikipedia.org/wiki/%E3%83%9E%E3%82%B8%E3%83%83%E3%82%AF%E3%83%8A%E3%83%B3%E3%83%90%E3%83%BC_(%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%A0))

今回は素朴な書き方で進めましたが、こういったものは極力減らしたほうが後々のためになります。対処しない場合、たとえば数値を変えたいときに影響箇所を探しにくいというデメリットもあります。

## 対処

### 説明変数

なんだこの数字？ と思わせてしまうのを避ける最も手軽な方法はコメントを付けることだと思われますが、場合によっては説明変数というものを書いたほうが読みやすくなります。

問題の数値を使う直前のところで、いったんそれを変数（定数）として宣言しておくというもので、上記の `entitiesAreColliding()` の例で言うと、（命名にあまり自信ないのですが）次のような感じです。

```javascript { linenos=false }
const playerHalfWidth = 20;
const playerHalfHeight = 20;
const blockHalfWidth = 40;
const blockHalfHeight = 200;
const collisionXDistance = playerHalfWidth + blockHalfWidth;
const collisionYDistance = playerHalfHeight + blockHalfHeight;

if (
  entitiesAreColliding(player, block, collisionXDistance, collisionYDistance)
) {
  // ...
}
```

このように、数値の意味などを説明する目的で宣言する変数を**説明変数**と言います。

コメントだと厳密にどの部分を指しているのか分かりにくく、「この〇〇っていう関数の、この第3引数に入れた数値の意味はですねぇ、……」みたいに迂遠になりがちなところ、説明変数は問題の対象をダイレクトで説明できます。

### 定数をまとめる

どうせ定数なんだったら一箇所でまとめて定義しておいたら分かりやすいのでは？

ということで、次のような対処がありえます。

1. プレイヤーとブロックの「描画する四角形のサイズ（width/height）」を定数として定める
1. 同じ場所に、プレイヤーとブロックの「衝突判定に用いる x/y 距離」を定数として定める
1. 1 と 2 はゆるやかに連動していて、どちらかを変えたらもう一方も調整が必要である旨、注釈を書いておく
1. `entitiesAreColliding()` の引数は、2 の数値を加算したものを入れる、  
もしくは加算した結果をさらに別の定数として定める

### グループ管理用オブジェクト

定数をズラッと並べるのが美しくないと感じる場合は、プレイヤーやブロックといった単位でエンティティの種類を管理するための「エンティティグループ」という概念を新たに考えるという方法もあります。

実装は、以下のような情報をまとめたオブジェクトという形になるでしょう。

- エンティティの配列
- 描画用の関数など、そのグループ固有の関数群
- 描画の大きさや衝突判定用の数値など、そのグループ固有のデータ
{{< /expand >}}

{{< expand "余談： 関数名 xxxIsAlive() の命名" >}}
衝突したらプレイヤーエンティティは死ぬ、とみなすほうが自然で、`playerIsAlive()` ではプレイヤーエンティティが画面下端まで落ちたかどうかしか分からない、これは名が体を表していないのでは？ という問題があります。

これはちょっと良い状態ではなくて、筆者も動作確認時にちょっと混乱したので、要改善な気がします。

位置の判定と衝突の判定を別々に処理している以上、処理結果を保存しておく必要があるはずで、となるとやっぱりエンティティに生死フラグを持たせるべきなのかもしれません。
{{< /expand >}}


{{< expand "余談： 衝突判定の最適化">}}
今回の例では総当たり的に衝突判定をしていますがこの場合、例えば1000対1000の衝突があったりすると百万回のループが必要になり（まあ極端な例なので普通は無いですが、破壊可能弾の弾幕を打ち合う場合とか。敵方が1000というのは普通にありえます）、しかもそれを継続的に繰り返さなければなりません。つまり、衝突判定は全体の中でもパフォーマンス上のボトルネックになりやすい要素の一つなのです。

そのため、無駄な判定を削減するような高度な工夫がいろいろ存在します（筆者は自力でやったことはありません）。  
参考： [JavaScriptで大量のオブジェクトの当たり判定を効率的にとる](https://sbfl.net/blog/2017/12/03/javascript-collision/)

単純な削減方法としては、2フレームに1回判定すれば十分でしょ、みたいなサボり方をするというのがあり、市販のゲームでもそうしているケースはあるようです。半分になるだけで、桁が減るわけではないのですが（「計算量」の概念を調べよう）、キリキリ削減しなきゃいけない場合にはこういうのも選択肢になります。
{{< /expand >}}


{{< expand "余談： その他、課題となりえる要素">}}
- 物体の動きが速すぎると、衝突する範囲を飛び越えてすり抜けてしまう可能性があり、ちゃんとした物理エンジンはそういうのもチェックしたりするようです。
- 衝突後の処理。衝突した時点でゲームが止まったり、どちらかが消滅したりすると楽なのですが、そうでない場合は押し返したり反発したりといった処理が必要になります。特にマリオ的なのを作ろうとすると、マリオとブロックとの衝突判定で変なところに引っかかったりして意外と苦労するかもしれません。
{{< /expand >}}

---

## 一区切り

{{< hint info >}}
以上で、Flappy Bird ライクなゲームの基本的なルールが一通り実装できました。  
おめでとうございます！

さて、本当に面白いのは、これをベースにしてどんなふうにでもアレンジできる上に、  
同じ骨組みを使って全く別のゲームも作れてしまうという点です。

この先もぜひ自由に改造してみましょう。  
[「アレンジいろいろ」](../../arrange) にていくつか選択肢を示していますが、  
他にも可能性は無限大です。
{{< /hint >}}

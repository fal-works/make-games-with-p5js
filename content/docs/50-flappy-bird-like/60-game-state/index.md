---
title: ゲームオーバーを実装
type: docs
weight: 60
---

# ゲームオーバーを実装

ゲームオーバーになる条件は

1. プレイヤーが画面下端まで落ちる
1. プレイヤーがブロックに衝突する

だったが、このうち 1. は 2. より簡単そうなので、このタイミングで実装する

加えて、ゲームオーバー後にクリックしたらリセット → もう一度遊べるようにする


## 準備： setup/draw の中身を整理する

### 理由

- 初期化処理が `setup()` の中にあると「リセットして最初に戻る」のがやりにくい
- `draw()` の中身が増えてきたので、更新処理／描画処理 の2つに分けて整理したい

### やること

以下のそれぞれについて、新たに専用の関数を用意して引っ越す

1. `setup()` の中の初期化処理
1. `draw()` の中の更新処理
1. `draw()` の中の描画処理
1. `mousePressed()` の中身（マウスボタンが押されたときの処理）


### まず引越し先を用意する

```javascript { hl_lines=["16-34"], linenostart=60 }
// ---- ゲーム全体に関わる部分 ----------------------------------------------

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

/** ゲームの初期化・リセット */
function resetGame() {
  // （あとで）
}

/** ゲームの更新 */
function updateGame() {
  // （あとで）
}

/** ゲームの描画 */
function drawGame() {
  // （あとで）
}

/** マウスボタンが押されたときのゲームへの影響 */
function onMousePress() {
  // （あとで）
}
```


### 引っ越す

「setup/draw 他」セクションに書いてあった処理が、特に変更されることなく  
「ゲーム全体に関わる部分」セクションにごっそり移動している

```javascript { hl_lines=["18-22", "27-36", "41-44", "49-50", 59, "63-64", 68], linenostart=60 }
// ---- ゲーム全体に関わる部分 ----------------------------------------------

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

/** ゲームの初期化・リセット */
function resetGame() {
  // プレイヤーを作成
  player = createPlayer();

  // ブロックの配列準備
  blocks = [];
}

/** ゲームの更新 */
function updateGame() {
  // ブロックの追加と削除
  if (frameCount % 120 === 1) addBlockPair(blocks); // 一定間隔でブロック追加
  blocks = blocks.filter(blockIsAlive); // 生きているブロックだけ残す

  // 全エンティティの位置を更新
  updatePosition(player);
  for (let block of blocks) updatePosition(block);

  // プレイヤーに重力を適用
  applyGravity(player);
}

/** ゲームの描画 */
function drawGame() {
  // 全エンティティを描画
  background(0);
  drawPlayer(player);
  for (let block of blocks) drawBlock(block);
}

/** マウスボタンが押されたときのゲームへの影響 */
function onMousePress() {
  // プレイヤーをジャンプさせる
  applyJump(player);
}

// ---- setup/draw 他 ---------------------------------------------------

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


## プレイ／ゲームオーバー の2状態を作って切り替える

### ゲームオーバー状態がプレイ状態と異なる点

- 画面の動きが止まる
- "GAME OVER" と表示される
- マウスボタンを押したらリセットされる

### やること

1. プレイ／ゲームオーバー の状態を管理する変数を用意する
1. ゲームのリセット処理
    - プレイ状態で始まるようにする
1. ゲームの更新処理
    - ゲームオーバー状態のときは動きを止める（エンティティを更新しない）
    - プレイヤーが死んでいたらゲームオーバー状態にする
1. ゲームの描画処理
    - ゲームオーバー状態のときは通常の描画に加えて "GAME OVER" と表示する
1. マウスボタンを押したときの処理
    - プレイ状態ならジャンプ（従来どおり）
    - ゲームオーバー状態ならリセット


### 必要な関数が揃っている、と仮定して書いてみる

※ このコードは動きません（`playerIsAlive()` と `drawGameoverScreen()` が存在しないため）

```javascript { hl_lines=["9-10", "21-22", "33-34", "47-48", "58-59", "64-73"], linenostart=60 }
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

/** ゲームの初期化・リセット */
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
  if (frameCount % 120 === 1) addBlockPair(blocks); // 一定間隔でブロック追加
  blocks = blocks.filter(blockIsAlive); // 生きているブロックだけ残す

  // 全エンティティの位置を更新
  updatePosition(player);
  for (let block of blocks) updatePosition(block);

  // プレイヤーに重力を適用
  applyGravity(player);

  // プレイヤーが死んでいたらゲームオーバー
  if (!playerIsAlive(player)) gameState = "gameover";
}

/** ゲームの描画 */
function drawGame() {
  // 全エンティティを描画
  background(0);
  drawPlayer(player);
  for (let block of blocks) drawBlock(block);

  // ゲームオーバーならそれ用の画面を表示
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
```


### 必要な関数を実装して完成

`playerIsAlive()` と `drawGameoverScreen()` を実装しただけ

```javascript { hl_lines=["33-37", "78-85"], linenostart=1 }
// ---- エンティティ関連の関数 -------------------------------------------------

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

/** ゲームの初期化・リセット */
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
  if (frameCount % 120 === 1) addBlockPair(blocks); // 一定間隔でブロック追加
  blocks = blocks.filter(blockIsAlive); // 生きているブロックだけ残す

  // 全エンティティの位置を更新
  updatePosition(player);
  for (let block of blocks) updatePosition(block);

  // プレイヤーに重力を適用
  applyGravity(player);

  // プレイヤーが死んでいたらゲームオーバー
  if (!playerIsAlive(player)) gameState = "gameover";
}

/** ゲームの描画 */
function drawGame() {
  // 全エンティティを描画
  background(0);
  drawPlayer(player);
  for (let block of blocks) drawBlock(block);

  // ゲームオーバーならそれ用の画面を表示
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

// ---- setup/draw 他 -------------------------------------------------------

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

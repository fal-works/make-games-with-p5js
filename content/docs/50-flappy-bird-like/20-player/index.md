---
title: プレイヤーだけ実装
type: docs
weight: 20
---

# プレイヤーだけ実装してみる

## やること

1. プレイヤーを入れる変数を用意する
1. `setup()` の中で、プレイヤーを作成する
1. `draw()` の中で、プレイヤーについて以下を行う
    - 位置の更新
    - 重力の適用
    - 描画
1. マウスボタンを押したときだけ、プレイヤーをジャンプさせる


## ソースコード全体の構成を決めておく

整理のため、以下のようにセクションを分けることにする

1. エンティティ関連の関数
1. ゲーム全体に関わる部分
1. setup/draw 他

```javascript
// ---- エンティティ関連の関数 --------------------------------------------------

// （ここに何かが入る）

// ---- ゲーム全体に関わる部分 --------------------------------------------------

// （ここに何かが入る）

// ---- setup/draw 他 -------------------------------------------------------

function setup() {
  createCanvas(800, 600);

  // （ここに初期化処理が入る）
}

function draw() {
  // （ここにデータ操作処理が入る）

  background(0);
  // （ここに描画処理が入る）
}

function mousePressed() {
  // （ここにマウスボタンを押したときの処理が入る）
}
```


## さっき考えたエンティティ関連の処理（再掲）

### 全エンティティ共通

- 位置の更新

### プレイヤー用

- 作成
- 重力の適用
- ジャンプ
- 描画

### ブロック用

- 作成
- 描画


## 必要な関数が揃っている、と仮定して書いてみる

```javascript
// ---- エンティティ関連の関数 --------------------------------------------------

// （あとで）

// ---- ゲーム全体に関わる変数 --------------------------------------------------

/** プレイヤーエンティティ */
let player;

// ---- setup/draw 他 ----------------------------------------------------------

function setup() {
  createCanvas(800, 600);
  rectMode(CENTER); // 四角形の基準点を角から中心に変える

  // プレイヤーを作成
  player = createPlayer();
}

function draw() {
  // プレイヤーの位置を更新
  updatePosition(player);

  // プレイヤーに重力を適用
  applyGravity(player);

  // プレイヤーを描画
  background(0);
  drawPlayer(player);
}

function mousePressed() {
  // プレイヤーをジャンプさせる
  applyJump(player);
}
```


## 必要な関数を用意する

```javascript
// ---- エンティティ関連の関数 --------------------------------------------------

// 全エンティティ共通

function updatePosition(entity) {
  entity.x += entity.vx;
  entity.y += entity.vy;
}

// プレイヤーエンティティ

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
```

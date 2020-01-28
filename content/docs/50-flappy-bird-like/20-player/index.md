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
  rectMode(CENTER);

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

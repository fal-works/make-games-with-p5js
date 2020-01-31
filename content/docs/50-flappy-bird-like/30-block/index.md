---
title: ブロック1個だけ実装
type: docs
weight: 30
---


# ブロックを1個だけ実装してみる

## 【再掲】エンティティ関連の処理

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


## やること

プレイヤーのときと流れは同じ

1. ブロックを入れる変数を用意する
1. `setup()` の中で、ブロックを作成する
1. `draw()` の中で、ブロックについて以下を行う
    - 位置の更新
    - 描画


## 必要な関数が既に揃っている、と仮定して書いてみる

```javascript { hl_lines=["6-7", "18-19", 23, 25, 30, 33], linenostart=33 }
// ---- ゲーム全体に関わる部分 --------------------------------------------------

/** プレイヤーエンティティ */
let player;

/** ブロックエンティティ */
let block;

// ---- setup/draw 他 ----------------------------------------------------------

function setup() {
  createCanvas(800, 600);
  rectMode(CENTER);

  // プレイヤーを作成
  player = createPlayer();

  // ブロックを作成
  block = createBlock(300); // 指定したy座標で作成。とりあえず画面中央の高さで
}

function draw() {
  // 全エンティティの位置を更新
  updatePosition(player);
  updatePosition(block);

  // プレイヤーに重力を適用
  applyGravity(player);

  // 全エンティティを描画
  background(0);
  drawPlayer(player);
  drawBlock(block);
}

function mousePressed() {
  // プレイヤーをジャンプさせる
  applyJump(player);
}
```


## 必要な関数を実際に用意する

```javascript { hl_lines=["33-46"], linenostart=1 }
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
```

{{< hint info >}}
各自実行してみて、ブロックが画面右端から現れ、左側に向かって移動してくることを確認してください。
{{< /hint >}}

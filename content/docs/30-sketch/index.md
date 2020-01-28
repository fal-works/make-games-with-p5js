---
title: p5.js スケッチの基本構造
type: docs
weight: 30
---

# p5.js スケッチの基本構造

[p5.js Web Editor](https://editor.p5js.org/) の操作確認も兼ねて、以下のコードを実行

```javascript
function setup() {
  createCanvas(800, 600); // 800 x 600 ピクセル。今回このサイズでやっていきます
}

function draw() {
  background(0); // 黒背景。後で自由に変えていただきます
}
```

- 最初に1回だけ `setup()` が実行される
- `draw()` が繰り返し実行されることで、動いているように見える
  - この1回1回を「フレーム」と呼ぶ
  - デフォルトでは毎秒60回実行される。  
これを「フレームレートは 60 FPS」などと言う（FPS = frames per second）
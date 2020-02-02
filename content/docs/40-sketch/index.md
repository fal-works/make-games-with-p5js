---
title: p5.js スケッチの基本構造
type: docs
weight: 40
---

# p5.js スケッチの基本構造

## 空っぽのスケッチ

{{< hint info >}}
[p5.js Web Editor](https://editor.p5js.org/) （あるいはその他、お使いの環境）の操作確認も兼ねて、  
以下のコードを実行してみてください。
{{< /hint >}}

```javascript
function setup() {
  createCanvas(800, 600); // 800 x 600 ピクセル。今回このサイズでやっていきます
}

function draw() {
  background(0); // 黒背景。後で自由に変えていただきます
}
```

- 最初に1回だけ `setup()` が実行される
- `draw()` が繰り返し実行されることで、動いているように見せられる（この時点では真っ黒）
  - この1回1回を「フレーム」と呼ぶ
  - デフォルトでは毎秒60回実行される。  
これを「フレームレートは 60 FPS」などと言う（FPS = frames per second）

## リンク

### p5.js チートシート

[https://bmoren.github.io/p5js-cheat-sheet/ja.html](https://bmoren.github.io/p5js-cheat-sheet/ja.html)  

ここに書いていないものとしては、システム変数 `frameCount` なども使う  
（`draw()` が呼ばれるたびに1ずつ増える値）

### 公式のリファレンス

[https://p5js.org/reference/](https://p5js.org/reference/)

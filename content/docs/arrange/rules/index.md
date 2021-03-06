---
title: ルール変更
type: docs
weight: 30
---

# ルール変更

{{< hint info >}}
今回作ってきたプログラムは、少し手を加えるだけで色々なゲームに化けることができます。

どんな改造ができるでしょうか？

思いつくままに改造してみましょう。

いくつか案を列挙しますが（サンプルコードはありません）、  
他にもないか自分なりに考えてみても良いでしょう。

仮にゲームの体を成していない状態になっても、それはそれでアリだと思います。
{{< /hint >}}


{{< expand "補足： 現行プログラムの改善点">}}
ちなみに、改造前のルールにしても完全ではありません。  
画面上端からさらに上へといくらでも飛べてしまって、ブロック避け放題だからです。  
プレイヤーのy座標を制限する処理を入れても良いでしょう。

また、スコアがあったほうが遊びがいがあります。  
そのためには、ブロックを避けられたら得点アップ、という処理を入れる必要があります。  
実装としては、

- 上下のブロックの間に不可視のエンティティを入れて、衝突したらそれを殺して得点
- ブロックの出現間隔と移動速度が一定な場合、得点アップのタイミングが正確に予測できるので、`frameCount` の値が特定の値になったら得点

などが考えられます。
{{< /expand >}}


## ルール変更案

おおむね簡単順。後ろの方のはちょっと力が要るかも

### 数値調整

- 移動速度や重力の強さなどの数値を変えたら、遊び心地はどう変わってくる？

### ブロック関連

- ブロックの移動速度にばらつきがあったらどうだろう？
- ブロックがちょっとだけ縦方向にも動くようにしたら？
- ブロックを小さくして、代わりに数を増やしたらどうだろう？  
（描画処理と衝突判定処理の両方で数値変更が必要なことに注意）

### 操作方法

- クリックでジャンプではなく、ボタンを押している間だけ、  
継続的に揚力（浮き上がる力）を与えたらどうだろう？
- 重力を廃止し、マウスカーソルのy座標に応じて追随or加速するようにしては？
- キーボード操作にするのはどうか？

### ルール追加

- 何度かミスってもOK、にしてみては？  
（ミスった直後の一定時間は無敵、などの処理が必要）
- ブロックという障害物だけでなく、コインやアイテムが飛んできて、  
衝突したら得点アップ！ とかはどうか？ （要：得点のカウント＆表示処理）
- 何らかの条件で、衝突時にブロックを破壊可能にしてみては？

### ルール大幅変更

- 攻撃を発射してブロックを破壊可能に（もはやシューティング）
- 地面を追加して着地可能にし、ジャンプしたりしなかったりして避ける（もはやマリオ）
- ブロック崩しゲームにしようぜ！！！１


{{< expand "余談： 換骨奪胎">}}
上の「ルール大幅変更」は、即座にはできないにしても、実際のところ十分に可能です。

ここまで作ってきたプログラムで、土台はある程度できているからです。

たとえば、Flappy Bird と古典的なブロック崩しとの違いは、おおむね次のとおりです。

||Flappy Bird|ブロック崩し|
| ---- | ---- | ---- |
|エンティティ|プレイヤー・ブロック|プレイヤー・ボール・ブロック|
|プレイヤー|画面左で上下移動(+重力)|画面下端で左右に移動|
|ブロック|右から出現、左へ移動して削除|位置固定。衝突で削除|
|操作方法|クリックでジャンプ|マウスやキーで移動|
|衝突する相手|プレイヤー ↔ ブロック|プレイヤー ↔ ボール ↔ ブロック|
|〃||ボール ↔ 画面枠|
|衝突時の処理|ゲームオーバー|ボールは反発、ブロックは削除|
|勝利条件|なし|全ブロック破壊|
|敗北条件|落ちる or 当たる|ボールを取り逃す|

つまり、このそれぞれに該当する部分を地道に書き換えればよく、

- エンティティの概念
- 複数エンティティの管理（追加・更新・削除）
- ゲーム状態（ゲームオーバー等）の概念

などのような全体の枠組みはたいして変わらない、ということが分かります。
{{< /expand >}}

## 市販ゲームの例

### 『BIT.TRIP Runner』

地面でジャンプ ＋ コイン／アイテム ＋ スライディング＆障害物破壊 ＋ リズムゲーム化

[BIT.TRIP Runner - Trailer - YouTube](https://www.youtube.com/watch?v=RKFJHH9iklQ)

![BIT.TRIP Runner image](https://img.youtube.com/vi/RKFJHH9iklQ/0.jpg)



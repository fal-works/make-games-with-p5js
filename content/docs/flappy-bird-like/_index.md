---
title: Flappy Bird ライクなゲーム
type: docs
weight: 20
---

# Flappy Bird ライクなゲーム

## Flappy Bird とは

[Flappy Bird Gameplay - IGN - YouTube](https://www.youtube.com/watch?v=fQoJZuBwrkU)

{{< figure src="https://img.youtube.com/vi/fQoJZuBwrkU/0.jpg" alt="Flappy Bird (image)" width="320" height="240" >}}

- 土管にぶつからないように鳥を操作する、非常にシンプルなモバイルゲーム
- 開発者： Dong Nguyen
- 2014年頃に爆発的に流行（現在は削除済み）
- いろいろなゲームの中でも特に作りやすく、かつ基礎を学ぶにも適している

## 今回作るゲームの概要

Flappy Bird を抽象化した、次のようなミニゲームを作る  
（この例では見た目に全く気を使っていませんが、各自アレンジしていただきます）

![Abstract Flappy Bird](./flappy-bird-like.gif)

### ルール

- マウスボタンを押すとジャンプ
- 画面下端まで落ちるか、障害物にぶつかるとゲームオーバー
- （本当は点数もカウントして表示したいけど、一旦省略）

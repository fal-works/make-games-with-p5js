---
title: 画像や音を使う
type: docs
weight: 40
---

# 画像や音を使う

{{< hint info >}}
単純な図形にとどまらず、もっとリッチな見た目にしたい人。  
音楽や効果音を流してみたい人。

申し訳ないことにサンプルコードはありませんが、  
主要な関数などを挙げておきますのでご参考になさってください。

素材次第で印象が圧倒的に違ってきますので、ぜひチャレンジしてみると良いでしょう。
{{< /hint >}}


## 画像を使う

### 画像ファイルを読み込む

- [preload()](https://p5js.org/reference/#/p5/preload)
- [loadImage()](https://p5js.org/reference/#/p5/loadImage)

### ソースコード内で自分で画像を作る

- [createGraphics()](https://p5js.org/reference/#/p5/createGraphics)

### 用意した画像を表示する

- [image()](https://p5js.org/reference/#/p5/image)
- [imageMode()](https://p5js.org/reference/#/p5/imageMode)


## 音を使う

### 音声ファイルを読み込む

- [preload()](https://p5js.org/reference/#/p5/preload)
- [loadSound()](https://p5js.org/reference/#/p5.SoundFile/loadSound)

### 音声ファイル読み込みで使うかもしれない関数

場合によるかもしれない

- [soundFormats()](https://p5js.org/reference/#/p5/soundFormats)
- [userStartAudio()](https://p5js.org/reference/#/p5.sound/userStartAudio)

### 音声を再生する

読み込みによって [p5.SoundFile](https://p5js.org/reference/#/p5.SoundFile) クラスのインスタンスが得られるので、  
そのメソッド（`play()` など）を使う


## ライセンス

フリー素材などを利用する場合、「利用規約」「ライセンス」などの条項を要確認

### Creative Commons

比較的有名なライセンスの一つに [Creative Commons](https://creativecommons.jp/licenses/) がある

利用許可の条件にいくつか種類があるが、著作権情報の表示義務は共通  
→ 具体的な表示方法： [Best practices for attribution - Creative Commons](https://wiki.creativecommons.org/wiki/Best_practices_for_attribution)

なお組み合わせ可能な条件の一つである「改変禁止(ND)」の原語は "No Derivatives"  
→ 素材としての利用もNGという可能性があるため注意（確かなことは個別に確認を）
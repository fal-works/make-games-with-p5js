---
title: 準備
type: docs
weight: 10
---

# 準備

このワークショップでは [p5.js Web Editor](https://editor.p5js.org/) を使います。

[![p5.js Web Editor](./web-editor.png)](https://editor.p5js.org/)

ユーザーアカウントを持っていない人は、右上の "Sign Up" から登録してください。  
（スケッチの保存・共有機能を使っていただきたいため）

※ 使い慣れたエディタがあればそれを使い、保存だけ Web Editor で行う、というのでも可

※ 使い慣れたエディタがなく、p5.js Web Editor で画面が2分割されているのが狭く感じる人は、  
[OpenProcessing](https://www.openprocessing.org/) を使ってみるのも良いと思われます


{{< expand "余談： 環境関連の諸々">}}
[NEORT](https://neort.io/) や [OpenProcessing](https://www.openprocessing.org/) などは、エディタ兼作品公開プラットフォームとしても機能します。ここでスケッチを公開することも検討すると良いでしょう。

ローカルで使うための強力なエディタとしては、例えば [Visual Studio Code](https://azure.microsoft.com/ja-jp/products/visual-studio-code/) などがあります。  
参考： [PCD2019の田所先生のワークショップ資料](https://yoppa.org/pcd19)

他にもいろいろ便利なツールがあります。例えば……
- [ESLint](https://eslint.org/) は、推奨される書き方から外れたコードについて警告したり自動で修正したりしてくれます。  
- [Prettier](https://prettier.io/) は、スペースや改行や括弧の有無などを一定の規則で自動整形してくれます。
- ツールと呼ぶのも微妙ですが [TypeScript](https://www.typescriptlang.org/) は、JavaScript に静的型付けを導入した言語です。
- モジュールバンドラ（[webpack](https://webpack.js.org/) 他色々）は、複数のファイルに分けて書いたソースコードを統合してくれます。
- パッケージ管理ツール（[npm](https://www.npmjs.com/) や [Yarn](https://yarnpkg.com/lang/ja/)）は、以上のような各種ツールのダウンロードや実行を補佐してくれます。

なにいってだこいつ、と思った人は正常です。これがジャバスクリプトだ！  
気楽に書けるのがメリットであるはずの p5.js で、がっちり環境を整える人はあまり多くはないでしょうから、慣れてきてから調べても遅くないと思います。
{{< /expand >}}

---
title: 準備
type: docs
weight: 10
---

# 準備

{{< hint info >}}
このワークショップでは [p5.js Web Editor](https://editor.p5js.org/) を使います。

ユーザーアカウントを持っていない人は、エディタ右上の "Sign Up" から登録してください。  
（スケッチの保存・共有機能を使っていただきたいため。ダミー的なアカウントでも可）

登録できたら、下記の表に書いて教えて下さい。  
[一覧表（Googleスプレッドシート）](https://docs.google.com/spreadsheets/d/1cprOYEDthfGgYqv4e8jD0QVGH_RAann-f_LLSpgYFjU/edit?usp=sharing)  
※ この表とリンクはワークショップ終了後に削除します
{{< /hint >}}

[![p5.js Web Editor](./web-editor.png)](https://editor.p5js.org/)

{{< hint info >}}
- 使い慣れたエディタで編集し、保存だけ Web Editor で行う、というのもOKです。

- p5.js Web Editor では画面が2分割されています。  
境界線はドラッグで動かせますが、それでも狭い（全画面がいい）と感じる人は、  
[OpenProcessing](https://www.openprocessing.org/) のエディタで編集しても良いかもしれません。  
（"Join" ボタンからアカウント登録が必要ですが）
{{< /hint >}}

{{< expand "余談： 環境関連の諸々（いますぐ読む必要はありません）">}}
[NEORT](https://neort.io/) や [OpenProcessing](https://www.openprocessing.org/) などは、エディタ兼作品公開プラットフォームとして機能します。不特定多数の人にスケッチを見てもらいたいときは、ここで公開するのも良いでしょう。

ローカルで使うための強力なエディタとしては、例えば [Visual Studio Code](https://azure.microsoft.com/ja-jp/products/visual-studio-code/) などがあります。  
参考： [PCD2019の田所先生のワークショップ資料](https://yoppa.org/pcd19)

初めてだと使い方に戸惑うかもしれませんが、他にもいろいろツールがあります。例えば……
- [ESLint](https://eslint.org/) は、推奨される書き方から外れたコードについて警告したり自動で修正したりしてくれます。  
- [Prettier](https://prettier.io/) は、スペースや改行や括弧の有無などを一定の規則で自動整形してくれます。
- ツールと呼ぶのも微妙ですが [TypeScript](https://www.typescriptlang.org/) は、JavaScript に静的型付けを導入した言語です。
- モジュールバンドラ（[webpack](https://webpack.js.org/) 他色々）は、複数のファイルに分けて書いたソースコードを統合してくれます。
- パッケージ管理ツール（[npm](https://www.npmjs.com/) や [Yarn](https://yarnpkg.com/lang/ja/)）は、以上のような各種ツールのダウンロードや実行を補佐してくれます。

なにいってだこいつ、と思った人は正常です。これがジャバスクリプトだ！  
気楽に書けるのがメリットであるはずの p5.js で、がっちり環境を整える人はあまり多くはないでしょうから、慣れてきてから調べても遅くないと思います。
{{< /expand >}}

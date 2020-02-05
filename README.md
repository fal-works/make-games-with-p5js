# p5.js でゲーム制作

## ビルド環境

- hugo v0.64.0
- hugo-extended v0.64.0
- prettier v1.19.1

## 修正後の公開手順

1. `hugo --cleanDestinationDir`
2. `prettier --write docs/**/*.html --print-width 120`
3. docs/index.html の `title` タグを手修正
4. コミット
5. `git push`

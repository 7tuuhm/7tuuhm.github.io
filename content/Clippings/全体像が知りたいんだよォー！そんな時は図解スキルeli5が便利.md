---
title: 全体像が知りたいんだよォー！そんな時は図解スキルeli5が便利
source: https://eiji.page/blog/ai-skill-eli5-is-great/
author:
published: 2026-10-01
created: 2026-10-02
description: ソフトウェアに関する備忘録を書く個人ブログです！
tags:
  - clippings
updated: 2026-10-02T00:06
---
AIを使った開発によってスピードが上がっているのは実感するものの、それと引き換えに **自分がちゃんと理解できなくてふわっとした部分** が増えてきました。  
そんなとき、自分の理解が合っているか音声入力でバーっと喋ってAIとやりとりします。  
他の人が書いたコードの機能の概要・ロジックなんかも同じです。

ただ、複雑すぎて「全体像を見たい！」「矢印とか使ってイイ感じな図を見たい！」と思うことがしばしば。

そんな時に図解のスキル [eli5](https://github.com/anthropics/claude-plugins-community/tree/main/eli5) が便利なので紹介します。

## \## eli5とは

[eli5](https://github.com/anthropics/claude-plugins-community/tree/main/eli5) は解説用スキルです。

「 **E** xplain any topic **L** ike **I** ’m **5** （5歳児だと思って説明して）」というのが由来です。

### \### 実際に見てみる

見たほうが早いので実物を。

![eli5での例](https://storage.eiji.page/ai-skill-eli5-is-great/eli5-example.avif)

HTMLで適度に色を付けたりして見やすく整理してくれます。説明で使われる言葉も簡単です。

### \### 実際に使うときに投げているプロンプト

単に `/eli5 〇〇について解説して` だけで十分ですが、実際に筆者が投げているプロンプトも書いておきます。

```markdown
opensrcで[ghostty](https://github.com/ghostty-org/ghostty)を取得して、その中の libghostty について どういう役割で、libghosttyがやってくれること・やってくれないことを eli5でHTMLにまとめてほしい
ファイルは .mywork/work-logs/ に置いて。既存のファイルのフォーマットは無視して。
```

opensrcは指定の場所にソースコードを落とすスキル兼ツールです。別の記事で解説しているので知らない人はどうぞ。  
[AIにコードを読ませるならopensrc！調査コードを一元管理](https://eiji.page/blog/opensrc-intro/)

筆者の場合、置き場所として指定した `.mywork/work-logs/` の中にMarkdownファイルがたくさんあります。それゆえにHTMLではなくMarkdownとして出力されてしまうことがあったため、HTML指定を明記してます。

---

以上、スキルの布教でした。ソフトウェア開発をするモモンガ、無さそう。ちいかわにハマりすぎて冷蔵庫がちいかわたちのマグネットだらけです。
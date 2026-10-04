---
title: 一日の作業をMarkdownに自動記録するAmbient Contextが良さげかも
source: https://kawarimidoll.com/posts/202609222/
author:
published: 2026-09-22
created: 2026-09-24
description: Macでの作業をMarkdownで自動記録してAIにまとめさせるAmbient Contextの紹介です。
tags:
  - clippings
updated: 2026-09-24T02:03
---
Macでの作業をMarkdownで自動記録してAIにまとめさせるAmbient Contextの紹介です。[GitHub - dragthelake/ambient-context: A menu bar app that keeps a written record of what you worked on.](https://github.com/dragthelake/ambient-context)

[A menu bar app that keeps a written record of what you worked on. - dragthelake/ambient-context](https://github.com/dragthelake/ambient-context)

[

github.com

![A menu bar app that keeps a written record of what you worked on. - dragthelake/ambient-context](https://kawarimidoll.com/rehype-og-card/f3e592a4c553953abc2fb8fb0a76877fa100922140ef0a7d18025c43d36acef4)](https://github.com/dragthelake/ambient-context)

## 機能

Tauri製のmacOSメニューバーアプリで、フォーカス中のウィンドウのテキストを数秒おきに拾ってMarkdownに蓄積します。その時点で何をやっていたかを自分で書き出さずとも、AIツールに作業記録を渡せるというのがコンセプトです。

情報取得はAccessibility API経由で、スクリーンショット・録画・OCRは使いません。取得したテキストは重複除去とノイズ除去にかけられ、秘匿情報らしき文字列は `[redacted]` に置換されます。パスワードマネージャーのウィンドウとプライベートブラウジングはスキップされ、アプリごとに除外指定もできます。

UIがレトロでかわいい〜。

![img](https://storage.kawarimidoll.com/1788940400329.avif)

起動するとこうなります。しかも目が動く。ひぇ…。

![img](https://storage.kawarimidoll.com/1788940508718.avif)

出力は3種。Contextがキャプチャされた元データで、KnowledgeとNotesはCLIエージェントを接続して生成します。

- Context: 時系列順で並ぶアプリ名・ファイルパス・URLの生データ
- Knowledge: ContextをPeople・Commitments・Threads・Products・Issues・Readingの6つにまとめ直したもの
- Notes: その日のまとめ

エージェントはClaude Code、Codex、opencodeに対応。書き留めた記録にアクセスするためのMCPサーバーとスキルが提供されています。

## 所感

まとめられた内容はさすがに公開できませんが見た目はこんな感じ。

![img](https://storage.kawarimidoll.com/1788956650185.avif)

日報を実際の作業記録から作れて便利そう。あとは数日前に読んだページを記録から辿るといった使い方もできそうです。ただ、GPUレンダリングのターミナルからはテキストがほとんど取れないとのことで、筆者のような1日の大半をターミナルで過ごしているタイプにはちょっと合わないかもしれません。

以下は画面上の作業すべてを録画するツールの紹介です。[mac画面をすべて記録するアプリRetraceが良さげかも](https://kawarimidoll.com/posts/202605042/)

[

macの画面を常時記録してOCRで全文検索可能にするアプリRetraceの紹介です。

kawarimidoll.com

![mac画面をすべて記録するアプリRetraceが良さげかも](https://kawarimidoll.com/posts/202605042/og.png)](https://kawarimidoll.com/posts/202605042/)

あとレトロな見た目つながりでこれも。[すべてがファイルになるSheruが良さげかも](https://kawarimidoll.com/posts/202608032/)

[

ローカル・リモート・クラウドを統一UIで辿れるmacOSのファイルブラウザSheruの紹介です。

kawarimidoll.com

![すべてがファイルになるSheruが良さげかも](https://kawarimidoll.com/posts/202608032/og.png)](https://kawarimidoll.com/posts/202608032/)
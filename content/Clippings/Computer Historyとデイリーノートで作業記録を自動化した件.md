---
title: "Computer Historyとデイリーノートで作業記録を自動化した件"
source: "https://x.com/shotovim/status/2098675239605453208"
author:
  - "[[@shotovim]]"
published: 2026-09-12
created: 2026-09-18
description: "1日の終わりにその日の業務や作業内容を振り返る際、具体的にどのタスクへどれだけの時間を割いたかを正確に思い出すことには、一定の認知コストがかかります。忙しく手を動かしていたにもかかわらず、日次レビューや日報を作成する段階になると、直前の作業内容しか思い出せないケースは少なくありま..."
tags:
  - "clippings"
---
1日の終わりにその日の業務や作業内容を振り返る際、具体的にどのタスクへどれだけの時間を割いたかを正確に思い出すことには、一定の認知コストがかかります。

忙しく手を動かしていたにもかかわらず、日次レビューや日報を作成する段階になると、直前の作業内容しか思い出せないケースは少なくありません。

この課題は、CodexのComputer Historyを活用することで解決できます。

[

![Computer History | ChatGPT Learn](https://pbs.twimg.com/card_img/2098469115178766336/vn_8fbur?format=jpg&name=medium)

Computer History | ChatGPT Learn

](https://learn.chatgpt.com/docs/customization/computer-history)

[From learn.chatgpt.com](https://learn.chatgpt.com/docs/customization/computer-history)

Computer Historyを使うことで、PC上の活動履歴から客観的な作業ログを自動で抽出し、デイリーノートに集約する仕組みを構築でき、記憶に頼る必要がなくなります。

さらにCronosu（後述）を用いてこの処理を定期実行すれば、記録作業そのものを監視できるため、毎日の作業ログを安定して蓄積できます。

## 1\. Computer Historyによる作業履歴の取得

Computer Historyを有効にすると、許可したアプリケーションやブラウザでの操作ログがバックグラウンドで記録されます。作業ごとの手動メモを残さずとも、この履歴データを基に活動内容を整理できます。

![Image](https://pbs.twimg.com/media/HR_6zAkbEAAwJGi?format=jpg&name=large)

また、次のような指示を与えることで、各時間帯の主な作業内容を客観的なタイムラインとして可視化できます。

```markdown
本日のComputer Historyを参照し、
実行した作業内容を時間帯ごとに簡潔にまとめてください。
記録から読み取れない事項については推測で補わないでください。
```

## 2\. デイリーノートの活用

整理した作業内容は、Obsidianをはじめとするデイリーノートに記録します。

Obsidianのデイリーノートは毎日コツコツ書くことで長期的にみたときにかなり価値あるものになるんですが、書く内容や振り返りをする際に思い出せないみたいなことが起こりなかなか継続できないです。

しかしこのComputer historyとデイリーノートを組み合わせることで振り返りがかなりしやすくなります。

わたしがおすすめしているのはComputer Historyで列挙された事実と自分で考えた振り返りを分けて書くことです。

以下のような感じです。

![Image](https://pbs.twimg.com/media/HR_6QgIboAAlkrZ?format=png&name=large)

Computer Historyの事実列挙だけでも十分だと思いますが、今日一日どうだったかみたいな主観が入る感想を書くことで振り返りの質があがり、デイリーノートとして完成度が高くなります。

## 3\. 定期実行による記録プロセスの自動化

日次の記録を手動でAIに指示していると、確実に続かないのでスケジューリングして定期実行します。具体的には履歴の抽出からノートへの追記までを完全に自動化します。

以下は、実行指示（プロンプト）の構成例です。このあたりはお好みでいいと思います。わたしはざっくり作業・運用と娯楽を分けたり、10分ごとの記録単位で出力してもらうような設定をしています。

```markdown
本日のComputer Historyを参照し、当日のデイリーノートに作業内容を追記してください。

- 時間帯ごとに作業実績を簡潔にまとめること
- 業務とそれ以外の利用（調査・閲覧等）を分類すること
- ログから読み取れない情報や評価は推測して記述しないこと
- 「Computer History」セクションのみを更新し、既存のテキストは維持すること
- 履歴が取得できない場合は、ノートを編集せず処理を終了すること
- 保存先パス：[デイリーノートの保存先ディレクトリ]
```

## 4\. Cronosuを用いた実行管理

この定期実行タスクのスケジューリングには、Codex内のスケジュール機能を使用していて、Mac用タスクランナー「Cronosu」で監視しています。

[

![Cronosu。AIとスクリプトを定期実行](https://pbs.twimg.com/card_img/2100525748968886272/RtjPdk0j?format=jpg&name=medium)

Cronosu | Macの自動化を、ひと目で。

](https://www.cronosu.com/ja)

[From cronosu.com](https://www.cronosu.com/ja)

![Image](https://pbs.twimg.com/media/HR_7HGkaoAACfpE?format=jpg&name=large)

Cronosuを使うことで成功したか失敗したかが直感的にわかるのでとても便利です。

> 実際にわたしは振り返りを1日の終わりにスケジュール設定していましたが、Macがロック状態になりcomputer history による追記ができてなかったことにCronosuで監視していたことで気づけました。

## まとめ

本仕組みの役割分担は以下の通りです。

1\. 客観的事実の収集・整理：Computer History（Codex）

2\. 定常プロセスの自動化：Cronosu

3\. 成果の評価とネクストアクションの決定：デイリーノート

作業ログを「思い出す」作業をシステムに委ねることで、日次レビューを形式的な作業で終わらせず、次の意思決定につなげるための有意義な時間に充てることができます。

**最後に宣伝です。**

AIに、毎回同じ前提を説明していませんか？

以前決めた方針をまた説明し直す。メモが見つからず探す時間ばかり増える。議事録が次の仕事に活きない――。

そんな状態を解消するため、日々のメモや業務ログをAIにスムーズに受け渡す「情報整理の仕組み化」をお手伝いしています（個人・法人問わず）。

新しいツールへ無理に乗り換える必要はありません。今使っているメモや資料を活かしながら、どこを連携させれば二度手間がなくなるかを一緒に整理します。

Obsidianの初期からナレッジ管理とAI連携を実践・発信してきた経験をもとに、設計から自走運用の定着まで伴走します。

方針が固まっていなくても構いません。まずは現在の業務状況や困りごとについて、[LINEでお気軽にご相談ください（初回無料）](https://genkan.saneru104.workers.dev/r/note)。

[

![](https://pbs.twimg.com/card_img/2098672511441678336/WH-t-O-W?format=png&name=medium)

Add LINE friend

](https://genkan.saneru104.workers.dev/r/note)

[From line.me](https://genkan.saneru104.workers.dev/r/note)

## 今回の記事の参考

[

![](https://pbs.twimg.com/card_img/2098672609328353281/qdvm7FxE?format=jpg&name=medium)

AIで日々のやっていることを可視化したらめちゃ良かった

](https://open.spotify.com/episode/19pueEbV4vbGcPPWdLeeLB?si=f2c63349be684268)

[From open.spotify.com](https://open.spotify.com/episode/19pueEbV4vbGcPPWdLeeLB?si=f2c63349be684268)
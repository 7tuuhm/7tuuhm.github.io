---
title: "Post by @ctgptlb on X"
source: "https://x.com/ctgptlb/status/2100120850754412967"
author:
  - "[[@ctgptlb]]"
published: 2026-09-16
created: 2026-09-17
description: "【速報】ChatGPT共同発明者、2年のステルスを経て新型AIモデル「Jev」を発表 ・LLMより20〜200倍高速、40〜400倍安価 ・出力マラソンは永久無料（安すぎて課金計測できない） ・文字列ではなく「型付きの判断」を返す ・構造上ハルシネーションが起きない技術と実例をス"
tags:
  - "clippings"
---
【速報】ChatGPT共同発明者、2年のステルスを経て新型AIモデル「Jev」を発表 ・LLMより20〜200倍高速、40〜400倍安価 ・出力マラソンは永久無料（安すぎて課金計測できない） ・文字列ではなく「型付きの判断」を返す ・構造上ハルシネーションが起きない技術と実例をスレッドにまとめます👇🧵

![画像](https://pbs.twimg.com/media/HSUgsHBasAAgNcz?format=png&name=large)

---

1/ Jevは「System One Model」という新しいモデルクラス

LLM＝人間向けに文章を生成するAI

Jev＝ソフトウェア向けに判断を返すAI

入力：非構造データ＋プログラムの状態

出力：型安全な値＋較正済みの確率

開発者いわく「フロンティア知能を関数呼び出しにしたもの」

---

2/ なぜ20〜200倍速いのか

LLMは1トークンずつ逐次生成する（前の出力に依存）

Jevは全出力を1クエリで並列に生成する

逐次計算を並列に置き換える――TransformerがRNNを飛び越えたのと同じ構図

応答時間は70〜500ms

フロンティアLLMは3〜329秒

---

3/「ハルシネーションしない」の中身

出力の候補と構造を事前に定義するため、Jevは型エラーを起こせない（数学的に不可能）

さらに全出力に較正済みの確率が付く

・確信度が高いほど正答率も高い

・低ければコード側でエスカレーション

「95%できるが残り5%がどこか言えない」問題を潰す設計

![Image](https://pbs.twimg.com/media/HSUh4hQacAAt2K4?format=png&name=large)

---

4/ 数字

入力：$0.042 / MTok（10億トークンで$42）

出力：無料

→ Claude Fable 5.1の入力単価の1/238

公式のワークフロー評価では、GPT-6 AstraとFable 5.1の平均を基準として

193.6倍高速・444.6倍安価

Intelligence per dollarでパレートフロンティアを独占

---

5/ トレードオフ

Jevは文章を生成できない

できるのは分類・ルーティング・スコアリング・抽出・分岐

つまりコードの中の「賢いif文」

選択肢の上限は255（超える場合は2段階処理）

汎用性を捨てて自動化に全振りした設計です

---

事例① 約5,000リクエストで$2

@MichaelLee04 が分類・モデルルーティング・意図判定で実測

p50 150ms / p95 350ms

従来はLLM分類が平均4秒＋高コストなため「分類器を呼ぶか」を粗いヒューリスティクスで判定する連鎖を書いていた。それが丸ごと不要に

> **Michael @MichaelLee04** · 2026-09-15
> 
> I got access to Jev earlier today (thank you @hackgoofer). I have run ~5,000 requests so far, (which cost me around $2!), across classification, model routing, intent, steering, and many other things.
> 
> tl;dr, Jev enables a new intelligent decision-making primitive, separate from x.com/CompleteSkepti…
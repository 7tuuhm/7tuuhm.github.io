---
title: "はじめに — 本書の読み方｜Claude Code本番運用ガイド — CI/CDに組み込む自律エージェント設計"
source: "https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/intro"
author:
published:
created: 2026-09-18
description:
tags:
  - "clippings"
---
Chapter 01

## はじめに — 本書の読み方

[

はんぺん

2026.09.14に更新

](https://zenn.dev/hampen2929)

Claude Codeと対話しながら開発する。それはもう、多くのエンジニアにとって日常になりました。指示を出し、出力を見て、軌道修正し、コミットする。隣に優秀な相棒がいる感覚です。

では、質問です。 **あなたが寝ている間も、Claude Codeは働いていますか。**

CIが検出したlintや型エラーが、翌朝には修正PRになって並んでいる。夜間に失敗したテストが、原因の一次調査レポートつきで報告されている。PRを出せば、チームのレビュー観点での一次レビューが数分で返ってくる——本書は、Claude Codeをそういう「毎晩黙って働くチームメンバー」にするための設計書です。

対話で使えているツールを無人にするのは、コマンドにフラグを1つ足すだけ——ではありません。あなたが対話中に無意識にやっていた仕事、つまり危険な操作を止める、出力のおかしさに気づく、コストに気を配る、結果に責任を持つといった仕事を、すべて仕組みに移植する必要があります。それが本書のテーマである「本番運用」です。

## 読み始めるために必要なこと

本書は入門書ではありませんが、無人運用の経験は前提にしません。次の3つが手元でできていれば読み始められます。

- Claude Codeを対話で使い、出てきた差分を `git diff` で確認してコミットしたことがある
- `npm test` や `npx tsc --noEmit` のようなチェックを自分で実行し、失敗の出力を読んだことがある
- GitHubでPull Requestを出し、レビューを受けてマージした経験がある

反対に、CIの中で `claude -p` を無人で動かした経験、ワークフローの権限設計、コストの集計は、本書で初めて扱う前提で書いています。CIで自動化したことがなくても、GitHub Actionsの `on:` と `steps:` の意味が分かれば、掲載例は追えます。

- [Claude Code入門 — インストールから最初のひと仕事まで](https://zenn.dev/hampen2929/articles/20260814-claude-code-getting-started)
- [Claude Code入門を終えた人が、次にやるべき5つのこと](https://zenn.dev/hampen2929/articles/20260807-claude-code-next-5-steps)
- [claude -p 15分ハンズオン — 初めてのヘッドレス実行](https://zenn.dev/hampen2929/articles/20260807-claude-p-headless-hands-on)

3本目のハンズオンを終えている——つまり `claude -p` をローカルで一度でも動かしたことがある——と、「 [ヘッドレス実行の基礎](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/headless-basics) 」の章以降が格段にスムーズです。まだなら、その章の冒頭で15分だけ寄り道してください。

## 想定読者

- 個人ではClaude Codeを使いこなしているが、 **チーム展開やCI組み込みを任された** 人
- `claude -p` をローカルで動かしたことはあるが、 **本番のCIに入れる確信が持てない** 人
- AIエージェントの無人運用について、 **暴走・品質・コスト・監査への答えを用意する立場** にある人（テックリード、プラットフォームエンジニア、導入責任者）

## 読み終えたときにできるようになること

本書は1つの題材——架空のタスク管理アプリ（TypeScript/Node）——を通して、無人ジョブの一連の判断を追います。夜間の実行結果を見て成功・失敗・判定不能を区別する。検出した型エラーを修正させる。検証に落ちたらPRを出さず、失敗を報告して止める。出てきたPRを人間がレビューする。そして計測した数字で、任せる範囲を広げるか止めるかを決める。読み終えたとき、この流れを自分のリポジトリで組み立て、次のことができるようになります。

- ヘッドレス実行をCI/CDに組み込み、認証・権限・上限を設計できる
- 「検出→修正→検証→コミット」の自律修正ループを設計し、直せない夜は失敗として止められる
- 事故（Safety）と攻撃（Security）を区別して守りを設計し、その内容を他人に説明できる
- 品質とコストをメトリクスで管理し、「1修正あたりいくらか」でROIを語れる
- 自社・自チームへ導入する道筋（PoC→パイロット→展開、監査対応）を描ける

## 本書の構成

| 部 | 章 |
| --- | --- |
| **第1部 土台 — 無人で動かす** | なぜ「本番運用」は別物なのか / ヘッドレス実行の基礎 / 自律修正ループの設計 / サブエージェント編成 |
| **第2部 守り — 事故と攻撃を防ぐ** | ガードレール（事故を防ぐ） / セキュリティ（攻撃を防ぐ） |
| **第3部 運用 — 回し続け、広げる** | 品質評価 / コスト管理 / 企業環境への導入 |
| **実践** | リファレンス実装 — 全部つなげて動かす |

![本番運用の4つの壁（暴走・品質・コスト・監査）と、本書のどの章が答えるかの対応](https://static.zenn.studio/user-upload/deployed-images/38f66a57e66886e967142b1e.jpg?sha=b0fd35b6bb6948cf8c2d7117008875d88aea81e5)

基本は順に読む構成です。各章は同じ題材のジョブに戻り、実行の仕組みと運用の判断を積み上げていくので、通読すると1本の読み取り専用ジョブが自律修正ループに育ち、守りと計器がつき、組織に載るまでを追えます。目的がはっきりしている人は、次の入口から入ってください。

- **まず最小の無人ジョブを動かしたい**: 「 [ヘッドレス実行の基礎](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/headless-basics) 」→「 [自律修正ループの設計](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/autonomous-fix-loop) 」→「 [ガードレール](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/guardrails) 」の3章で最小構成が組めます
- **稟議やセキュリティレビューの回答を用意したい**: 「 [なぜ「本番運用」は別物なのか](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/why-production-is-different) 」で4つの壁を押さえ、「 [セキュリティ](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/security) 」と「 [企業環境への導入](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/enterprise) 」へ
- **動いているジョブの費用や品質を判断したい**: 「 [品質評価](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/quality-evals) 」と「 [コスト管理](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/cost) 」
- **設定やフラグの意味を確かめたい**: 「ヘッドレス実行の基礎」の権限モードと、「ガードレール」のpermissions・Hooksの節。最終章「 [リファレンス実装](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/reference-implementation) 」のチェックリストが各章への索引になります

どの入口から入っても、守り（第2部）を飛ばした構成のまま本番に置くことだけは避けてください。理由は第2部で嫌というほど説明します。

本書は方法論を、実際の失敗例とコード例に結びつけて説明します。きれいな設計論だけでは本番運用は語れないので、筆者が実際に運用している構成と、実際に踏んだ失敗——作業ディレクトリ外のファイルを上書きされた事故、自動レビューの形骸化など——を隠さず材料にします。そして最終章で、全章の部品を組み合わせた1つのリファレンス実装を、動くコードとして通しで解説します。

## 検証環境と表記

- 本書のコード例は、架空のタスク管理アプリ（TypeScript/Node）のサンプルリポジトリを一貫した題材として進みます。型チェック・テスト・lintが `package.json` のスクリプトで走る、平凡なAPIです。この題材はシリーズ共通で、続刊（AIチーム開発・評価と品質保証・セキュリティ）でも同じリポジトリを育てていく予定です
- 2026-09-08の推敲では、Claude Code CLI 2.1.263のヘルプと公式ドキュメントを照合し、掲載コードの構文・主要な判定処理をローカルで検査しました。掲載ワークフローをGitHub Actionsで再実行した検証ではありません。例のCLIバージョンは固定し、更新時は代表ジョブで回帰検証してください
- CIの例はGitHub Actionsを使います。GitLab CI/CDなど他のCIでも、設計はそのまま移植できます
- 本書の設計原則の大半は、Claude Code固有ではありません。codexなど他のコーディングエージェントを運用する場合にも、権限設計・検証ゲート・コスト管理の考え方はそのまま使えます

仕様の参照先は [ヘッドレス実行](https://code.claude.com/docs/en/headless) 、 [CLIリファレンス](https://code.claude.com/docs/en/cli-reference) 、 [権限設定](https://code.claude.com/docs/en/permissions) 、 [Hooks](https://code.claude.com/docs/en/hooks) です。記載どおりに動かないときは、手元の `claude --version` / `claude --help` と照合してください。

## 更新履歴

| 日付 | 内容 |
| --- | --- |
| 2026-08 | 初版刊行 |
| 2026-08 | セキュリティ章: Issueトリアージ例のワークフローにシェルインジェクションの脆弱性（Issue本文の `${{ }}` を `run` に直接埋め込み）があったため、環境変数経由の受け渡しに修正 |
| 2026-09-08 | 認証・ツール権限・修正採用条件を整合。差分検査・Hooks・結果JSON検査・PR受理率集計を補修し、GitHubの起動条件とキャッシュの説明を更新 |
| 2026-09-14 | 全章の読みやすさを推敲。キャッシュ寿命・思考予算・監視イベント・公開サンプルとの差分の記述を訂正（改稿案・レビュー待ち、公開版未反映） |
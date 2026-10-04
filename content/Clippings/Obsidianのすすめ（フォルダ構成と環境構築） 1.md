---
title: Obsidianのすすめ（フォルダ構成と環境構築）
source: https://zenn.dev/atman/articles/255327dafb6405
author:
published: 2025-08-31
created: 2026-10-03
description:
tags:
  - clippings
updated: 2026-10-03T14:36
---
21

14[Obsidian](https://zenn.dev/topics/obsidian)

[

idea

](https://zenn.dev/tech-or-idea)

現代のデジタルワーク環境では、情報管理の効率化が重要な課題になっていると感じます。

多くの方が次のような悩みを抱えているのではないでしょうか：  
「増え続けるメモが散在し、必要な情報にすぐアクセスできない」  
「プロジェクトごとに異なるツールを使い分けることで、管理が複雑化する」

これらの課題をシンプルかつ効果的に解決するのが **Obsidian** と思っています。

Obsidianは従来のメモアプリの枠を超え、「知識のネットワーク」を構築するための強力なツールです。双方向リンク機能によりノート間を有機的につなげることで、個人の知識を体系化し、活用可能なデータベースとして構築できます。  
使い方次第で、ビジネスドキュメントの管理から学術研究まで、様々なシーンでその真価を発揮できると思います。

<iframe src="https://embed.zenn.studio/card#zenn-embedded__fef2903e3b14c" frameborder="0" height="122"></iframe>

今まで様々なノートアプリを利用してきましたが、この **Obsidian** が一番しっくりきています😌

e.g. 私のObsidian環境  
![私のObsidian環境のスクリーンショット](https://static.zenn.studio/user-upload/e95f945d607d-20250831.png)

## Obsidianの便利なところ

Obsidianの魅力は、その多機能性にあります。ここでは特に便利な3つの機能を紹介します。

- **柔軟なテンプレート機能**: 議事録や読書メモなど、用途に応じたテンプレートを作成できます。これにより、定型文書の作成効率が劇的に向上します。
- **強力なリンク機能**: ノート間をリンクで結びつけることで、関連情報が自然と集まります。まるで脳の神経細胞のように知識のネットワークが広がり、新たな発見を促します。
- **拡張性の高いプラグイン**: 1,000を超えるプラグインから、必要な機能を追加できます。例えば、「Excalidraw」プラグインを使えば、ノート内に手書きの図やスケッチを直接埋め込めます。

## Obsidianの導入方法

Obsidianを始めるのはとても簡単です。

1. **公式サイトからダウンロード**
	- [Obsidian公式サイト](https://obsidian.md/) にアクセスし、お使いのOS（Windows, macOS, Linux）に合ったインストーラーをダウンロードします。
2. **インストール**
	- ダウンロードしたファイルを実行し、画面の指示に従ってインストールします。
3. **保管庫（Vault）の作成**
	- 初回起動時に「保管庫（Vault）」の設定を求められます。これは、すべてのノートを保存する専用のフォルダです。
		- 「保管庫を新規作成する」を選び、好きな名前（例: `my-notes` ）と場所を指定すれば、準備は完了です。

## 設定内容の紹介

Obsidianを最大限に活用するためには、初期設定が重要です。ここでは、私が実践している「Zettelkasten（ツェッテルカステン）」という知識管理術を参考にした設定を紹介します。これはあくまで一例ですので、ヒントになれば幸いです。

### フォルダ構成

Zettelkastenは、情報を小さなカード（ノート）に分割し、それらをリンクで繋いでいく手法です。この考え方に基づき、情報の性質ごとにフォルダを分けることで、思考が整理されやすくなります。

この構成で、「情報収集 → アイデア創出 → 知識の体系化（情報整理）」という知的生産のフローをスムーズにしていきます。

> 上記フォルダ構成は、以下の記事を参考にさせて頂きました。
> 
> - [Obsidianで最適なPKM環境を考える](https://zenn.dev/game8_blog/articles/0e50c36cd63b98)
> - [ノート管理術：Zettelkastenの仕組みと実際にObsidianでやってみた感想](https://zenn.dev/fukagawa/articles/9e0436fe7e6f25)

index（索引ノート）とstructure（構造ノート）の違い

#### index-note（索引ノート）

- **目的**: 「入り口」や「目次」として機能するノート。
- **イメージ**: 図書館の「索引カード」や「本の目次」。
- **特徴**:
	- 単純にリンクを並べて、探したいノートにすぐアクセスできるようにする。
		- 主に「タグやテーマごとの一覧」を作る。
		- 内容の関係性まではあまり記述せず、ナビゲーション重視。

e.g.

```
# Index: 学習法
- [[Zettelkastenとは]]
- [[アクティブリコールのメリット]]
- [[分散学習（Spaced Repetition）]]
- [[メタ認知と学習効率]]
```

👉 このノート自体は「リンクのリスト」。読むというより「どこに何があるか探す」ため。

#### structure-note（構造化ノート）

- **目的**: あるテーマについて、 **ノート同士の関係性を整理し、ストーリーや構造を組み立てる** 。
- **イメージ**: 本の「章構成」や「論文のアウトライン」。
- **特徴**:
	- 単なるリンク集ではなく、 **リンクに説明や文脈を付ける** 。
		- ノートを並べる順番や階層を工夫して、理解を深めるための「知識の地図」にする。
		- 後に記事や本を書くときの「骨組み」になる。

e.g.

```
# Structure: 学習理論
学習効率を高めるための重要な理論をまとめる。

1. 学習の基本原則
   - [[Zettelkastenとは]] … 情報を小さく分割し、リンクでつなぐ思考法
   - [[アクティブリコールのメリット]] … 思い出すこと自体が学習を強化する

2. 学習を支える心理学的要素
   - [[分散学習（Spaced Repetition）]] … 記憶の定着には間隔をあけることが有効
   - [[メタ認知と学習効率]] … 自分の理解度を客観的に把握する力
```

👉 ただのリストではなく、「どう繋がるか」「どんな順番で理解すると良いか」が書かれている。

### Option設定とショートカット

#### 基本設定

今回のフォルダ構成に合わせて、Optionsに以下を設定しておきます。

- Files and links
	- Folder to create new notes in: `fleeting-note`... 新規ノートを作成するフォルダ
		- Attachment folder path: `_config/extra`... 添付ファイルフォルダのパス
- Core Plugins
	- Daily notes: ON
		- Templates: ON

#### テンプレート設定

毎回同じ情報を手入力するのは非効率です。 `Templater` プラグインを使って、ノート作成を自動化します。

**1\. Frontmatterテンプレートの作成**  
`Frontmatter` とは、ノートの作成日やタグなどのメタデータを記述する部分です。これを共通化します。

まずは、以下のFrontmatterテンプレートファイルを作成します。

`_config/templates/_frontmatter.md`

```
---
id: {{date:YYYYMMDDTHHmmssSS}}
aliases: []
tags: []
created: {{date:YYYY-MM-DDTHH:mm:ss}}
updated: {{date:YYYY-MM-DDTHH:mm:ss}}
---
#
```

次に、プラグイン [Templater](https://github.com/SilentVoid13/Templater) をインストールします。  
Core Plugins > Templaterの設定は下記を参考にしてください。

- Template folder location: `_config/templates`
- Folder templates:  
	![TemplaterプラグインのFolder templates設定画面](https://static.zenn.studio/user-upload/dbe8eadb7937-20250831.png)

**2\. デイリーノート用テンプレートの作成**  
日々の記録を効率化するためのテンプレートです。

まずは、以下のデイリー用テンプレートファイルを作成します。

`_config/templates/daily.md`

```
---
id: {{date:YYYYMMDDTHHmmssSS}}
aliases: [{{date:YYYY/MM/DD}}, {{date:YYYY年M月D日}}]
tags: [daily/{{date:YYYY/MM/DD}}]
created: {{date:YYYY-MM-DDTHH:mm:ss}}
updated: {{date:YYYY-MM-DDTHH:mm:ss}}
---
# Memo

## 今日やること

## 気になること

## 一時的なメモ
```

次に、Core Plugins > Daily notes の設定を行います。

- New file location: `daily`
- Template file location: `_config/templates/daily`

これらのテンプレートを設定することで、ワンクリックで整形されたノートを作成できます。

#### ショートカットキーのカスタマイズ

VSCodeなどのエディタに慣れている方は、キーボードショートカットを統一すると作業効率が上がります。

- Move line down: `Alt + ↓`
- Move line up: `Alt + ↑`
- Navigate back: `Alt + ←`
- Navigate forward: `Alt + →`

### 外観のカスタマイズ

長時間を共にするツールだからこそ、見た目は重要です。

#### テーマの変更

設定画面の `外観 > テーマ` から、コミュニティが作成した多くのテーマを試せます。

1. 設定のAppearence > Themes の「Manage」ボタンをクリックし、適用したいテーマを取得する  
	![テーマ管理画面のスクリーンショット](https://static.zenn.studio/user-upload/7a186d165eb7-20250831.png)
2. 取得したテーマは、Themesで設定すると反映される  
	![テーマ選択画面のスクリーンショット](https://static.zenn.studio/user-upload/86ef78668892-20250831.png)

#### フォントの変更

私は、温かみのある手書き風フォント「ふい字P」を愛用しています。 `Custom Font Loader` プラグインを使えば、好きなフォントを簡単に導入できます。

1. 利用したいフォントをダウンロードする  
	e.g. ふい字P... [ふい字Pの詳細情報: Vector ソフトを探す！](https://www.vector.co.jp/soft/data/writing/se347444.html?ds)
2. Obsidianに、 [Custom Font Loader](https://github.com/pourmand1376/obsidian-custom-font) プラグインをインストールする
3. `.obsidian/fonts/` フォルダに、ダウンロードしたフォントのファイル（`.tts` ）を保存する  
	![Obsidianのfontsフォルダにフォントファイルを保存している様子](https://static.zenn.studio/user-upload/255c78aba2f7-20250831.png)

## おすすめプラグイン

Obsidianの真価はプラグインによる拡張性にあります。ここでは、おすすめの5つのプラグインを紹介します。

### Update time on edit

ノートを編集した際に、更新日時を自動で記録してくれます。情報の鮮度を保つために重要なプラグインです。

- プラグイン： [beaussan/update-time-on-edit-obsidian](https://github.com/beaussan/update-time-on-edit-obsidian)
- 私が利用している設定例：
	- Folder to exclude of all update: `_config`... コンフィグのファイルは更新不要なため除外
		- Minimum number of minutes between update: 5... 5分間隔で更新
		- Folder(s) to exclude for updating the created property: `_config`... コンフィグのファイルは更新不要なため除外

### Excalidraw

Obsidian内で、高機能なホワイトボードアプリのように手書きの図やスケッチが描けます。複雑なアイデアを視覚的に整理するのに最適です。  
![image](https://static.zenn.studio/user-upload/99707490011e-20250831.png)

- プラグイン： [Excalidraw](https://github.com/zsviczian/obsidian-excalidraw-plugin)
- 私が利用している設定例：
	- Zoom and Pan > Right-click drag to pan: ON... 右クリックでドラッグ移動
		- Zoom and Pan > Mouse wheel to zoom by default: ON

### Iconize

ファイルやフォルダにアイコンを設定できます。視覚的に区別しやすくなるため、ノートが増えてきても直感的に目的のファイルを見つけられます。

### Remotely Save

ノートデータをOneDriveなどのクラウドストレージに自動で同期します。これにより、スマートフォンや他のPCからも同じ環境にアクセスできます。

### Git

GitHubと連携し、ノートの変更履歴を自動で保存します。誤ってデータを消してしまっても、いつでも過去の状態に復元できるため安心です。

### Web Clipper

Web Clipperは、正確にはプラグインではなく、ブラウザ拡張機能です。PCに表示しているページをボタン一つでObsidianに保存できて便利です。

<iframe src="https://embed.zenn.studio/card#zenn-embedded__f20c785d6db3a" frameborder="0" height="122"></iframe>

クリップした記事の保管場所設定はこちらが参考になります。

<iframe src="https://embed.zenn.studio/card#zenn-embedded__542a3ab1a9a92" frameborder="0" height="122"></iframe>

## まとめ

Obsidianを導入することで、以下のメリットを得られると思います。

1. **知識の体系化**: 点在していた情報が有機的につながり、新たな洞察やアイデアが生まれる。
2. **業務効率の向上**: 必要な情報に瞬時にアクセスでき、意思決定やアウトプットの質とスピードが向上する。
3. **データの永続性**: オープンなMarkdown形式なので、特定のサービスに縛られることなく、長期的に安心して知識を蓄積できる。
4. **無限の拡張性**: 豊富なプラグインにより、あなたの成長に合わせてツールも進化し続ける。

まずは、日々のメモを取ることから始めてみませんか？  
その小さな一歩が、あなたの知的生産性を劇的に変える、大きな飛躍へと繋がるはずです。たぶん...

## おまけ

参考までに、私がObsidianを利用する前と後で変わったイメージを共有します。

### Before - After

![before-after](https://static.zenn.studio/user-upload/28141a15074f-20250906.png)

従来は、もちろんメモを取る習慣はありましたが、各ツールやサービスごとに情報が散在しており、必要な情報にアクセスするのに苦労することが多くありました。前回どのサービスやWebページにアクセスしたかを思い出せず、迷子になることも少なくありませんでした。

現在は、Obsidianで作成したノート（メモ）を経由することで、自分の関心事に関連する情報をスムーズに引き出せるようになっています。ノート自体が「情報の入口」となり、複数のツールやサービスを行き来する必要が大幅に減りました。

もちろん、ノートを作成・整理する作業が負担になることもあります。しかし、Obsidianにはノート作成や整理を効率化するための仕組みが標準機能として備わっているため、使えば使うほど負担は軽減されていくと感じます。

### Obsidianが便利だと感じた標準機能の一部

1. **ノート作成の効率化**
	- テンプレートからノート作成
		- ノートの一部を別ノートに切り出し
		- ノートを別ノートに結合
2. **情報の検索性向上**
	- ノート間をリンクで接続
		- 参照ノートの内容をプレビュー表示

こうした機能のおかげで、単なるメモアプリにとどまらず、自分の思考や知識を「引き出しやすい形」で育てていけるのがObsidianの大きな魅力だと思います。

14

保存
---
title: "ヘッドレス実行の基礎 — claude -p を自動化の部品にする"
source: "https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/headless-basics"
author:
published:
created: 2026-09-18
description:
tags:
  - "clippings"
---
Chapter 03

[

はんぺん

2026.09.14に更新

](https://zenn.dev/hampen2929)

この章から手を動かします。扱うのは、無人運用の最小部品である `claude -p` （ヘッドレス実行）を、 **CIに載せられる品質の部品** に鍛え上げることです。

最初の到達点は小さく置きます。 **1回の実行結果を見て、成功・失敗・判定不能の3つを機械的に区別できること。** 対話では画面を見れば分かるこの区別が、無人では終了コードとJSONの中身から組み立てるものになります。ここができると、後の章のジョブはすべて「この判定の上に何を足すか」として読めます。

その判定を土台に、「ローカルで動いた」と「CIで毎晩動く」の間にある溝を順に埋めていきます。再現性（ `--bare` ）、出力の契約（JSONとスキーマ）、認証、権限、そして時間・リトライ・冪等性です。章の最後で、これらを1本の読み取り専用ジョブに組み上げます。

## claude -p の正体 — 対話と同じエージェントが、1回で終わる形で動く

`-p` （ `--print` ）は「対話UIを省略するフラグ」ではありません。公式の位置づけでは、 `claude -p` は **Agent SDKをCLIとして使う** 入口です。対話モードと同じツール群・エージェントループ・コンテキスト管理が、プロンプトを受け取って結果を出力して終了する形で動きます。

自動化の部品として見ると、押さえるべきは入出力の契約です。

- **終了コード**: 成功で0、実行が失敗すると非0。スクリプトはまずここで分岐できます
- **不正なフラグ**: 実行開始前にstderrへエラーが出ます
- **実行中の失敗**: 認証切れなどは、 **stdoutの結果として** 出力されます

3つめが曲者です。実際に、筆者の環境で認証が切れた状態で実行すると、こういうJSONが返ってきました。

```
{
  "type": "result",
  "subtype": "success",
  "is_error": true,
  "num_turns": 1,
  "result": "Failed to authenticate. API Error: 401 OAuth access token has expired. Re-authenticate to continue.",
  "total_cost_usd": 0
}
```

失敗がエラー出力ではなく「結果」として整然と返ってくる。これは無人運用にとってはむしろ好都合で、 **失敗も成功と同じ経路でパースできる** ということです。ただし教訓がひとつ。終了コードだけを見るスクリプトも、 `result` だけを読むスクリプトも、いつか失敗を成功として扱います。 **終了コードと `is_error` の両方をチェックする** のがヘッドレス実行の基本の型です。

この型をシェルで書くと次のようになります。1行目の `||` が終了コードの判定、続く `jq -e` が結果JSONの判定です。JSONが壊れていて `jq` が読めない場合も同じ分岐に落ちるので、「読めなかった」が成功に化けることはありません。

```
output=$(claude --bare -p "..." --output-format json) || { echo "実行失敗" >&2; exit 1; }
if ! printf '%s\n' "$output" | jq -e '
  .type == "result" and .subtype == "success" and .is_error == false
' > /dev/null; then
  echo "正常な結果JSONを確認できないため停止" >&2
  exit 1
fi
```

## CIの再現性 — --bare を既定にする

CIで動かすときの最初の判断は、 `--bare` を付けることです。

`claude -p` は素の状態だと、対話セッションと同じコンテキストを読み込みます。作業ディレクトリと `~/.claude` にあるCLAUDE.md、Hooks、スキル、プラグイン、MCPサーバー、自動メモリ——すべてです。ローカルでは便利ですが、CIでは2つの問題になります。

**第一に、再現性が壊れます。** runnerに残った設定、チームメンバーごとの `~/.claude` 、プロジェクトに後から足されたプラグイン。「どの環境で走っても同じ条件」がCIの前提なのに、暗黙の読み込みはそれを静かに崩します。

**第二に、信頼していない内容が実行前に動きます。** 対話モードなら初見のフォルダでワークスペース信頼の確認が挟まりますが、 `-p` は確認ダイアログを出せません。つまり `--bare` なしのヘッドレス実行は、 **チェックアウトしたリポジトリに含まれる `.claude/settings.json` のHooksや `.mcp.json` のMCPサーバーを、確認なしで起動し得る** ということです。CIが処理するのは他人のPRかもしれないのに、です。この論点は「 [セキュリティ](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/security) 」の章で信頼境界として掘り下げますが、対策自体は今日から入れられます。 `--bare` です。

<iframe src="https://embed.zenn.studio/mermaid#zenn-embedded__875230181b2b4" frameborder="0"></iframe>

`--bare` は暗黙のカスタマイズ読み込みを省くモードです。図では、素の `claude -p` が暗黙の読み込みへつながり、 `--bare` は「省略」へつながっています。 `--bare` 側に入ってくるのは、信頼済みの設定をフラグで渡した分だけです。例外が1つあり、 `--add-dir` で追加したディレクトリ内のスキルは読み込まれるため、追加先も信頼できる場所に限定します。この「暗黙をやめて宣言的にする」転換こそ、対話環境からCI部品への本質的な変換です。渡すものとフラグの対応は次のとおりです。

| 渡したいもの | フラグ |
| --- | --- |
| システムプロンプトの追記 | `--append-system-prompt` / `--append-system-prompt-file` |
| 設定（permissionsなど） | `--settings <file-or-json>` |
| MCPサーバー | `--mcp-config <file-or-json>` |
| カスタムエージェント | `--agents <json>` |
| 追加の作業ディレクトリ | `--add-dir <path>` |

認証にも影響があります。 `--bare` はOAuth認証情報やシステムのキーチェーンを一切読みません。読むのは環境変数のAPIキーか、設定で指定した `apiKeyHelper` の返すキーです。 **CIでは環境変数 `ANTHROPIC_API_KEY` を渡すのが基本形** です（認証の選択肢は後述）。

## 出力の契約 — JSONとスキーマで受け取る

## \--output-format json の主要フィールド

スクリプトから使うなら出力はJSON一択です。主要フィールドを、役割ごとに整理します。

| フィールド | 役割 |
| --- | --- |
| `result` | 最終回答のテキスト |
| `is_error` | ジョブ内失敗のフラグ。 **必ずチェックする** |
| `structured_output` | `--json-schema` 指定時の構造化出力（後述） |
| `num_turns` | 内部で回ったターン数。暴走の検知材料になる |
| `total_cost_usd` | このジョブのAPIコスト。ジョブ単位のコスト計測の起点 |
| `usage` / `modelUsage` | トークン使用量とモデル別の内訳 |
| `session_id` | セッションID。ログとの突合・再開に使う |
| `permission_denials` | 権限で拒否された操作の記録。権限設計の検証材料になる |

2つ、運用上の注意があります。 `total_cost_usd` は **クライアント側の推計値** で、請求額と完全には一致しません。傾向の把握とジョブ単位の上限には十分ですが、経理の数字には請求ベースの集計を使います（「 [コスト管理](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/cost) 」の章）。もうひとつ、 `permission_denials` は地味に重要です。「権限を絞りすぎてジョブが仕事をできていないのではないか」という疑問に、推測ではなく記録で答えられます。

## 機械処理は --json-schema で固定する

後段のスクリプトが読むのは、自由文の `result` ではなく `structured_output` にします。 `--json-schema` でJSON Schemaを渡すと、出力がスキーマに適合した構造で返ります。

```
claude --bare -p "package.jsonのdependenciesを重要度順に3つ挙げて" \
  --output-format json \
  --json-schema '{"type":"object","properties":{"deps":{"type":"array","items":{"type":"string"}}},"required":["deps"]}' \
  | jq '.structured_output.deps'
```

これは取り出し方だけを示した例です。実際の後段処理では、先ほどの正常結果チェックに加えて、 `structured_output` の存在と必要なフィールドの型を検査します。スキーマに沿った出力を作れなかった応答や空の出力を、成功扱いしないためです。

ここで、失敗の種類を2つに分けておきます。渡したスキーマ自体が不正なJSON Schemaなら、モデルを呼ぶ前にCLIがエラーで止まります。静かに劣化せず入口で死んでくれるのは、CI部品として良い性質です。一方、スキーマは正しいのに出力がそれに沿っていない、あるいは `structured_output` が無い場合は、実行後の検査で拾うしかありません。前者は起動失敗、後者は結果の不適合で、止まる場所も見る場所も違います。

もう1つ、スキーマ適合が保証するのは構造だけです。上の例はプロンプトで「3つ挙げて」と頼んでいますが、スキーマは `deps` が文字列の配列であることしか求めていません。2件でも10件でも適合します。件数や内容の妥当性が必要なら、スキーマに制約を書くか、後段で別に検査します。

## 長時間ジョブには stream-json

`--output-format stream-json` （ `--verbose` と併用）にすると、実行中のイベントが1行1JSONで流れます。ツール実行の経過が見えるのでログ・監査の材料になるほか、 `api_retry` というイベントで **Claude Code内蔵のAPIリトライ** が観測できます。この存在が、次のリトライ設計の前提になります。

## CI上の認証 — 2つの正規ルート

まず、Anthropicへ直接接続する代表的な2方式を整理します。 **本書の `--bare` 付きCLI例ではAPIキーを使います。** `CLAUDE_CODE_OAUTH_TOKEN` を用意しても、 `--bare` はOAuth認証を利用しないため、そのまま差し替えることはできません。OAuth方式は、対応する公式GitHub Actionなど、利用する起動方式の認証仕様に合わせて選びます。

| 方式 | 作り方 | 向いている場面 |
| --- | --- | --- |
| `ANTHROPIC_API_KEY` | Claude Console でAPIキーを発行 | チーム・組織のCI（推奨） |
| `CLAUDE_CODE_OAUTH_TOKEN` | 手元で `claude setup-token` を実行 | 個人リポジトリ（Pro/Maxなどのサブスクで動かす） |

どちらもリポジトリのSecretsに保存し、ワークフローの `env` で渡します。チームで使うならAPIキーを推奨します。OAuthトークンは **発行した個人のサブスクリプションに紐づく** ため、共有リポジトリの認証が特定個人の契約に依存する形になり、その人の退職・プラン変更がCIの障害になるからです。

このほか、Amazon BedrockやGoogle Cloud経由でモデルを呼ぶ構成、静的なシークレットを置かずにOIDCで連携するワークロードアイデンティティ連携もあります。企業のネットワーク・調達要件が絡む話なので「 [企業環境への導入](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/enterprise) 」の章で扱います。

シークレットの扱いで今日から守るべき最低線は2つ。 **キーをログに出さない** （GitHub Actionsのマスキングを過信せず、出力に埋め込まない）。そして **エージェント自身にシークレットを読ませない** （`.env` や認証ファイルへのRead権限を与えない）。後者がなぜ重要かは「セキュリティ」の章で、攻撃者の視点から説明します。

## 権限モード — ツールの限定と自動承認を分ける

ハンズオン記事で触れた `--allowedTools` を、CI前提で整理し直します。対話モードでは、エージェントがツールを使おうとするたびに確認が出て、あなたがy/nを返していました。無人実行にはその確認相手がいないので、2つの問いを別々のフラグで先に答えておきます。 **そもそもどのツールを持たせるか** と、 **持たせたツールのどの操作を確認なしで通すか** です。

**`--tools`** — セッションに提供する組み込みツールを限定します。読み取り専用にするなら `Read,Grep,Glob` だけを残し、Bashや書き込みツールを渡しません。渡していないツールは、エージェントがどう判断しても呼び出せません。

**`--allowedTools`** — 自動承認する操作の列挙。これだけでは、列挙していないツールが取り除かれるわけではありません。 `--tools` で渡した中の、確認を省いてよい操作を指定するフラグです。 `Bash(git diff *)` のようにコマンド単位まで絞れます。prefix一致の `*` の前には **スペースが必要** という罠があり、 `Bash(git diff*)` と書くと `git diff-index` のような別コマンドまで一致します。

**`--permission-mode`** — セッション全体の基準線。CIで覚えるべきは2つです。

- `acceptEdits`: ファイル編集と基本的なファイル操作コマンドを自動承認。修正ジョブ向け
- `dontAsk`: 許可リストと読み取り専用コマンド以外を **すべて拒否** 。確認できない環境で、未承認の操作を止めるために使う

**`--disallowedTools`** — 明示的な禁止。許可リスト方式が基本なので出番は少ないですが、「何があっても触らせない」を宣言として残せます。

CIの既定形はこうなります。読み取り専用ジョブなら:

```
claude --bare -p "..." --tools "Read,Grep,Glob" \
  --allowedTools "Read,Grep,Glob" --permission-mode dontAsk
```

この例では `--tools` と `--allowedTools` に同じ3つを書いています。前者で持ち物を3つに限り、後者でその3つを確認なしで通し、 `dontAsk` でそれ以外を拒否する、という3段の宣言です。拒否された操作は前述の `permission_denials` に残るので、「絞りすぎて仕事にならない」場合も記録から調整できます。 `--dangerously-skip-permissions` は、名前のとおりなので本書では以後登場しません。

## タイムアウト・リトライ・冪等性 — 無人ジョブの三種の神器

ローカルの実験とCIの本番を分ける、最後の3要素です。

## 上限は3系統かける

「 [ガードレール](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/guardrails) 」の章で体系化しますが、実行の上限だけ先に導入します。系統の違う3つを重ねます。

| 系統 | 手段 | 何を防ぐか |
| --- | --- | --- |
| 時間 | GitHub Actionsの `timeout-minutes` 、 `timeout` コマンド | ハング・無限ループ |
| ターン | `--max-turns` | エージェントの試行錯誤の暴走 |
| コスト | `--max-budget-usd` | 上2つをすり抜けた高額ジョブ |

どれか1つでは穴が残ります。短時間で高額（大きなコンテキストの反復）、低額で長時間（外部コマンド待ち）のような失敗モードがそれぞれあるからです。3つ全部、雑にでいいので最初から掛けておきます。

## リトライは「分類してから、薄く」

失敗したら再実行、を無条件にやってはいけません。ポイントは2つです。

**第一に、内蔵リトライがあることを知る。** レート制限や過負荷のようなAPI起因の一時エラーは、Claude Code自身がリトライします（ `stream-json` の `api_retry` イベントで観測できるやつです）。つまり外側のリトライは「内蔵リトライでも救えなかった失敗」に対する薄い保険で十分です。回数は1回、多くて2回。

**第二に、リトライしてよい失敗かを分類する。** `is_error` が立ったら `result` の中身で分岐します。

- **リトライする価値がある**: 過負荷・タイムアウトなど一時的なもの
- **リトライしても無駄**: 認証切れ、課金上限、不正なリクエスト。人間に通知して止まるのが正解
- **リトライしてはいけない**: プロンプトや設計の問題で毎回同じ失敗をするもの。リトライは同じ結果に課金を重ねるだけ

「 [なぜ「本番運用」は別物なのか](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/why-production-is-different) 」の章で、失敗ジョブほど高くつくと書きました。その主犯が、分類なしの一律リトライです。リトライのたびに `--max-budget-usd` は掛け直されることも忘れずに——初回に加えて外側で3回リトライすれば、最大4回分の予算を消費します。

## 冪等性 — 2回走っても事故にならない設計

無人ジョブは必ず二重に走ります。cronの重複、workflowの手動再実行、リトライ。「同じジョブが2回走ったら何が壊れるか」に答えを持っていない自動化は、本番運用とは呼べません。

設計原則は4つです。

1. **成果物はPR経由にする** （直接pushしない）。二重に走っても壊れるのは「PRが2本立つ」までで、mainは無傷です
2. **着手前に既存の成果物を確認する** 。同じ内容のPR・ブランチが既にあれば、何もせず正常終了する。「静かに何もしない」は無人ジョブの立派な成功です
3. **ブランチ名を決定的にする** 。 `fix/nightly-20260814` のようにジョブと日付から一意に決まる名前にすれば、二重起動は「同名ブランチがあるので終了」に自然に落ちます
4. **CIの同時実行を絞る** 。GitHub Actionsなら `concurrency` グループで、同じジョブの並走を1本に制限できます

## 最小のCI組み込み — 読み取り専用ジョブを1本置く

この章の部品を全部組んで、実際にCIへ載せます。「なぜ「本番運用」は別物なのか」の章の原則どおり、最初のジョブは **読み取り専用** です。題材は本書共通のサンプルアプリ（タスク管理・TypeScript）ですが、Node系のリポジトリならほぼそのまま使えます。

やることは単純で、毎晩、型チェックとテストを走らせ、 **失敗したときだけ** Claudeが失敗ログの一次調査をして、結果をワークフローのサマリーに書き出します。修正はまだしません。

まず、調査結果を受け取るスキーマです。 `required` に挙げた `summary` と `findings` が、この後のワークフローで後段が読む唯一の場所になります。

```
{
  "type": "object",
  "properties": {
    "summary": { "type": "string", "description": "全体状況の要約(1〜2文)" },
    "findings": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "file": { "type": "string" },
          "cause": { "type": "string", "description": "失敗の原因" },
          "suggestion": { "type": "string", "description": "修正の方向性" }
        },
        "required": ["file", "cause", "suggestion"]
      }
    }
  },
  "required": ["summary", "findings"]
}
```

ワークフロー本体です。長いので、先に見る場所を3つ挙げておきます。Triage failuresステップの `if:`、 `claude` に渡している引数の並び、そして実行直後の `jq -e` です。この3か所が、この章で説明してきた判断の置き場所です。

```
name: Nightly Triage
on:
  schedule:
    - cron: "0 18 * * *" # JST 3:00
  workflow_dispatch: {}

concurrency:
  group: nightly-triage

permissions:
  contents: read

jobs:
  triage:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    steps:
      - uses: actions/checkout@v6
        with:
          persist-credentials: false

      - run: npm ci

      - name: Run checks
        id: checks
        run: |
          set +e
          npx tsc --noEmit > /tmp/typecheck.log 2>&1
          echo "typecheck=$?" >> "$GITHUB_OUTPUT"
          npx vitest run > /tmp/test.log 2>&1
          echo "test=$?" >> "$GITHUB_OUTPUT"

      - name: Triage failures
        if: steps.checks.outputs.typecheck != '0' || steps.checks.outputs.test != '0'
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          npm install -g @anthropic-ai/claude-code@2.1.263

          claude --bare -p "型チェックとテストの失敗ログが /tmp/typecheck.log と /tmp/test.log にある。リポジトリのコードを読み、失敗ごとに原因と修正の方向性をまとめて" \
            --tools "Read,Grep,Glob" \
            --allowedTools "Read,Grep,Glob" \
            --permission-mode dontAsk \
            --output-format json \
            --max-turns 20 --max-budget-usd 0.50 \
            --json-schema "$(cat .github/schemas/triage-schema.json)" \
            > /tmp/triage.json || { echo "::error::claude実行が失敗"; exit 1; }

          if ! jq -e '
            .type == "result" and .subtype == "success" and .is_error == false
            and (.structured_output.summary | type == "string")
            and (.structured_output.findings | type == "array")
          ' /tmp/triage.json > /dev/null; then
            echo "::error::正常な調査結果を確認できない"
            exit 1
          fi

          {
            echo "## 夜間チェックの一次調査"
            jq -r '.structured_output.summary' /tmp/triage.json
            echo ""
            jq -r '.structured_output.findings[]
              | "- **\(.file)**: \(.cause) — \(.suggestion)"' /tmp/triage.json
          } >> "$GITHUB_STEP_SUMMARY"

          echo "cost_usd=$(jq -r '.total_cost_usd' /tmp/triage.json)"
```

設計ポイントを3つだけ。

1. **Claudeは失敗時にしか呼ばれない。** 全部緑の夜はAPIコストゼロです。「AIを呼ぶ前に、呼ぶ必要があるかを機械的に判定する」はコスト設計の基本形です
2. **AIに渡すツールは読み取り3つだけ。** `--tools` でBash・Edit・Writeを外し、 `dontAsk` で未承認の操作を拒否します。AIのツール操作による書き換えを防ぎますが、前段の `npm ci` やテストはコードを実行するため、信頼済みブランチと使い捨てランナーが前提です。被害半径を絞ったまま、運用の勘所（コスト感・出力のばらつき・ログ）を安全に学べます
3. **後段が読むのは `structured_output` だけ。** サマリー生成はスキーマ済みの構造から機械的に組み立てており、自由文のパースがどこにもありません。 `is_error` が `false` でも `structured_output` が欠けていれば `jq -e` が失敗し、ジョブは止まります。「読めなかった」を成功に変換しないという冒頭の到達点が、ここに埋まっています

このワークフローの成功・失敗は、一次調査の成否を表します。検出した型・テストの失敗はサマリーに残しますが、調査に成功すればジョブは成功で終わります。つまり、ジョブが緑でもコードは壊れていることがあります。マージ可否を決める通常のCIチェックには、そのまま流用しないでください。また、本番ではNodeとClaude Codeのバージョンを検証済みのものに固定します。

翌朝、あなたが見るのはワークフローのサマリーと `cost_usd` の行です。サマリーの見立てが実際の失敗原因と合っているか、コストが上限に対してどの水準かを、しばらく毎朝確かめてください。このジョブを1〜2週間動かすと、毎朝のサマリーとコストの実測が溜まります。それがそのまま、次の段階——書き込みを伴う自律修正——へ進む判断材料になります。

## まとめ

- `claude -p` はAgent SDKのCLI。 **終了コードと `is_error` の両方** をチェックするのが基本の型（認証切れも「結果」として返ってくる）
- CIでは **`--bare` を既定に** 。再現性の確保と、未信頼のリポジトリ内容を実行前に読み込まないための境界線。必要なコンテキストは宣言的に渡す
- 機械処理は `result` の自由文ではなく **`--json-schema` + `structured_output`** で。コストは `total_cost_usd` （クライアント推計）、権限の過不足は `permission_denials` で観測する
- 本書の `--bare` 付きCLI例は `ANTHROPIC_API_KEY` で認証する。個人サブスクのOAuthは対応する起動方式で使う。OAuthトークンを共有リポジトリの基盤にしない
- 上限は **時間・ターン・コストの3系統** 。リトライは失敗を分類してから薄く。冪等性は「PR経由・着手前確認・決定的な名前・concurrency」の4原則
- 最初のCIジョブは読み取り専用の一次調査から。失敗時にしかAIを呼ばない構造がコスト設計の基本形

次の「 [自律修正ループの設計](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/autonomous-fix-loop) 」の章では、このジョブに「修正して、検証して、コミットする」を足します。持ち越す前提は3つです。結果の判定は終了コードと `is_error` と `structured_output` で行うこと、書き込みは `--tools` で渡した範囲に限ること、上限は時間・ターン・コストの3系統で掛けること。読み取り専用の一次調査が、検出→修正→検証→コミットの自律修正ループに変わります。
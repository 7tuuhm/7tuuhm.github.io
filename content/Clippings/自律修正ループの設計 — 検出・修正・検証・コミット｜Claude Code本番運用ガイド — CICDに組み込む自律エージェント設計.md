---
title: "自律修正ループの設計 — 検出・修正・検証・コミット｜Claude Code本番運用ガイド — CI/CDに組み込む自律エージェント設計"
source: "https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/autonomous-fix-loop"
author:
published:
created: 2026-09-18
description:
tags:
  - "clippings"
---
Chapter 04

## 自律修正ループの設計 — 検出・修正・検証・コミット

[

はんぺん

2026.09.14に更新

](https://zenn.dev/hampen2929)

前の「 [ヘッドレス実行の基礎](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/headless-basics) 」の章で作った夜間ジョブは、失敗ログを読んで報告するところまででした。結果の判定は終了コードと `is_error` と `structured_output` で行い、ツールは `--tools` で渡した読み取り3つだけ、上限は時間・ターン・コストの3系統。この章では、その土台に「直して、検証して、PRを出す」を足します。完成するのは本書の中心部品—— **自律修正ループ** です。

やること自体は、権限に書き込みを足してプロンプトを変えるだけに見えます。しかし実際に毎晩回るループにするには、4つの設計判断が要ります。 **何を検出とするか。どう切り出すか。何をもって合格とするか。人間にどう渡すか。** この章はこの4つを順に潰していきます。読み終えたとき、検証に落ちた夜にはPRを出さずに止まり、翌朝その理由がサマリーで分かるジョブを組めるようになります。

## ループの全体像 — 5つの段と3つの出口

先に全体像です。

<iframe src="https://embed.zenn.studio/mermaid#zenn-embedded__42249dbe3a18e" frameborder="0" height="1140.9375"></iframe>

「 [なぜ「本番運用」は別物なのか](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/why-production-is-different) 」の章で置いた原則が、この図にそのまま効いています。図で追ってほしいのは矢印の向きです。修正の箱へ戻る矢印はリトライの1本だけで、PRへ届く経路は検証の「合格」を必ず通ります。出口は必ずPRで、mainには直接書きません。そして出口は3つあります。 **直ってPRが出る。対象がなくて何もしない。直せなくて報告する。** 3つとも設計に含めるべき結果です。

ただし、CIの終了状態は区別します。対象なしは成功、修正不能や検証不合格は通知につなげるため失敗で終えます。「直せない」は想定内でも、黙って成功扱いにはしません。翌朝の人間にとって、緑のジョブは「調べるべき失敗はない」の合図です。PRが出ていれば、そのレビューは別に要ります。直せなかった夜まで緑にすると、その合図が嘘になります。

## 段1: 検出 — 「探させない」のが第一原則

最初の設計判断は、意外かもしれませんが「AIに探させない」ことです。

「リポジトリの問題を見つけて直して」と渡すのは、無人運用では悪手です。理由は3つあります。 **再現性** — 何が見つかるかが毎晩変わり、ジョブの挙動が予測できなくなります。 **コスト** — 探索はリポジトリを広く読むので高くつきます。 **スコープ** — 探す権限を与えると、直したくなるのがエージェントの性です。前章まで積み上げた「範囲を絞る」設計が根本から崩れます。

そこで役割を分けます。 **検出は決定的な道具がやる。AIは検出済みのリストを修正するだけ。** 型チェッカー、テストランナー、linter、依存の脆弱性チェック——検出器はすでにリポジトリに揃っているはずです。ループの入力は「道具が出したエラーのリスト」であって、「何かおかしいかもしれないという疑い」ではありません。

もうひとつ、同じ発想の線引きがあります。 **機械で直せるものにAIを使わない。** フォーマットは formatter が、 `--fix` で直るlint指摘は linter 自身が直せばよく、そこにトークンを払う理由はありません。AIの持ち場は「機械では直せないが、判断の幅が狭い」ゾーンです。このゾーンに入るシグナルを、修正の性質と一緒に並べると、最初の題材が見えてきます。

| 検出シグナル | 修正の性質 | 自律修正の相性 |
| --- | --- | --- |
| 型エラー | 局所的で、合否が型チェッカーで即検証できる | ◎ 最初の題材に最適 |
| テスト失敗 | 検証は同じ道具でできるが、原因が仕様レベルのことがある | ○ 「直せないならskip」前提で |
| lint指摘（ `--fix` 不可のもの） | 局所的だが、抑止コメントでの誤魔化しが起きやすい | ○ 禁止事項の設計が重要 |
| 依存ライブラリの更新 | 変更自体は機械的、影響はテストの厚み次第 | ○ テストが薄いと危険 |
| ドキュメントとコードの乖離 | 検出の機械化が難しい | △ まず検出器を作るのが先 |

どれから始めるかは「なぜ「本番運用」は別物なのか」の章の3条件——失敗が可逆か、合否を機械判定できるか、文脈が閉じているか——をシグナルに当てるだけです。本章の実例に型エラーを選んだのは、3条件がいちばんきれいに揃うからです。

## 段2: 切り出し — 粒度と上限が運用性を決める

検出リストが手に入ったら、それをジョブにどう割るかを決めます。両極端はどちらも失敗します。

**全部まとめて1つのジョブ・1本のPR** にすると、まずレビューが死にます。50ファイルに散った雑多な修正のPRは、人間が読み切れないので「AIが直したなら、まあいいか」で通り始めます。これは「 [品質評価](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/quality-evals) 」の章で扱うレビュー形骸化の入口です。さらに、1件の無理筋な修正が検証を落とすと、残り49件の正しい修正まで道連れにします。

**1件ずつN本のジョブ** にすると、起動オーバーヘッド（依存インストール、コンテキスト読み込み）をN回払ううえ、同じファイルを別ジョブが触って衝突します。

現実解はシンプルです。 **シグナルの種類でまとめて、件数に上限を掛ける。** 「今夜は型エラーを、先頭から最大5件まで」。これで十分です。

全部やらなくていいのか、と思うかもしれません。いいのです。ここが対話利用との大きな発想の違いで、 **このループは毎晩走ります** 。小さく切った差分を採用できる検証設計なら、今夜の残りは明日のジョブが拾えます。1回で完璧にやろうとせず、1回あたりの被害半径と検証可能性を優先して、回数で収束させる。無人運用の設計は「1回の完成度」ではなく「反復の安定性」に最適化します。

![1回で全部やると巨大PRになりレビューが崩壊する。毎晩少しずつなら小さなPRで回り続ける](https://static.zenn.studio/user-upload/deployed-images/c56815717767a89609bbfe8d.jpg?sha=02008eea2e76a54f568a89c6a26305e2a6174514)

ただし、「今夜は5件まで」と「5件直せば採用する」は別の話です。 **部分修正を積み上げる設計と、全エラー解消を採用条件にする設計は別です。** 本章末の最小例は後者で、型チェックが1件でも失敗すればPRを出しません。検出が5件以内なら全部直して緑にできますが、6件以上あって5件しか直せなければ、型チェックは落ちたままなので人間に報告して終えます。部分修正を採用するには、修正前後の診断を安定したキーで比較し、「対象のエラーが消え、新しいエラーが増えていない」という別のゲートが必要です。単に検証を緩めることとは違います。本書の最小例がこの別ゲートを持たないのは、まず「全部緑になったときだけPR」という単純な条件で回し始めるためです。

切り出しでもうひとつ重要なのが、コンテキストの渡し方です。検出器の出力（エラーメッセージとファイルパス）をそのまま渡し、プロンプトで **渡したリスト以外に手を出さないことを明示** します。リポジトリ全体を読ませる必要はありません。修正に必要な周辺コードは、エージェントが該当ファイルから自分で辿れます。

## 段3: 修正 — プロンプトは契約書として書く

修正ジョブのプロンプトは、指示文というより契約書です。書くべき条項は5つあります。

```
あなたはこのリポジトリの型エラーを修正する。

1. 対象は /tmp/errors.txt にあるエラーのみ。最大5件まで。
   それ以外の問題を見つけても手を出さない
2. 検証コマンドはワークフロー側で実行する。対象の修正に集中する
3. 自信を持って直せないエラーは修正せず、skipとして理由を報告する。
   無理に直すより、直せないと報告するほうが価値が高い
4. 禁止事項:
   - テストコード・テストの期待値の変更
   - \`@ts-ignore\` \`@ts-expect-error\` \`as any\` の追加
   - tsconfig.json など設定の変更でエラーを黙らせること
5. 結果は指定のスキーマで報告する（fixed / skipped）
```

条項ごとに意図があります。1は段2の切り出しをプロンプト側からも固定するもの。2は修正と検証の役割分離。 **3が最重要** で、これを書かないと「全部直せ」と解釈したエージェントが無理筋の変更をひねり出します。skipという退路を先に用意しておくと、出力の質が目に見えて安定します。4は「直った」の定義を守る条項です。型エラーは `@ts-ignore` を書けば一瞬で消えますが、それは修正ではなく隠蔽です。エラーを消す最短経路が悪手である場合、その経路は名指しで塞ぎます。

ここで正直に言っておくと、 **プロンプトの禁止事項は「お願い」にすぎません** 。確率的に破られます。だから本書の設計では、同じ制約を機械的な検査でも重ねます（この章では差分の範囲チェック、体系的には「 [ガードレール](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/guardrails) 」の章）。プロンプトで意図を伝え、機械で強制する。二層で初めて「守られる制約」になります。

権限は前章の応用で、書き込みを最小限に足します。前章の読み取り専用ジョブとの差分は、 `--tools` にEditが増えたことと、 `--allowedTools` のEditにパスの条件がついたことの2点です。

```
--tools "Read,Grep,Glob,Edit" \
--permission-mode dontAsk \
--allowedTools "Read,Grep,Glob,Edit(src/**)"
```

この最小例ではBashを渡さず、編集の自動承認も `src/` 配下に限定します。Bashが無いので、エージェントは型チェックを自分で走らせられません。それでよく、型チェックは後段のVerifyに任せます。`.git/` や `.claude/` を編集可能にすると、後段のGit操作やフック設定を変える経路になるため、編集範囲に含めません。修正ジョブに `git push` の権限がないことに注目してください。コミットとPR作成はエージェントではなく、後段のワークフロー側のスクリプトがやります。

## 段4: 検証 — 合格はAIの自己申告ではなくCIの終了コード

エージェントが「直しました」と報告したら、それを **信用せずに** 検証します。意地悪ではなく役割分担です。報告文と実際の差分が食い違うことは現実にあるので、合否の判定者はワークフロー側が握ります。

検証の設計は3点です。

**第一に、対象だけでなく全体を再実行する。** 型エラー5件を直したなら、確認するのは「その5件が消えたか」ではなく「型チェック・テスト・lintの全体が、修正前より悪くなっていないか」です。直した箇所の隣で新しい何かを壊すのは、人間の修正でもAIの修正でも起きます。回帰の検出は全体実行でしか拾えません。

**第二に、差分の範囲を機械チェックする。** 変更されたファイル一覧を取り、想定外の場所——CI設定、依存定義、テストコード——に手が入っていたら、内容を見ずに破棄します。プロンプトの禁止事項（段3）の機械的な裏付けです。

**第三に、リトライは1回だけ。** 検証に落ちたら、失敗ログを渡してもう一度だけ修正させます。それでも落ちたら、その夜は諦めてエスカレーションです。「直るまで自己修正させる」ループはコストの穴になるうえ、リトライを重ねた修正ほど強引になっていきます。前章のリトライの原則——分類してから、薄く——はここでも同じです。

検証に通らなかった変更は、 **PRにせず捨てます** 。捨てた事実と理由はサマリーに残します。中途半端な修正のPRを出すくらいなら、「今夜は直せませんでした」という報告のほうが何倍も価値があります。

## 段5: 出口 — PRの型とエスカレーション

## PRの型

検証を通った変更は、PRとして出します。ブランチ名は前章の冪等性原則どおり決定的に（例: `nightly/typefix-20260818` ）。同名ブランチが既にあれば、今夜のジョブは何もせず終了します。

PR本文は毎回同じ型で自動生成します。読む人間が数秒で判断できることが目的です。

```
## 夜間ジョブ: 型エラーの自律修正

- 検出: tsc で型エラー5件
- 修正: 5件（このPR）
- skip: 0件
- 検証: tsc / vitest / eslint 全パス
- コスト: $0.31

### skipした項目
なし
```

この最小例では、未解消の型エラーが残るとPRは出ません。skipした項目と理由は、失敗時のStep Summaryに残して **人間が見るべき場所の案内** にします。PRのskip欄は、検証が通る場合の報告形式として残しています。

コミットとPRには、AIが作ったことを機械可読な形で残します（コミットの `Co-Authored-By` 、PRのラベルなど）。数ヶ月後に「このコードはどう入ったのか」を遡る手がかりで、「 [セキュリティ](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/security) 」の章の監査ログにつながります。

PRを受け取った人間は、何を見ればよいでしょうか。最初に、型チェック・テスト・lintがこのPRのコミットに対して実行され、合格しているかを確かめます。本文の「検証: 全パス」はワークフローの報告なので、必須チェックがあるリポジトリではPR上の結果と突き合わせます。それが確認できれば、同じチェックを手元で走らせ直す必要はありません。次に人間が判断するのは、差分が本文の「修正: 5件」と対応しているか、直し方が設計方針に合っているか、skipの理由に納得できるか、既存のテストがこの変更を検証できているかです。機械の判定を人間が再演せず、判定の存在と結果を確かめてから、機械が判定できないことに目を使う——この分担は「品質評価」の章で正面から扱います。

## GITHUB\_TOKEN の罠

GitHub Actionsでは、 **トークンの種類によって後続CIの起動方法が変わります。** `GITHUB_TOKEN` が発生させたイベントは原則として新しいワークフローを起動しません。ただし現在は、 `workflow_dispatch` / `repository_dispatch` に加えて、PRの `opened` / `synchronize` / `reopened` に例外があります。後者は承認待ちの実行を作成し、書き込み権限を持つ人が「Approve workflows to run」で開始します。

本書の最小例は、ループ内のVerifyで検証を完結させ、PRでは人間が必要なCIを承認する形です。必須チェックがあるリポジトリでは、その完了もマージ条件に含めます。自動で後続CIまで流す場合はGitHub Appのインストールトークンを使います。個人のPATでも起動できますが、チームの認証が個人に依存するため、組織導入ではAppを優先します。

このPRイベントの例外は公式資料の記述に基づきます [^1] 。

仕様は [GitHub公式のワークフロー起動条件](https://docs.github.com/en/actions/how-tos/write-workflows/choose-when-workflows-run/trigger-a-workflow) で確認できます（2026-09-08確認）。

## エスカレーション

最後に、直せなかったときの設計です。原則はひとつだけ。 **静かに失敗しない。**

実行されたのに何も出てこない夜が、無人運用でいちばん怖い状態です。PRが出ない理由が「対象ゼロ」なのか「全部skip」なのか「ジョブ自体が死んだ」のか、翌朝の人間に区別がつかないからです。だから3つの出口すべてが、それぞれ痕跡を残します。

- **対象ゼロ**: ジョブのサマリーに「検出0件」と1行残して正常終了
- **修正あり**: PR（skip込みの本文）
- **全滅・検証不合格**: サマリーに失敗の内訳とログを残し、ジョブを失敗として終える（通知はここに紐づける）

## 実例: サンプルアプリの夜間型エラー修正ジョブ

この章の設計を全部入れて、前章の一次調査ジョブを修正ジョブに拡張します。題材は引き続き共通サンプルアプリ（タスク管理・TypeScript）です。

報告スキーマから。

```
{
  "type": "object",
  "properties": {
    "fixed": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "file": { "type": "string" },
          "error": { "type": "string", "description": "対象のエラー" },
          "description": { "type": "string", "description": "何をどう直したか" }
        },
        "required": ["file", "error", "description"]
      }
    },
    "skipped": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "file": { "type": "string" },
          "error": { "type": "string" },
          "reason": { "type": "string", "description": "直さなかった理由" }
        },
        "required": ["file", "error", "reason"]
      }
    }
  },
  "required": ["fixed", "skipped"]
}
```

ワークフロー本体です。 **信頼済みの既定ブランチを使い捨てランナーで処理する最小例** です。簡潔さのため自動リトライは省き、検証不合格はその場で報告して停止します。 `ai-generated` ラベルと、ActionsによるPR作成の許可は事前に用意してください。検出コマンドが作る `tsconfig.tsbuildinfo` やcoverageなどの生成物は、事前に `.gitignore` へ設定します。実行後に検査対象を都合よく除外してはいけません。

追うときは、ステップのコメントにある段の番号を手がかりにしてください。Detectが `/tmp/errors.txt` を作り、Fixの `--tools` にBashが無く、Verifyが範囲検査と全体再実行の順に並んで、どちらで落ちても `exit 1` で止まる。この流れが見えれば、残りは前章のジョブと同じ部品です。

```
name: Nightly Typefix
on:
  schedule:
    - cron: "30 18 * * *" # JST 3:30
  workflow_dispatch: {}

concurrency:
  group: nightly-typefix

permissions:
  contents: write
  pull-requests: write

jobs:
  typefix:
    runs-on: ubuntu-latest
    timeout-minutes: 20
    steps:
      - uses: actions/checkout@v6
        with:
          persist-credentials: false

      - run: npm ci

      # 段1: 検出 — 決定的な道具で
      - name: Detect
        id: detect
        run: |
          set +e
          npx tsc --noEmit > /tmp/tsc-full.log 2>&1
          echo "typecheck=$?" >> "$GITHUB_OUTPUT"
          head -60 /tmp/tsc-full.log > /tmp/errors.txt

      - name: Nothing to do
        if: steps.detect.outputs.typecheck == '0'
        run: echo "検出0件。今夜は何もしない" >> "$GITHUB_STEP_SUMMARY"

      # 冪等性: 同名ブランチが既にあれば終了
      - name: Check existing branch
        id: dedupe
        if: steps.detect.outputs.typecheck != '0'
        run: |
          branch="nightly/typefix-$(date +%Y%m%d)"
          echo "branch=$branch" >> "$GITHUB_OUTPUT"
          if git ls-remote --exit-code origin "$branch" > /dev/null 2>&1; then
            echo "skip=true" >> "$GITHUB_OUTPUT"
            echo "既にブランチあり。終了" >> "$GITHUB_STEP_SUMMARY"
          fi

      # 段3: 修正 — 書き込みは最小権限で
      - name: Fix
        if: steps.detect.outputs.typecheck != '0' && steps.dedupe.outputs.skip != 'true'
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          npm install -g @anthropic-ai/claude-code@2.1.263
          claude --bare -p "$(cat .github/prompts/typefix.md)" \
            --tools "Read,Grep,Glob,Edit" \
            --permission-mode dontAsk \
            --allowedTools "Read,Grep,Glob,Edit(src/**)" \
            --output-format json \
            --max-turns 40 --max-budget-usd 1.00 \
            --json-schema "$(cat .github/schemas/typefix-schema.json)" \
            > /tmp/fix.json || { echo "::error::claude実行が失敗"; exit 1; }
          jq -e '
            .type == "result" and .subtype == "success" and .is_error == false
            and (.structured_output.fixed | type == "array")
            and (.structured_output.skipped | type == "array")
          ' /tmp/fix.json > /dev/null || {
            echo "::error::正常な修正結果を確認できない"; exit 1; }

      # 段4: 検証 — 差分の範囲チェック + 全体再実行
      - name: Verify
        if: steps.detect.outputs.typecheck != '0' && steps.dedupe.outputs.skip != 'true'
        run: |
          # 最小例は既存src/*.tsの変更だけを受け付ける。
          # 未追跡ファイルは無視対象も含めて検査し、srcへの新規追加を拒否する。
          if [ -n "$(git ls-files --others --exclude-standard)" ] ||
             [ -n "$(git ls-files --others --ignored --exclude-standard -- src/)" ]; then
            echo "::error::新規ファイルはこのジョブの対象外"
            exit 1
          fi
          git diff --name-only -z HEAD > /tmp/changed-paths
          python3 - <<'PYTHON'
          from pathlib import Path, PurePosixPath
          paths = Path('/tmp/changed-paths').read_bytes().split(b'\0')
          for raw in filter(None, paths):
              name = raw.decode()
              path = PurePosixPath(name)
              if (not name.startswith('src/') or path.suffix != '.ts'
                  or name.endswith(('.test.ts', '.spec.ts'))
                  or 'tests' in path.parts or '__tests__' in path.parts):
                  raise SystemExit('変更対象外のパスを検出')
          PYTHON
          if ! { npx tsc --noEmit && npx vitest run && npx eslint .; }; then
            echo "型・テスト・lintの検証不合格。PRは作成しません。" >> "$GITHUB_STEP_SUMMARY"
            jq -r '.structured_output.skipped[] | "- \(.file): \(.reason)"' /tmp/fix.json >> "$GITHUB_STEP_SUMMARY"
            exit 1
          fi

      # 段5: 出口 — PR
      - name: Create PR
        if: steps.detect.outputs.typecheck != '0' && steps.dedupe.outputs.skip != 'true'
        env:
          GH_TOKEN: ${{ github.token }}
        run: |
          if git diff --quiet HEAD; then
            echo "検証は合格しましたが採用する差分はありません。詳細:" >> "$GITHUB_STEP_SUMMARY"
            jq -r '.structured_output.skipped[] | "- \(.file): \(.reason)"' /tmp/fix.json >> "$GITHUB_STEP_SUMMARY"
            exit 0
          fi
          branch="${{ steps.dedupe.outputs.branch }}"
          git config user.name "nightly-typefix[bot]"
          git config user.email "bot@example.com"
          git checkout -b "$branch"
          git add -u -- src/
          git -c core.hooksPath=/dev/null commit -m "fix: 夜間ジョブによる型エラー修正" \
            -m "Co-Authored-By: nightly-typefix <bot@example.com>"
          gh auth setup-git
          git -c core.hooksPath=/dev/null push origin "$branch"

          {
            echo "## 夜間ジョブ: 型エラーの自律修正"
            echo "- 修正: $(jq '.structured_output.fixed | length' /tmp/fix.json)件 / skip: $(jq '.structured_output.skipped | length' /tmp/fix.json)件"
            echo "- 検証: tsc / vitest / eslint 全パス"
            echo "- コスト: \$$(jq -r '.total_cost_usd' /tmp/fix.json)"
            echo ""
            echo "### skipした項目"
            jq -r '.structured_output.skipped[] | "- \(.file) — \(.reason)"' /tmp/fix.json
          } > /tmp/pr-body.md
          gh pr create --title "夜間: 型エラーの自律修正 $(date +%Y-%m-%d)" \
            --body-file /tmp/pr-body.md --label ai-generated
```

設計ポイントの復習を3つだけ。

1. **エージェントの権限に `git` がない。** 修正するのはエージェント、コミットしてPRを出すのはワークフロー。Verifyを通過した差分だけを採用する手順です。悪意あるコードの安全な実行まで保証するものではなく、未信頼入力では後述のジョブ隔離が必要です
2. **Verifyが二段構え。** 差分の範囲チェック（プロンプト禁止事項の機械的裏付け）→ 全体再実行（回帰の検出）。どちらかが落ちればPRは出ません
3. **3つの出口すべてが痕跡を残す。** 検出0件・検証不合格（全件skipを含む）・修正PR、どの夜でもサマリーを見れば何が起きたか分かります

条件を1つ変えて考えてみます。検出シグナルを型エラーからテスト失敗に変えたら、このワークフローの何を変える必要があるでしょうか。Detectの検出コマンドと、Verifyが全体再実行することは同じです。禁止事項の「テストコードの変更禁止」もそのまま生きます。変わるのは段3の契約で、テストが仕様変更を反映していて実装側だけでは直せない場合を、skipの理由として明示的に許す必要があります。表で「直せないならskip前提」と書いたのはこの意味です。型エラーでは「型を合わせる」以外の解釈がほとんど無いのに対し、テスト失敗は「テストが正しいのか、実装が正しいのか」という判断を含みます。判断の幅が広がる分、skipの退路を広く取り、PRに残す説明を厚くします。

## まとめ

- 自律修正ループは **検出→切り出し→修正→検証→PR** 。出口は「PR」「何もしない」「直せないと報告」の3つを設計し、CIの成功・失敗とは分ける
- **検出は決定的な道具、AIは修正だけ** 。機械で直せるものは機械で直し、AIは「機械では直せないが判断の幅が狭い」ゾーンに使う
- 切り出しは **種類でまとめて件数上限** 。毎晩回るループは、1回の完成度ではなく反復の安定性に最適化する
- プロンプトは契約書。 **skipの退路・禁止事項・出力契約** を明記し、同じ制約を機械チェックでも重ねる（二層防御）
- 合格を決めるのはAIの自己申告ではなく **全体再実行の終了コード** 。検証に落ちた変更は捨て、リトライは1回まで
- `GITHUB_TOKEN` で作成・更新したPRのCIは承認待ちになる場合がある。ループ内の検証と必須チェックの完了を区別し、後続CIの自動起動にはGitHub Appトークンを使う

これで1本のループが毎晩回るようになりました。しかし対象を広げていくと、次の壁に当たります。修正に必要な文脈が1つのコンテキストウィンドウに収まらない、調査と修正で必要な性質が違う——次の「 [サブエージェント編成](https://zenn.dev/hampen2929/books/claude-code-production-guide/viewer/subagents) 」の章は、この「1人のエージェントの限界」を分割統治で越える話です。そこでも、合否を決めるのがVerifyと人間のマージであることは変わりません。

脚注

[^1]: 公式原文: “the resulting `pull_request` event creates workflow runs in an approval-required state.” 今回は公式資料との照合であり、この挙動をGitHub Actionsで実走した確認ではありません。
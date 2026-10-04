---
title: "Post by @RiverAi7z on X"
source: "https://x.com/RiverAi7z/status/2099124247667044548"
author:
  - "[[@RiverAi7z]]"
published: 2026-09-13
created: 2026-09-18
description: "ほら、言ったでしょ？Piの作者のGitHubをもっと頻繁にチェックすれば、いいものが見つかるって。彼がPi用に書いたcontrol.tsを使えば、異なる端末でコーディングしているエージェント同士が直接メッセージをやり取りできるんだ。pi --session-controlで起動す"
tags:
  - "clippings"
---
ほら、言ったでしょ？Piの作者のGitHubをもっと頻繁にチェックすれば、いいものが見つかるって。彼がPi用に書いたcontrol.tsを使えば、異なる端末でコーディングしているエージェント同士が直接メッセージをやり取りできるんだ。pi --session-controlで起動すると、エージェントは他のオンラインセッションを検出したり、タスクを送信したり、応答を照会したり、相手側が処理を完了するまで待ったりできる。基盤となるメカニズムはローカルのUnixソケットを使用するので、追加のサービス展開は不要だ。

---

インストール方法 \`\`\`bash mkdir -p ~/.pi/agent/extensionscurl -fL \\

https://raw.githubusercontent.com/mitsuhiko/agent-stuff/main/extensions/control.ts… \\ -o ~/.pi/agent/extensions/control.ts \`\`\` 然后启动： \`\`\`bash pi --session-control \`\`\`

https://github.com/mitsuhiko/agent-stuff/blob/main/extensions/control.ts…

---

## Comments

> **一地鸡毛 @zhang\_baoqing** · [2026-09-13](https://x.com/zhang_baoqing/status/2099284250998796616)
> 
> 本当にherdrを使っていませんか？
> 
> > **RiverAi7z @RiverAi7z** · [2026-09-14](https://x.com/RiverAi7z/status/2099292769219129762)
> > 
> > herdr でこれができることは知っていますが、このビデオでは herdr スキルが読み込まれていません。これは control.ts プラグインが動作しているところです。

> **ゴブリンタウン市民、CFA --rtrd/acc @0xItsover** · [2026-09-13](https://x.com/0xItsover/status/2099152055646695492)
> 
> herdrコマンドが存在するのに、なぜこれをherdrで使用しているのですか？
> 
> > **RiverAi7z @RiverAi7z** · [2026-09-14](https://x.com/RiverAi7z/status/2099292194574373032)
> > 
> > herdr でそれができることは知っていますが、control.ts プラグインを紹介するだけです。

> **ドン・ジャン @dzhang69** · [2026-09-13](https://x.com/dzhang69/status/2099162138644189226)
> 
> 牧畜民を使うだけ
> 
> > **RiverAi7z @RiverAi7z** · [2026-09-14](https://x.com/RiverAi7z/status/2099467568163758242)
> > 
> > 異なるエージェントにherdrを使うのは賛成だが、piの場合はこのプラグインの方が速い

> **ファックオナイ @Nokia0421** · [2026-09-14](https://x.com/Nokia0421/status/2099320562824642744)
> 
> 端末間でタスクをやり取りしようと試みましたが、ソケットが接続されたからといってコンテキストが接続されたとは限りません。セッション制御における最も簡単な落とし穴は、誰が誰を待っているのかを把握することです。
> 
> > **RiverAi7z @RiverAi7z** · [2026-09-14](https://x.com/RiverAi7z/status/2099467304111374592)
> > 
> > 彼らに状況をまとめてもらって、それを送ってもらえばいいでしょう。

> **X @stayconne** · [2026-09-13](https://x.com/stayconne/status/2099165135416033670)
> 
> 差し支えなければ、こちらはどの端末でしょうか？
> 
> > **RiverAi7z @RiverAi7z** · [2026-09-14](https://x.com/RiverAi7z/status/2099292073908469982)
> > 
> > otty
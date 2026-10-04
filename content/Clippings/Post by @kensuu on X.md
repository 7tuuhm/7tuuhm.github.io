---
title: "Post by @kensuu on X"
source: "https://x.com/kensuu/status/2003012829914501378?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E2003012829914501378%7Ctwgr%5Eef185e033ffb7b1cc6bc999673b08dcf8d748a6e%7Ctwcon%5Es1_&ref_url=https%3A%2F%2Fnote.com%2Fshotovim%2Fn%2Fn40164f5b555c"
author:
  - "[[@kensuu]]"
published: 2025-12-22
created: 2026-09-15
description: "毎分、作業中のPCのスクリーンショットを撮って、それをmacのOCRでテキスト化してJSONにして一つのファイルにまとめておいて、それをAIで読み取らせて日報みたいなのを作るツールをAIとともに自分で作ってみてやっているんですが、 思った以上に、普通に使えて便利！何の作業をやっ"
tags:
  - "clippings"
---
毎分、作業中のPCのスクリーンショットを撮って、それをmacのOCRでテキスト化してJSONにして一つのファイルにまとめておいて、それをAIで読み取らせて日報みたいなのを作るツールをAIとともに自分で作ってみてやっているんですが、

思った以上に、普通に使えて便利！何の作業をやってたかとか、こういうことが学べましたよね、みたいなことまでちゃんとまとめてくれる。

日記を書いたり、作業のログを取るのが面倒だなーと思ってたんですが、これでほぼ何も考えずにできてしまう。

![Image](https://pbs.twimg.com/media/G8wf3XvakAAdPnI?format=jpg&name=large) ![Image](https://pbs.twimg.com/media/G8whavFaAAAXgMv?format=jpg&name=large)

---

GoogleカレンダーをClaudeさんは読み込んでくれるので、ログと合わせてGoogleカレンダーの情報も入れ込んでおくと、日報的にそこそこ満足がいくものになってきた。

次はSlackでの発言とかまでAPIで取得していこうかな・・・。

---

## Comments

> **MyOwnChannel @R\_SQL\_English** · [2025-12-22](https://x.com/R_SQL_English/status/2003023614829019368)
> 
> 私も同じ思想で日報を自動化したいと思っていました、、、！差し支えなければ「定期的に作業画面をスクショする」の箇所をどのように実装したかご教示願えないでしょうか。
> 
> > **けんすう @kensuu** · [2025-12-22](https://x.com/kensuu/status/2003046511337447934)
> > 
> > 結構単純でPythonで
> > 
> > 1\. AppleScriptで、いまアクティブなウインドウの名前を取得
> > 
> > 2\. macOSの標準にあるscreencaptureを使って画面全体をスクショ
> > 
> > 3\. macOSのVision Frameworkを使ってOCRで中身を読み取る
> > 
> > 4\. 取得した情報をJSONL形式でログを保存
> > 
> > 5\. キャプチャの画像を削除

> **みやはま @miyahama\_\_\_\_\_\_** · [2025-12-22](https://x.com/miyahama______/status/2003078930073518247)
> 
> OCRしてjsonに出力してそれをLLMに解析してもらってる感じでしょうか？
> 
> > **けんすう @kensuu** · [2025-12-22](https://x.com/kensuu/status/2003106300692193678)
> > 
> > ですです、かなり割り切っていますが、出てきたものの納得度は全然実用レベルです

> **無味乾燥さんと他99人 @UnaKiri\_Megane** · [2025-12-22](https://x.com/UnaKiri_Megane/status/2003178603815797032)
> 
> スクショを直接LLMに読ませると効率か精度が悪いんですか？
> 
> > **けんすう @kensuu** · [2025-12-22](https://x.com/kensuu/status/2003233641368002731)
> > 
> > 8時間とるとしたら、480枚の画像になるので、500円とか毎日かかってしまう可能性があり、コスト的に見合わないかなと。OCRの精度はLLMの方が良いですが、アウトプット的にはそんなに体感変わらないのです。

> **sa+ @sa7836475955110** · [2025-12-22](https://x.com/sa7836475955110/status/2003043329249038351)
> 
> 昔のRewindですね！
> 
> > **けんすう @kensuu** · [2025-12-22](https://x.com/kensuu/status/2003046753436889136)
> > 
> > そんなのが！知らなかったですそれ

> **じゅん@複業せどらー @tradesedori** · [2025-12-23](https://x.com/tradesedori/status/2003473586754105460)
> 
> 素晴らしいアイデアありがとうございます。
> 
> 非エンジニアの私でも、Claudeに質問しながら１時間半で設定できました。

> **先読みBIZ｜海外ビジネスの最前線 @SakiyomiBiz** · [2026-03-04](https://x.com/SakiyomiBiz/status/2029321062610288661)
> 
> これすごいですね。"自分が何に時間を使ったか"を人間が覚えてなくてもAIが記録してくれるの、地味に革命的。日報って本来こうあるべきだったのかも。上司への報告じゃなくて、自分の振り返りツールとして。
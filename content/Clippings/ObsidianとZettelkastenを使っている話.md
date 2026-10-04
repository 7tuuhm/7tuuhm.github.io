---
title: "ObsidianとZettelkastenを使っている話"
source: "https://blog-dry.com/entry/2025/05/07/215832"
author:
  - "[[helloyuki]]"
published: 2025-05-07
created: 2026-08-21
description: "生成AIが直のMarkdownを読み込みやすいという話から、にわかにObdisianが注目を集めているようです。そしてObsidianが注目を集めるにあたり、Zettelkastenという手法にも同時にスポットライトが当たり始めているように見受けられます。実は両者をそれなりに使ってきたので、どう使っているかやどう思っているかについて簡単にまとめてみたいと思います。なお、勢いで書いたので事実誤認（AIでいうならハルシネーション）を含む可能性があります。厳密に見ると間違いがあるかもしれません。ご了承ください。 Zettelkasten 元々Obsidianを使い始めたのは、前職にてローカルで自分の…"
tags:
  - "clippings"
---
生成AIが直の [Markdown](https://d.hatena.ne.jp/keyword/Markdown) を読み込みやすいという話から、にわかにObdisianが注目を集めているようです。そしてObsidianが注目を集めるにあたり、Zettelkastenという手法にも同時にスポットライトが当たり始めているように見受けられます。実は両者をそれなりに使ってきたので、どう使っているかやどう思っているかについて簡単にまとめてみたいと思います。なお、勢いで書いたので事実誤認（AIでいうならハルシネーション）を含む可能性があります。厳密に見ると間違いがあるかもしれません。ご了承ください。

## Zettelkasten

元々Obsidianを使い始めたのは、前職にてローカルで自分のメモ書き等を管理したいためでした。というのも前職では最初の方、いい感じに使えるドキュメント管理ツールがなく、自分のメモ書きを残す場所を探していたという事情がありました（のちにConfluenceが導入されました）。Obsidianの機能にDaily Notesがありますが、これが日報として非常に便利でした。ローカルに [Markdown](https://d.hatena.ne.jp/keyword/Markdown) ファイルを置いておくだけで管理できるため、仕事の情報をプライベート用のツールに書いてしまうという [アンチパターン](https://d.hatena.ne.jp/keyword/%A5%A2%A5%F3%A5%C1%A5%D1%A5%BF%A1%BC%A5%F3) を踏まずに済む手段として活用していました。Obsidian自体はかれこれ3年くらい使っていることになります。

その後は技術メモを残したりといろいろ使っていたのですが、それから少し経った頃にZettelkasten（ツェッテルカステン）という情報管理の仕方に出会いました。具体的には下記のYouTuberの方が紹介している動画で出会いました。これが私の中ではよくフィットしており、この手法を最も素直に実現できるツールとしてObsidianを導入しているという流れです。Obsidian Neovimとの組み合わせは現在トライ中ですが、なんかバグっているのか上手に動いてくれなくて苦戦しています。

![](https://www.youtube.com/watch?v=zIGJ8NTHF4k)
[www.youtube.com](https://www.youtube.com/watch?v=zIGJ8NTHF4k)

Zettelkasten本体についての理解は実はあまり深くないのですが、簡単に紹介させてください。もともとはニコラス・ [ルーマン](https://d.hatena.ne.jp/keyword/%A5%EB%A1%BC%A5%DE%A5%F3) という [社会学](https://d.hatena.ne.jp/keyword/%BC%D2%B2%F1%B3%D8) 者（学生のときに [社会学](https://d.hatena.ne.jp/keyword/%BC%D2%B2%F1%B3%D8) の授業で出てきたなそいえば）が使っていた知的生産の手法でした。 [このニコラス・ルーマンは非常に多くの数の著作や論文を書いたことで有名で、70冊以上の本を書き、400近くの学術論文を発表したそうです。](https://studyhacker.net/memo-zettelkasten) 彼の知的生産能力を支えたのが、このZettelkastenという手法だったと言われています。

Zettelkastenは私の読んだいくつかの文献によると、次のようにカード（ノート）を分類して、それぞれを繋ぎ合わせながら運用されます。なおこの分類について、私も体系的にZettelkastenを学んだわけではないので、厳密性には欠ける可能性があります。

- Literature Notes: 文献ノート
- Fleeting Notes: いわゆるメモ書きにあたるもので、自分の思考をつらつらと書き留める。
- Permanent Notes: Zettelkastenで最も重要なノートで、Literature NotesとFleeting Notesを繋ぎ合わせて、自分の思考をさらにより構造化された形でまとめたもの。
- Structured Notes: 複数のPermanent Notesへのリンクを含むまとめとなるノート。
- Indexed Notes: 索引やキーワードなどをまとめたもの。私はこれは使用していません。Obsidianのさまざまなリンク機能が同等に機能すると考えているためです。

Obisidianでは次のように [ディレクト](https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8) リを切ってZettelkastenを管理しています。

```
00-Literature-Notes
01-Fleeting-Notes
02-Permanent-Notes
03-Structured-Notes
```

ちょっとしたこだわりですが、Literature Notes、Fleeting Notes、Permanent Notesはそれぞれ内部にさらに [ディレクト](https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8) リを設けてはいません。ファイルをフラットに置いています。さらに1階層掘って [ディレクト](https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8) リを用意すると単純に運用に迷いが出て大変になるほか、ZettelkastenはとくにFleeting NotesやPermanent Notesにおいて、前後のノートの関係性も大事にしています。そのため時系列で保存されると嬉しいわけなのですが、 [ディレクト](https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8) リに切るとこのZettelkastenプロジェクト内での時系列の構造が壊れてしまうことがあります。これを防ぐために、フラットにファイルを置くようにしています。

Literature Notesには、たとえば [Kindle](https://d.hatena.ne.jp/keyword/Kindle) や紙の書籍からの引用や、Web Clipperで収集したWebサイトの内容が入っています。一次文献として保存しておきたい内容を格納する場所として利用しています。

Fleeting Notesはかなり雑多にいろいろ詰め込んでいます。1行程度の考えたことが書かれていたり、あるいはある程度箇条書きでまとめた内容が入っていることもあります。Fleeting NotesとLiterature Notesの最大の違いは、Fleeting Notesは「自分の言葉で考えを書く」という点です。『まったく新しいアカデミックライティングの教科書』という書籍でも強調されていましたが、何か主張や思考をまとめる際には、必ず自分の言葉で [パラフレーズ](https://d.hatena.ne.jp/keyword/%A5%D1%A5%E9%A5%D5%A5%EC%A1%BC%A5%BA) しながらまとめるのが重要です。Fleeting Notesを書くときには特にこれを意識しています。

[![まったく新しいアカデミック・ライティングの教科書](https://m.media-amazon.com/images/I/31f3M1XdETL._SL500_.jpg "まったく新しいアカデミック・ライティングの教科書")](https://www.amazon.co.jp/dp/B0D97HJRT1?tag=helloyuki-22&linkCode=ogi&th=1&psc=1)

[まったく新しいアカデミック・ライティングの教科書](https://www.amazon.co.jp/dp/B0D97HJRT1?tag=helloyuki-22&linkCode=ogi&th=1&psc=1)

- 作者:[阿部 幸大](https://d.hatena.ne.jp/keyword/%B0%A4%C9%F4%20%B9%AC%C2%E7)
- 光文社
[Amazon](https://www.amazon.co.jp/dp/B0D97HJRT1?tag=helloyuki-22&linkCode=ogi&th=1&psc=1)

Permanent NotesはいくつかのFleeting Notesからアイディアを引っ張りながら、最終的に自分の考えを体系的にまとめあげるように使用しています。実はこのブログの原稿もPermanent Notesとしてまとめられたものがそのまま使用されていますが、とくに公開しなかったものも含め、こうした文章が書かれています。「外に出しても問題ないレベルのもの」がPermanent Notesには求められる、という記述を見ることもあります。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/y/yuk1tyd/20250507/20250507235231.png)

Obsidianで表示した、Zettelkastenタグに紐づくノートたち。ノート同士の関係性も辿ることができるのがいいポイント。

これらのノートは今のところ [iCloud](https://d.hatena.ne.jp/keyword/iCloud) で管理しています。 [iPhone](https://d.hatena.ne.jp/keyword/iPhone) でも見られるからという理由で [iCloud](https://d.hatena.ne.jp/keyword/iCloud) にしていますが、正直 [iPhone](https://d.hatena.ne.jp/keyword/iPhone) から見たことがほとんどありません。あんまり意味もないなと思い始めているので、近々 [GitHub](https://d.hatena.ne.jp/keyword/GitHub) での管理に変える予定です。

## Notionなど他ツールとObsidianの使い分け

これといって強いルールがあるわけではありませんが、Notionも併用しています。個人的にNotionは画像を貼ったり表を作ったりといった行為はやりやすいと思っていて、そうした用途が中心となる場合に使っています。たとえば、

- プライベートであれこれやりたいプロジェクトがあるとき。
- 自宅の備品の管理をしたいとき。
- 気に入った料理のレシピを保存したいとき。
- [YouTube](https://d.hatena.ne.jp/keyword/YouTube) で気に入った音楽をまとめたいとき。
- [ジャーナリング](https://d.hatena.ne.jp/keyword/%A5%B8%A5%E3%A1%BC%A5%CA%A5%EA%A5%F3%A5%B0) の置き場として。

あたりはすべてNotionにまとめています。とくにプライベート用のプロジェクトではPARA Methodという別の手法を取り入れています。PARA Methodについては詳しくは解説しませんが概要を説明すると、メモを「Project」「Area」「Task」「Resource」「Archive」に分類して管理するやり方です。特徴的な思想としては、メモは何かのアクションにつねに紐づいていなければならない、というものがあります。ただ情報を収集しても確かに無駄で、とくに情報に溢れる現代はメモにむしろ溺れることになります。Zettelkastenもそうですが、PARAも目的をもった情報整理を促す点で良い手法だと考えています。

そういうわけでObsidianには、今のところZettelkastenしているノートしか入れられていません。NotionとObsidianを両刀使いしているのが現状です。Notionの方は正直検索することもあまりないですし、AIを突っ込んで何かしたいと言うことも特段ないので、今後もNotionを使い続けているのではないかと思います。

## Zettelkastenを使っていて良かった点、辛い点

Zettelkastenをしていて良かった点の一つ目は、文章の正確性の担保が楽になる点です。私は本やメディアへの寄稿用の記事をよく書くのですが、その際一番気をつけていて労力がかかるのがファクトチェックです。ファクトチェック用の資料の管理は意外と大変で、正直以前はどこに何をやったのかわからなくなることが多かったです。Zettelkastenを入れてからは、Literature NotesとFleeting Notesという仕組みのおかげで、どこに何の情報があったかを発見しやすくなりました。Permanent Notesを作る瞬間にObsidianの参照機能を使ってこれらのノートを参照させておくだけで良いので、非常に便利です。

自分の言葉で文章を必ず起こすというZettelkastenの思想もまたよい習慣になっていると思います。本を読んで何か概念を得るとして、その概念を読んで自分の言葉に落とさないとどうしても借り物の言葉を話すようになってしまいますし、人に説明する際に苦労するようにもなります。一度以上 [パラフレーズ](https://d.hatena.ne.jp/keyword/%A5%D1%A5%E9%A5%D5%A5%EC%A1%BC%A5%BA) しておくと、人に説明する際楽になることに気づきました。説得力も高まるでしょう。書いているうちに対象物や概念の理解が深まることも結構ありますし、理解が深まるとより深い洞察を得られることもあります。そうした意味で、 [パラフレーズ](https://d.hatena.ne.jp/keyword/%A5%D1%A5%E9%A5%D5%A5%EC%A1%BC%A5%BA) して思考を残しておくZettelkastenは、思考力の強化にも大いに貢献してくれていると思います。

逆に大変な点ですが、やはりZettelkastenを利用しながらあれこれ情報収集をすると、常に思考することを求められる（気がしてしまう）ので、どうしてもカロリーが高まるなと感じます。多分その必要はまったくないのですが、何か考えをきれいにまとめなくてはならないのではという強迫観念に駆られる感じもします。具体的にはPermanent Notesまで作らなくてはならないのでは、と思ってしまいます。が、おすすめはFleeting Notesを気軽にとることです。Fleeting Notesを孤児にしなければよいので、タグ付けするでも十分です。ただ大切になるのは、気楽に残した考えを上手に検索できることでしょうか。Obsidianは検索性は悪くないのでよい選択肢だと思っています。

Zettelkastenには「目的のないメモ」置き場がありませんが、これは辛いというか「用途違い」と言えるかもしれません。たとえば、日々の単なる備忘録やプライベートで買いたいもののリストアップなどは、Zettelkastenに置くには向きません（というか、置く必要がありません）。用途違いなので、 [ディレクト](https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8) リを分けるか、私の例のようにもはや割り切ってNotionで管理してしまう、みたいな手もあると思います。

## おすすめの書籍

Zettelkastenについてはとりあえず一冊読みました。下記です。

[![TAKE NOTES!――メモで、あなただけのアウトプットが自然にできるようになる](https://m.media-amazon.com/images/I/41vxR6oJXsL._SL500_.jpg "TAKE NOTES!――メモで、あなただけのアウトプットが自然にできるようになる")](https://www.amazon.co.jp/dp/B09HZ38SFZ?tag=helloyuki-22&linkCode=ogi&th=1&psc=1)

[TAKE NOTES!――メモで、あなただけのアウトプットが自然にできるようになる](https://www.amazon.co.jp/dp/B09HZ38SFZ?tag=helloyuki-22&linkCode=ogi&th=1&psc=1)

- 作者:[ズンク・アーレンス](https://d.hatena.ne.jp/keyword/%A5%BA%A5%F3%A5%AF%A1%A6%A5%A2%A1%BC%A5%EC%A5%F3%A5%B9)
- [日経BP](https://d.hatena.ne.jp/keyword/%C6%FC%B7%D0BP)
[Amazon](https://www.amazon.co.jp/dp/B09HZ38SFZ?tag=helloyuki-22&linkCode=ogi&th=1&psc=1)

途中で紹介したPARAという手法は、下記の書籍で勉強しました。

[![ＳＥＣＯＮＤ　ＢＲＡＩＮ（セカンドブレイン）　時間に追われない「知的生産術」](https://m.media-amazon.com/images/I/41SPbynaKvL._SL500_.jpg "ＳＥＣＯＮＤ　ＢＲＡＩＮ（セカンドブレイン）　時間に追われない「知的生産術」")](https://www.amazon.co.jp/dp/B0BSWZ9PZL?tag=helloyuki-22&linkCode=ogi&th=1&psc=1)

[ＳＥＣＯＮＤ　ＢＲＡＩＮ（セカンドブレイン）　時間に追われない「知的生産術」](https://www.amazon.co.jp/dp/B0BSWZ9PZL?tag=helloyuki-22&linkCode=ogi&th=1&psc=1)

- 作者:[ティアゴ・フォーテ](https://d.hatena.ne.jp/keyword/%A5%C6%A5%A3%A5%A2%A5%B4%A1%A6%A5%D5%A5%A9%A1%BC%A5%C6)
- [東洋経済新報社](https://d.hatena.ne.jp/keyword/%C5%EC%CD%CE%B7%D0%BA%D1%BF%B7%CA%F3%BC%D2)
[Amazon](https://www.amazon.co.jp/dp/B0BSWZ9PZL?tag=helloyuki-22&linkCode=ogi&th=1&psc=1)

## まとめ

ObsidianとZettelkastenの組み合わせが注目を集め始めているようなので書いてみました。やってて思いますがまあまあ大変なので、用途や人を選ぶと思います。が、自身の思考を深めるという一点においてはかなり有用な手段だと思っています。
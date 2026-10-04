---
title: テーブルの created at にサービスのドメインロジックを持たせない - めもるが
source: https://scrapbox.io/uvb-76/%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%AE_created_at_%E3%81%AB%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E3%81%AE%E3%83%89%E3%83%A1%E3%82%A4%E3%83%B3%E3%83%AD%E3%82%B8%E3%83%83%E3%82%AF%E3%82%92%E6%8C%81%E3%81%9F%E3%81%9B%E3%81%AA%E3%81%84
author:
published:
created: 2026-09-29
description:
tags:
  - clippings
updated: 2026-09-29T03:48
---
テーブルの created at にサービスのドメインロジックを持たせない

**前提**

ActiveRecord の仕組みを用いてデータベースの migration を行っている。

**主張**

ActiveRecordでDBのマイグレーションファイルを作ると

```
t.timestamps
```
がデフォルトで指定されている。これをmigrateするとタイムスタンプとして
```
created_at
```
```
updated_at
```
がカラムについてくる。

これらのカラムはレコードを作成した際や更新を行った際に記録されるメタテーブルとしてのみ利用したい。それ以上のことを行うと辛くなるので避けたほうが良い。

具体的に言うと、アプリケーション内で

```
created_at
```
を where や sort の条件として利用することは避けたい。

[https://railsguides.jp/active\_record\_migrations.html](https://railsguides.jp/active_record_migrations.html)

**なぜか**

created\_at の意味が変わる

```
created_at
```
というカラム名は「レコードが作成された日時」という意味を持っているが、それは「レコードが記録されたイベントが発生した日時ではない」いう考えを持っている。

例えば月間に発生した件数集計するときの条件としては「そのイベントが行われた期間」を条件とするのが自然で「レコードが作成された期間」を条件とするのは違和感があると思う。

仮に前の月に発生したデータの不整合を、レコードを1件追加して直そうとする。

created\_at を集計条件としていた場合、追加するレコードの created\_at を前月にしてinsertする必要があるのだが、それはなんだか違和感がありそう。

**どうする**

何らかのアクションが行われた日付を取りたいのであれば別途そのためのカラムをtimestampで持つべきである。

注文(order)が配送された日時を記録したいのであれば

```
shipped_at
```

(雑な例だが)

```
order
```
has\_one
```
shipment
```
のようにイベントテーブルを作ってそのうえで配送された時刻
```
shipped_at
```
を作るイメージ

**「じゃあtimestampいらないテーブルも出てくるじゃん」**

はい、そうだと思います。 ActiveRecord がデフォルトで timestamp 付与してくるのでそのままにしているんだと思う。

**「必要になってからカラムを増やすといいよ」**

はい。それもそうだと思います。 ですが、開発時にそれを判断できるメンバーがいつも開発フローの場にいるとは限らないので、created\_at を条件にして集計ロジックを書く人がでてくる...なので、最初からカラムを作ってあげた方が将来開発するメンバーが迷わないんじゃないかあという気持ちがあったりします。

AIエージェントクンに「created\_at, updated\_at を条件にすることがあったらカラムをマイグレーションしろ」ってみんなお願いするかなあ。

**他の方々の意見**

[https://songmu.jp/riji/entry/2019-10-21-created\_at-update\_at.html](https://songmu.jp/riji/entry/2019-10-21-created_at-update_at.html)

以前拝見したことはあったけど、ほとんど同じ事書いたな...:pray:

[https://x.com/sinsoku\_listy/status/2098002950165844047?s=20](https://x.com/sinsoku_listy/status/2098002950165844047?s=20)

神速さんのこのpostから派生した意見

[https://neko314.hatenablog.com/entry/2026/09/12/231212](https://neko314.hatenablog.com/entry/2026/09/12/231212)

とても丁寧な主張〜

[https://hanachin.hateblo.jp/entry/2026/09/11/234206](https://hanachin.hateblo.jp/entry/2026/09/11/234206)

こちらは逆の意見の方。必要になってから考える。この考えがあってもいいと思う〜。

**いいわけ**

これは業務で扱っているサービスが既存のdbの上にRailsを被せている歴史を持っているのでそういう考えに至るのかもしれない

created\_at, updated\_at の実装は [ActiveRecord::Timestamp](https://scrapbox.io/uvb-76/ActiveRecord::Timestamp) にあるっぽい
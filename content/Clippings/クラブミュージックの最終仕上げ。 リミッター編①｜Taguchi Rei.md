---
title: "クラブミュージックの最終仕上げ。 リミッター編①｜Taguchi Rei"
source: "https://note.com/reitaguchi/n/n7ba67c3f200e"
author:
  - "[[Taguchi Rei]]"
published: 2026-08-30
created: 2026-08-30
description: "「True Peak」とは  リミッター編①では、意外と知られていないTrue Peakについて紹介します。FabFilter Pro-L 2を例に、「True Peak Limiting」は画面下部（緑色で点灯）で、On / Offできますが、なんとなくOnにしている人もいるのではないでしょうか。  True Peakとは再生時に現れるピークのことです。True Peak Limitingをオンにしないと、メーター上（サンプルピークを）0dB以下に抑えていても、True Peak（Inter Sample Peak）は0dBを超えることがあり、再生や変換の場面で歪みが出る恐れがある、"
tags:
  - "clippings"
---
### 「True Peak」とは

リミッター編①では、意外と知られていないTrue Peakについて紹介します。FabFilter Pro-L 2を例に、「True Peak Limiting」は画面下部（緑色で点灯）で、On / Offできますが、なんとなくOnにしている人もいるのではないでしょうか。

![画像](https://assets.st-note.com/img/1788056259-J5sy4wYjGoivtCa60Fl1WDgx.jpg?width=1200)

True Peakとは再生時に現れるピークのことです。True Peak Limitingをオンにしないと、メーター上（サンプルピークを）0dB以下に抑えていても、True Peak（ [Inter Sample Peak](https://www.g200kg.com/jp/docs/dic/intersamplepeak.html) ）は0dBを超えることがあり、再生や変換の場面で歪みが出る恐れがある、というものです。True Peakを抑える機能が無いリミッターも多く存在します。（例：Waves L2、Ableton 11以前のLimiterなど）

配信ではmp3やAACへの変換で波形が変わり、ピークが元より高くなることがあります。だから配信サービス側は余裕を見ていて、Spotifyは−1dBTP以下を公式に推奨しています。Appleも最低1dBのヘッドルームを推奨していますが、こちらはTrue Peakではなくサンプルピーク基準です。デジタルデータが0dBFSにあると再生時のオーバーサンプリングでクリップが起きうる、という理由づけで、True Peakという言葉は出てきません。

**しかし、本記事の内容としては、クラブミュージックではTrue Peakは無視しても良いんじゃない？、という内容です。**

> [**Electronic Music Mastering: LUFS and True Peak Explained**](https://www.dancemusicnw.com/electronic-music-mastering-lufs-and-true-peak-explained/)  
> ダンスミュージック系メディアのDance Music Northwestが、Beatportで購入した100曲のWAVのTrue Peakを計測した記事です。有名レーベルの曲でもTrue Peakが0dBを0.01〜5.5dB上回る例が多数見つかったという内容。しかも超過が2dB以内の曲の多くは音も良かったと書かれていて、筆者は「これは意図的なのか事故なのか」と問いを投げています。

【注意】全ての音楽に当てはまらない話です。配信時にクリップが問題となる可能性のある音楽では、全く参考にならないです。特に日本人の国民性とは相性が悪い、バイブスや音楽性の話です。

---

### 「True Peak Limiting」ありなしでの音の違い

True Peak（以下、TP）リミッターは、サンプル間のピークまで天井に数えるので、同じ設定でも余計にリダクションが入ります。トランジェントの強い素材は特に差が出ます

クラブミュージックは、ドラムのトランジェントと高い音圧を両立させたい音楽です。余計なリダクションは、そのどちらにとっても不利です。OnとOffを是非聴き比べてみてください。一目瞭然です。

![画像](https://assets.st-note.com/img/1788059193-FoXf8BlyWLjwY2sJHpqvtabD.jpg?width=1200)

Ozone 12 Maximizer。 TPは画面左のOutput Levelの右。

![画像](https://assets.st-note.com/img/1788058980-6IlUTAZvYzXKCWn8h0Smpyek.jpg?width=1200)

Waves L4。 TPは右上のDitherの左。

---

### 実測。市販音源のTrue Peakを見てみる。

計測はiZotope RX11のWaveform Statisticsで行っています。

**① Joy Orbison x Overmono - Bromley (2019)  
**Mastered by Matt Colton（Metropolis Studios）

![](https://www.youtube.com/watch?v=ThaTKXGXHKE)

データ：16bit / 44.1kHz  
True Peak Level: L +1.31dB / R +1.27dB  
Sample Peak Level: -0.3dB

![画像](https://assets.st-note.com/img/1788060838-JoOlaFZ1cyd4xtvE3gmrbziB.png?width=1200)

**② Dave - Raindance (ft. Tems) (2025)  
**Mastered by Joe LaPorta（Sterling Sound）

![](https://www.youtube.com/watch?v=SOJpE1KMUbo)

データ：24bit / 48kHz  
True Peak Level: L +0.29dB / R +0.17dB  
Sample Peak Level: -0.3dB

![画像](https://assets.st-note.com/img/1788061358-PHLye6oThOqQWMnfp7wG5zJb.png?width=1200)

**③ Summer Walker - Body (2019)**  
Mastered by Nicolas de Porcel（Million Dollar Snare）

![](https://www.youtube.com/watch?v=PY9HTSBXE8s)

データ：16bit / 44.1kHz  
True Peak Level: L +0.30dB / R +0.16dB  
Sample Peak Level: 0dB

![画像](https://assets.st-note.com/img/1788061546-i6dPFM7xscXghT4WIpaJorzt.png?width=1200)

**④ Coco Bryce - Blacklist (2020)**  
Mastered by Beau Thomas (Ten Eight Seven Mastering)

![](https://www.youtube.com/watch?v=oudq3kAm6bo)

データ：16bit / 44.1kHz  
True Peak Level: L +0.98dB / R +1.55dB  
Sample Peak Level: 0dB

![画像](https://assets.st-note.com/img/1788062250-z8yEKRT4W3PHXgCGkcYe2nwI.png?width=1200)

**⑤ Two Shell - Ghosts (2022)**  
Mastered by Heba Kadry (Heba Kadry Mastering)

![](https://www.youtube.com/watch?v=5P07yWoBaVo)

データ：16bit / 44.1kHz  
True Peak Level: L +0.53dB / R +0.48dB  
Sample Peak Level: 0dB

![画像](https://assets.st-note.com/img/1788062686-Re6L8mA7G2DPI4BVq0sO3iQl.png?width=1200)

---

### 再生経路を考えても、True Peakはキリがない。

最終ゴールをDJがプレイすると考えます。BandcampやBeatportで曲を買うとき、フォーマットを選ぶのはユーザー側です。WAVで買う人もいれば、mp3で買う人もいます。mp3への変換で初めて出るピークもあれば、購入したWAVやAIFFを24bit / 48kHzから16bit / 44.1kHzを変換する人も。エンコードするサイトのシステムも、ソフトも様々です。

現場での再生経路はさらに変数が多いです。CDJ本体でDAしてアナログ出力されるのか、CDJからDJMへはデジタルで送られて、ミキサーでDAされるのか、こちらも様々です。CDJのマスターテンポのオンオフでも変わるようです。TPについてはあまり気にしなくても良いと考えられます。

---

### Ceilingの適切な値は？

答えは、実測した通りで、特に決まりはありません。

参考資料を貼っておくので各自参考にしてください。

> iZotopeのOzone公式マニュアル、MaximizerにあるCeilingの項です。ディザを使うときはCeilingを−0.3dBに、あとでmp3やAACに変換する音源なら−0.6〜−0.8dBまで下げることを推奨しています。将来のクリップを防ぐため、という理由づけです。  
>   
> iZotope Maximizer Manual  
> [https://downloads.izotope.com/docs/ozone9/en/maximizer/index.html](https://downloads.izotope.com/docs/ozone9/en/maximizer/index.html)

> Wavesの公式記事です。CD向けならリミッターのCeilingは−0.2か−0.1dBFSに設定し、0.0は不可(インターサンプルピークが通り抜けて、民生機の再生で歪みうるため)。慎重にいきたい場合や音圧の高い音楽では、−0.3dBFSまで下げれば最悪のケースでも避けられる、という内容です。  
>   
> 4 Essential Mastering Levels Tips  
> [https://www.waves.com/essential-mastering-levels-tips](https://www.waves.com/essential-mastering-levels-tips)

8
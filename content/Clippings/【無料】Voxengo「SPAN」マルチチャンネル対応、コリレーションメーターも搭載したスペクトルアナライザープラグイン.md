---
title: "【無料】Voxengo「SPAN」マルチチャンネル対応、コリレーションメーターも搭載したスペクトルアナライザープラグイン"
source: "https://dtm-sale.com/37663/"
author:
  - "[[ダウハック]]"
published: 2023-12-10
created: 2026-09-10
description: "VoxengoボクセンゴのSPANは、マルチチャンネル対応＆コリレーションメーター搭載、RMS・トゥルーピーク"
tags:
  - "clippings"
---
[フリープラグイン](https://dtm-sale.com/ct/free-plugin/)

本サイトにはPRリンク/PR記事が含まれています

![span特定の周波数帯域のみをモニタリング](https://dtm-sale.com/wp-content/uploads/2023/12/span-ui9.jpg)

span特定の周波数帯域のみをモニタリング

Voxengo ボクセンゴ のSPANは、マルチチャンネル対応＆コリレーションメーター搭載、RMS・トゥルーピークレベル・クリッピングの検出も行う高機能なスペクトルアナライザープラグインです。

「SPAN」は、データ解析手法の一つ「フーリエ変換」（時間領域データを周波数領域へと変換するアルゴリズム）が使用されています。

目次

## Voxengo「SPAN」の特徴

![](https://www.youtube.com/watch?v=iZrWMv02tlA)

主な特徴

- 高速フーリエ変換スペクトラムアナライザープラグイン
- ステレオ、ミッドサイド、サラウンドのアナライズが可能
- RMS・トゥルーピークレベル・クリッピングの検出が可能
- ステレオ相関メーター（Correlation Meter）を搭載
- EBU R128（ラウドネス基準値）、K-system（Bob Katzが提唱したレベル管理手法）などのメーターを搭載

「SPAN」は、複数のメーターリングモードを搭載していることや、視認性の良さなど無料で使えるとは思えないほど高機能なスペクトルアナライザープラグインです。  
基本的にはマスターチャンネルで使用します。

上位有料版の「SPAN PLUS」は更に高機能ですが、無料版のSPANだけでも十分なアナライズが行なえます。

### Block Sizeの設定

![span Block Sizeの設定](https://dtm-sale.com/wp-content/uploads/2023/12/span-ui8.jpg)

span Block Sizeの設定

メーターを滑らかに見やすく表示させるには、歯車マークをクリックし、Block Sizeから設定を変更します。  
数値が大きいほど詳細な表示ができますが、リアルタイム表示の滑らかさが減少します。

2048～8192に設定するのがおすすめですが、マシンへの負荷を鑑みながら設定しましょう。

### マルチチャンネル分析対応

![span](https://dtm-sale.com/wp-content/uploads/2023/12/span-ui6.jpg)

無料版のSPANでは、2つのチャンネル及びチャンネルグループのスペクトルを表示できます。

MiD/Side、またはステレオMonoの2チャンネルなどを色分けして表示する際に使用することが多いと思います。  
また、それぞれのチャンネルは好みの色で色分けして表示させられるのも便利です。

### EBU R128・K-systemメーターを搭載

EBU R128は、EBU（ヨーロッパ放送連合）のラウドネスとオーディオ信号の最大レベルの推奨規格事項のことを指します。

SPANではこの規格を用いたアナライズが可能。

また、Bob Katz（アメリカのマスタリングエンジニア）が提唱したレベル管理手法「K-system」を用いたメーターが搭載されています。

### 周波数帯域別のモニタリングが可能

![span特定の周波数帯域のみをモニタリング](https://dtm-sale.com/wp-content/uploads/2023/12/span-ui9.jpg)

span特定の周波数帯域のみをモニタリング

Command＋マウスドラッグ（Winの場合Ctrl＋マウスドラッグ）することで、周波数帯域ごとの音をモニタリングできます。

細かな調整を行う際に現状を把握しやすいので便利です。

また、マウスホイールを使って周波数帯域幅の調整もできます。

### M/S（Mid/Side）の周波数分布の把握が可能

![span](https://dtm-sale.com/wp-content/uploads/2023/12/span-ui2.jpg)

SPANでは、MIDとSIDEに分けて周波数分布を表示させることができます。

設定方法は、「Routing」の横にある▼マークをクリックすると設定選択ウインドウが表示されます。  
この中からMID・SIDE STEREOを選択するだけです。

MIDとSIDEの周波数分布は、個別に表示させることはもちろん、同時に表示させることもできます。

### プリセット

![span](https://dtm-sale.com/wp-content/uploads/2023/12/span-ui4.jpg)

プリセットが多く用意されており、使用目的に応じてアナライザーモードを切り替えて使用することが可能です。

### Correlation Meter（コリレーションメーター）を搭載

![spanリコレーションメター](https://dtm-sale.com/wp-content/uploads/2023/12/span-ui10.jpg)

spanリコレーションメター

UIの右下には、Correlation Meterが配置されています。

Correlation Meterはステレオの相関を表示してくれるもので、位相について把握できます。

・相関値が中央から左側に位置している場合  
位相が外れた状態で、モノラルでモニタリングすると信号が打ち消し合う可能性があります。

・相関値が中央から右側に位置している場合  
ステレオとモノラルのそれぞれの信号に互換性がある状態です。モノラルに近い状態で聞こえると言い換えてもいいかもしれません。

・相関値が中央に位置している場合  
広範囲なステレオ効果を得られている状態といえます。

### ユーザーインターフェースのカラー変更

![span スキンカラー設定](https://dtm-sale.com/wp-content/uploads/2023/12/span-ui7.jpg)

span スキンカラー設定

UIのスキンカラーを変更することができます。

目に優しい落ち着いた色から派手な色まで用意されているので好みのものを選択してみてください。

この他の機能・特徴

- スロープの変更・調整
- ユーザーインターフェースの画面サイズの変更が可能
- トゥルーピークおよびクリッピング表示
- MID/SIDE分析
- 内部チャンネルルーティング
- チャンネルグループ化
- UNDO・REDO機能
- A/B比較
- コンテキストヒントメッセージの表示
- 全サンプルレートに対応

## 仕様

プラグインフォーマット：AAX / AudioUnit / VST  
MAC：Apple Silicon native / Monterey / Ventura / Sonomaに対応  
WINDOWS：32bit/64ビットのWindows XP/Vista/7/8/10 11に対応

## ダウンロードページ

Voxengo「SPAN」は無料でありながら高機能なスペクトルアナライザープラグインです。

「SPAN」だけでなく、他のアナライザを併用して更に詳細なアナライズをするのもお勧めですが、「SPAN」だけでも多くの情報を把握できます。

使い方も簡単なので初めてのスペクトルアナライザーとしてもおすすめです。

＞＞ [ダウンロードページはこちらから](https://www.voxengo.com/product/span/)

また、Voxengo「SPAN」には、更に高機能な上位版「SPAN Plus」もあります。

「SPAN Plus」は、MeldaProductionの「MMultiAnalyzer」と比較されることも多いですが、どちらも優秀なアナライザープラグインです。

細かいポイントを挙げるとそれぞれに違いがありますが、使い心地やUIで選んでもよいレベルです。

自分に合うものを選ぶには、やはりデモ版を使用してみるのがおすすめです。

＞＞ [Voxengo「SPAN Plus」](https://www.voxengo.com/product/spanplus/)

＞＞ [MeldaProduction「MMultiAnalyzer」](https://www.pluginboutique.com/product/2-Effects/25-Spectral-Analysis/841-MMultiAnalyzer?a_aid=64d266126cdc1)

あわせて読みたい

![](https://dtm-sale.com/wp-content/uploads/2024/05/MMultiAnalyzer1200-300x158.jpg)

[【66%OFF】「MMultiAnalyzer」高性能かつ多機能なMeldaProductionのアナライザープラグイン（セール期間…](https://dtm-sale.com/20201/) 【価格】11,330円 (税込) → 66%オフ 3,801円 (税込)

[フリープラグイン](https://dtm-sale.com/ct/free-plugin/)

[Voxengo](https://dtm-sale.com/tag/voxengo/) [アナライザー](https://dtm-sale.com/tag/analyzer/) [スペクトルアナライザー](https://dtm-sale.com/tag/spectral-analyzer/) [フリープラグイン](https://dtm-sale.com/tag/free-plugin/)

- [![](https://dtm-sale.com/wp-content/uploads/2023/12/FeltSeilerFreeUI-300x149.jpg) 【無料】Strezov Sampling「The Felt Seiler - Free Edition」柔らかく穏やかな響きを持ったKONTAKT用フェルトピアノ音源](https://dtm-sale.com/37376/)
- [![](https://dtm-sale.com/wp-content/uploads/2023/12/tutti_free-300x202.jpg) 【無料】SONOKINETEIC「TUTTI Free」オーケストラヒットやグリッサンドなどのTUTIIを収録したKONTAKTI音源](https://dtm-sale.com/37732/)

## この記事を書いた人

Logic / Cubaseユーザー、ハードウェア&プラグインマニア。DAW・音源プラグイン・MIXプラグインなどDTM関連の製品を中心に発信。少しでも安く買えるセール情報も随時収集&発信しています。
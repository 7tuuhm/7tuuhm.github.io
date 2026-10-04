---
title: "DAWの音を配信へ"
source: "https://yoruhinot.github.io/DawAudioStreamer/"
author:
published:
created: 2026-09-10
description: "DAWの音声を、オーディオ設定を変えずにOBSやDiscordの画面共有へ送れます。Windows版とmacOSプレビュー版を配布中。"
tags:
  - "clippings"
---
## 使うパソコンを選ぶ

OSごとに必要なものが違います。該当する側だけ見れば使い始められます。

### Windows

OBSでの配信・録画と、Discordの画面共有に対応します。

OBSDiscord

[**Windows版をダウンロード** v0.4.2・Windows 11・x64](https://github.com/yoruhinot/DawAudioStreamer/releases/download/v0.4.2/DawAudioStreamer-Setup-0.4.2.exe)

インストール時にWindowsの警告が表示される場合があります。

[Windowsの手順へ](#windows-setup)

DAWの音をOBSへ送る用途向けです。Discordには通常不要です。

OBS

[**macOSプレビュー版をダウンロード** v0.4.1-macos-preview.1・macOS 13以降・Apple Silicon](https://github.com/yoruhinot/DawAudioStreamer/releases/download/v0.4.1-macos-preview.1/DawAudioStreamer-0.4.1-macos-preview.1-macOS-AppleSilicon.zip)
- **対応環境** macOS 13以降・Apple Silicon
- **プラグイン** AU／VST3

初回インストール時にmacOSの警告が表示される場合があります。

[macOSの手順へ](#macos)

## セットアップ

OSごとに違うのはインストールだけ。その後は同じ流れで使えます。

1

### OSに合わせてインストール

ここだけOSごとに手順が違います。

Windows 11

#### インストーラーを実行

OBS、Discord、DAWを終了してから、ダウンロードしたインストーラーを実行します。

macOS 13以降

#### Install.commandを実行

ZIPを展開し、「Install.command」をControlキーを押しながらクリックして［開く］を選びます。

対応環境：macOS 13以降・Apple Silicon。

2

### DAWのマスターに挿す

ここからはWindows・macOS共通です。最終的な音が通る場所の最後に「DAS Send」を1個追加します。

#### DAWごとの場所

WindowsはVST3、macOSはAUまたはVST3を使用できます。

コンソールの「メイン」チャンネルにある、インサートの最後へ追加します。

MixConsoleの「Stereo Out」にある、Insertsの最後へ追加します。

MASTERトラックの［FX］を開き、エフェクト列の最後へ追加します。

3

### 使いたい配信先を選ぶ

OBSかDiscord、使いたい方を選びます。選んだ先の手順だけ見れば大丈夫です。

**OBS**

#### 配信・録画

Windows・macOSで同じ3手順。追加ソフトは不要です。

**DISCORD**

#### 画面共有

WindowsではVB-CABLEを使用。macOSでは通常DAS不要です。

#### OBSへ送る3手順

WindowsでもmacOSでも操作は同じです。

1. 1OBSの「ソース」で［＋］を押す
2. 2「DAS Audio（DAW）」を追加
3. 3DAWを再生して音声ミキサーのメーターを確認

追加ソフトは不要です

#### Discordの画面共有へ送る

WindowsではVB-CABLEを、DAS内部の音声経路として使用します。

**macOSではこの設定は不要**

DAWアプリまたは画面全体を共有すれば、macOS標準機能で音声も共有できます。

**先にVB-CABLEをインストール**
1. 1公式ZIPを展開
2. 2「VBCABLE\_Setup\_x64.exe」を管理者として実行
3. 3［Install Driver］を押してWindowsを再起動
[VB-CABLE公式サイト](https://vb-audio.com/Cable/)

1. 4DAS Sendが「準備完了」になることを確認
2. 5DiscordでDAWまたは画面全体を共有

**設定は変えません**

「CABLE Input」をDAWの出力やDiscordのマイクに選ばないでください。DASが内部で使用します。

共有する音だけを選びたい場合

「配信用BUS」を1本作り、配信したいトラックだけを送ります。そのBUSの最後にDAS Sendを1個挿すと、自分だけが聞く音を配信から外せます。

## うまくいかないとき

該当する項目だけ開いて確認してください。

DAS SendがDAWに出てこない

DAWのVST3またはAUプラグインを再スキャンします。インストール後にDAWを再起動していない場合は、一度終了して開き直してください。

OBSにDAS Audioが出てこない

OBSを完全に終了して開き直し、「ソース」の［＋］をもう一度確認してください。

OBSのバージョンが古いと、DAS Audioが表示されない場合があります。表示されない場合は、OBSを最新版に更新して試してください。

Windows版のDiscordがVB-CABLEのまま

VB-CABLEのx64版を管理者として導入し、Windowsを再起動してください。

音が二重に聞こえる

OBSでDAS Audioとデスクトップ音声の両方から、同じDAW音声を取り込んでいないか確認します。DAS Sendも1個だけにしてください。

アンインストールしたい

Windowsは「インストールされているアプリ」から削除します。macOSはDAWとOBSを終了して「Uninstall.command」を実行します。

それでも解決しない

不具合や要望は [X（@yoruhinot）](https://x.com/yoruhinot) または [GitHub Issues](https://github.com/yoruhinot/DawAudioStreamer/issues) へ。使用OS、DAW、OBSのバージョンと症状を添えてください。
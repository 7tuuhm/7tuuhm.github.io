---
title: "MacでKindle本を自動スクリーンショット: AppleScriptによる効率的な方法"
source: "https://qiita.com/YUK_KND/items/5c430a1c3aa61d48d115"
author:
  - "[[YUK_KND]]"
published: 2024-07-13
created: 2026-08-21
description: "こんにちは。今回は、Macで電子書籍（特にKindle本）を自動的にスクリーンショットする方法について解説します。この方法を使えば、効率的に電子書籍の内容を画像として保存できます。 はじめに 電子書籍の内容を簡単に画像として保存したいと思ったことはありませんか？本記事では..."
tags:
  - "clippings"
---
この記事は最終更新日から1年以上が経過しています。[この記事誰得？ 私しか得しないニッチな技術で記事投稿！](https://qiita.com/official-events/d340ce2d6bd2d5b557f5)

[

Qiita Engineer Festa2024年7月17日まで開催中！

](https://qiita.com/official-events/d340ce2d6bd2d5b557f5)

- [Mac](https://qiita.com/tags/mac)
- [AppleScript](https://qiita.com/tags/applescript)
- [Kindle](https://qiita.com/tags/kindle)
- [KindleUnlimited](https://qiita.com/tags/kindleunlimited)

12

投稿日

こんにちは。今回は、Macで電子書籍（特にKindle本）を自動的にスクリーンショットする方法について解説します。この方法を使えば、効率的に電子書籍の内容を画像として保存できます。

## はじめに

電子書籍の内容を簡単に画像として保存したいと思ったことはありませんか？本記事では、MacのAppleScriptを使用してKindleアプリの画面を自動的にスクリーンショットするスクリプトを紹介します。

**注意**: このスクリプトは個人的な利用を前提としています。著作権法を遵守し、適切に使用してください。

## スクリプトの概要

このスクリプトは以下の機能を持っています：

1. OS バージョンの確認（Mojave以降が必要）
2. スクリーンショットの範囲と保存先の設定
3. スクリーンショット回数の指定
4. ページ方向（右開き/左開き）の指定
5. 自動スクリーンショットとページ送り

## スクリプトコード

```applescript
-- グローバル変数の宣言と初期設定
global waitSeconds
global appName
-- スクリーンショットのタイミングと対象アプリケーションの設定
set waitSeconds to 0.5 -- スクリーンショットのタイミングが合わない場合は秒数を調整する
set appName to "Kindle" -- ここにはアプリケーション名を記述する

-- メイン処理をtryブロックで囲み、エラーハンドリングを追加
try
    -- OSバージョン確認
    set OSVer to do shell script "sw_vers -productVersion | cut -d '.' -f 1-2"
    set nOSVer to OSVer as number
    if nOSVer < 10.14 then
        display alert "このスクリプトはMojave以降のOSでしか使えません。"
        error number -128 -- スクリプトを終了する
    end if

    -- 範囲指定と保存先設定の確認
    display dialog "範囲指定と保存先設定は済んでいますか？" & return & "" & return & "範囲指定:点線エリアを調整します。" & return & "保存先:画面右下のオプションから保存先を指定します。" & return & "最後に右下のパネルをxで閉じるか「取り込む」を押します。" buttons {"はい", "設定", "終了"} default button "はい" with title "選択範囲設定確認 Step0"
    set confirmPattern to button returned of result
    if confirmPattern = "終了" then error number -128 -- スクリプトを終了する
    if confirmPattern = "設定" then openScreenshotTool() -- スクリーンショットツールを開く

    -- スクリーンショット回数の指定
    display dialog "スクショする回数を入力してください。" default answer "5" with title "自動スクショ Step1"
    set screenShotCount to (text returned of result) as integer -- 入力された回数を整数に変換

    -- ページ方向の指定
    display dialog "右開き？左開き？" buttons {"左", "右"} default button "右" with title "自動スクショ Step2"
    set pageDirection to button returned of result -- ページ方向を保存

    -- スクリーンショット取得とページ送りのループ
    repeat with i from 1 to screenShotCount
        takeScreenshot() -- スクリーンショットを撮る
        turnPage(pageDirection) -- ページをめくる
    end repeat

    -- 処理終了の通知
    display alert "処理が終了しました。"
    
on error errMsg number errNum
    -- エラーハンドリング
    display alert "エラーが発生しました: " & errMsg & " (" & errNum & ")"
end try

-- スクリーンショットを取得するハンドラ
on takeScreenshot()
    try
        tell application appName to activate
        tell application "System Events"
            delay waitSeconds
            key code 23 using {command down, shift down} -- スクリーンショットツールを起動
            delay waitSeconds
            key code 76 -- スクリーンショットを撮る
        end tell
    on error errMsg number errNum
        display alert "スクリーンショット取得中にエラーが発生しました: " & errMsg & " (" & errNum & ")"
        error errNum -- エラーを再スロー
    end try
end takeScreenshot

-- ページをめくるハンドラ
on turnPage(direction)
    try
        tell application appName to activate
        tell application "System Events"
            delay waitSeconds
            if direction = "右" then
                keystroke (ASCII character 29) -- ページ右送り
            else
                keystroke (ASCII character 28) -- ページ左送り
            end if
            delay waitSeconds
        end tell
    on error errMsg number errNum
        display alert "ページ送り中にエラーが発生しました: " & errMsg & " (" & errNum & ")"
        error errNum -- エラーを再スロー
    end try
end turnPage

-- スクリーンショットツールを起動するハンドラ
on openScreenshotTool()
    try
        tell application appName to activate
        tell application "System Events"
            key code 23 using {command down, shift down} -- スクリーンショットツールを起動
        end tell
    on error errMsg number errNum
        display alert "スクリーンショットツールの起動中にエラーが発生しました: " & errMsg & " (" & errNum & ")"
        error errNum -- エラーを再スロー
    end try
end openScreenshotTool
```

## スクリプトの使用方法

1. AppleScriptエディタを開きます。
2. 上記のコードをコピーしてエディタに貼り付けます。
3. スクリプトを保存します。
4. 実行ボタンをクリックしてスクリプトを開始します。
5. 画面の指示に従って設定を行います。

## カスタマイズのヒント

1. `waitSeconds` 変数: スクリーンショットの間隔を調整できます。お使いのMacの性能に応じて調整してください。
2. `appName` 変数: Kindle以外の電子書籍アプリを使用する場合は、ここでアプリ名を変更できます。
3. ページ送りのキーコード: 電子書籍アプリによっては、ページ送りのキーが異なる場合があります。必要に応じて `turnPage` ハンドラ内のキーコードを調整してください。

## 注意点

- このスクリプトは個人的な利用を前提としています。著作権法を遵守し、適切に使用してください。
- 大量のスクリーンショットを取得すると、ストレージ容量を圧迫する可能性があります。必要に応じて保存先を調整してください。
- スクリーンショットの品質は、ディスプレイの解像度や電子書籍アプリの表示設定に依存します。最適な結果を得るために、これらの設定を調整することをお勧めします。

## まとめ

このAppleScriptを使用することで、Macで電子書籍の内容を効率的にスクリーンショットとして保存できます。個人的な学習や研究のために活用してください。スクリプトは自由に改変して使用して頂いてもOKです！  
より使いやすくなれば是非コメントください！

[0](#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

新規登録して、もっと便利にQiitaを使ってみよう

1. あなたにマッチした記事をお届けします
2. 便利な情報をあとで効率的に読み返せます
3. ダークテーマを利用できます
[ログインすると使える機能について](https://help.qiita.com/ja/articles/qiita-login-user)
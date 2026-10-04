---
title: 12GB VRAMで125Bモデルを動かす「Strata」の概要｜npaka
source: https://note.com/npaka/n/n4a1185074686?sub_rt=share_sb
author:
  - "[[npaka]]"
published: 2026-10-02
created: 2026-10-02
description: 12GB VRAMで125Bモデルを動かす「Strata」の概要をまとめました。   1. はじめに  ローカルLLMでは、GPUのVRAM容量が大きな制約になります。特に100Bを超えるモデルは、量子化しても一般的なGPUのVRAMだけで動かすのは困難です。  そこで登場したのが、AI推論エンジン「Strata」です。  Strataは、GPUとCPUで計算を分担し、VRAM・RAM・SSDを活用して、大規模なMoEモデルを一般的なPCで動かすことを狙ったオープンソースの推論エンジンです。  現在は「Qwen3.8-Flash-Next」を中心に、その派生モデルや量子化モデルに対応し
tags:
  - clippings
updated: 2026-10-02T15:29
---
12GB VRAMで125Bモデルを動かす「Strata」の概要をまとめました。

## 1\. はじめに

ローカルLLMでは、GPUのVRAM容量が大きな制約になります。特に100Bを超えるモデルは、量子化しても一般的なGPUのVRAMだけで動かすのは困難です。

そこで登場したのが、AI推論エンジン「 **Strata** 」です。

Strataは、 **GPUとCPUで計算を分担し、VRAM・RAM・SSDを活用して、大規模なMoEモデルを一般的なPCで動かす** ことを狙ったオープンソースの推論エンジンです。

現在は「 **Qwen3.8-Flash-Next** 」を中心に、その派生モデルや量子化モデルに対応しており、125Bパラメータ級のモデルをRTX 5070 12GBのようなGPUでも実行できます。

<iframe allowfullscreen="" allow="autoplay *; encrypted-media *; ch-prefers-color-scheme *" src="https://iframely.net/LT8LjpJ3?v=1&amp;app=1"></iframe>

## 2\. Strataの仕組み

### 2-1. 125Bモデルを一般的なGPUで動かす

Strataの大きな特徴は、 **モデル全体をVRAMへ読み込まない** ことです。

Qwen3.8-Flash-Nextは125B規模のMoEモデルですが、推論時にすべてのExpertをGPUへ置く必要はありません。  
Strataでは、 **VRAM・RAM・SSDを役割に応じて使い分けます** 。GPUにはAttentionやRouter、KV Cache、頻繁に使われるExpertを配置し、それ以外のExpertはRAMに保持してCPUで計算します。必要に応じて、一部をPCIe経由でGPUへ転送することもあります。  
また、SSDには大容量のLookup Tableを置き、必要な部分だけを読み込みます。

このように、 **GPUだけでなくCPU・RAM・SSDも活用することで、VRAMに収まらない巨大モデルを一般的なPCで動かします** 。

### 2-2. ExpertをVRAMにキャッシュ

MoEモデルでは、入力ごとに必要なExpertだけが選択されます。  
Strataはこの性質を利用して、 **よく利用されるExpertをVRAMへキャッシュ** します。

キャッシュされたExpertはGPUで高速に処理し、それ以外はCPU計算やPCIe転送を利用します。Expertキャッシュは利用状況に応じて適応するため、12〜16GB程度のVRAMでも巨大なMoEモデルを扱えます。

### 2-3. 投機的デコードで高速化

高速化には「 **Speculative Decoding（投機的デコード）** 」も利用します。  
Qwen3.8-Flash-Next自身が持つMTP（Multi-Token Prediction）層で複数トークンを先に予測し、本体モデルでまとめて検証します。

Strataの公式説明では、この投機的デコードによって、通常の生成に比べて **約1.6〜1.8倍の高速化** が得られるとされています。  
コード編集などでは、過去のコンテキストを利用した予測も組み合わせます。

## 3\. Strataの推論性能

公開ベンチマークでは、 **RTX 5070 12GB + Ryzen 5 7600 + RAM 64GB** でQwen3.8-Flash-Nextを実行しています。

短い会話での生成速度は、

> **・Q2\_0：約93 tokens/s  
> ・IQ2\_XS：約79 tokens/s  
> ・IQ3\_XXS：約62 tokens/s  
> ・IQ3\_S：約53 tokens/s**

とされています。

128Kコンテキストでも、それぞれ約74 / 63 / 49 / 46 tokens/sです。  
量子化によって速度と品質が変わり、Strataではバランスの取れた「 **IQ2\_XS** 」が推奨されています。  
ただし、これらは開発側の測定値で、実際の速度はGPU、CPU、RAM、PCIe帯域、コンテキスト長などによって変わります。

## 4\. 必要なPC環境

Strataでは、おおむね次の環境が推奨されています。

> **・GPU：NVIDIA RTX 20 / 30 / 40 / 50シリーズ  
> ・VRAM：12GB以上推奨（8GBでも動作可能）  
> ・RAM：64GB推奨  
> ・CPU：AVX2対応x86-64  
> ・ストレージ：NVMe SSD推奨  
> ・OS：Windows 10 / 11、Linux**

8GB VRAMでも動作可能とされていますが、速度面では不利になります。  
また、モデル関連ファイルの保存には70〜120GB程度のストレージが必要になる場合があります。

## 5\. Strataの使い方

WindowsではGitHubからStrataを取得して、「 **START-HERE.bat** 」を実行します。Linuxでは、「**./setup.sh** 」を利用します。

初回セットアップでは、モデル、量子化、コンテキスト長、画像入力などを選択すると、必要な環境が準備されます。

起動後は、

```javascript
http://127.0.0.1:8080
```

からWeb UIを利用できます。

Chatに加えて、GPU使用率、VRAM、CPU、RAM、PCIe通信量、生成速度などを確認するMonitorも用意されています。

## 6\. OpenAI / Anthropic互換API

Strataは、 **ローカルAIサーバー** としても利用できます。

OpenAI互換APIは、

```javascript
http://127.0.0.1:8080/v1
```

から利用できます。

PythonのOpenAI SDKから接続する場合は次のようになります。

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://127.0.0.1:8080/v1",
    api_key="none",
)

response = client.chat.completions.create(
    model="strata",
    messages=[
        {"role": "user", "content": "Pythonでテトリスを作って"}
    ],
)

print(response.choices[0].message.content)
```

Anthropic Messages互換APIにも対応しているため、外部ツールやコーディングエージェントのローカルLLMとして使うこともできます。

## 7\. まとめ

Strataは、 **GPU・CPU・RAM・SSDを組み合わせて巨大なMoEモデルを動かすAI推論エンジン** です。

特に重要なのは、

> **・MoEのExpertをGPUとCPUに分散**  
> **・頻繁に使うExpertをVRAMへキャッシュ**  
> **・RAMにExpertを保持し、SSDに大容量Lookup Tableを配置**  
> **・投機的デコードによる高速化**  
> **・OpenAI / Anthropic互換API**

という仕組みです。

これまでのローカルLLMでは「VRAMに収まるモデルを選ぶ」のが基本でした。Strataはその制約を緩和し、 **PC全体のリソースを使うことで100B超のモデルを一般的なGPUでも実行する** というアプローチを採っています。

64GB以上のRAMと12〜24GB程度のNVIDIA GPUを持つPCで、大規模モデルをローカル実行するための興味深い選択肢です。

## 【おまけ】 RAM 32GBでも使える？

通常のQwen3.8-Flash-Nextは、Q2\_0でも約34GBのRAMを使うため、 **32GB RAMでは基本的に不足します** 。

そこでStrataでは、32GB RAM向けに「 **Qwen3.8-Flash-Next Coder** 」も用意されています。Coder版はExpertを約半分に減らし、コード生成やツール利用に重要なExpertを残したモデルで、Shard 1は約29.6GBです。

そのため目安として、

> **・32〜48GB RAM：Coder版**  
> **・48GB以上：通常版のQ2\_0 / IQ2\_XS**

という使い分けができます。

ただし、Coder版は用途を絞っているため、 **一般的な文章生成や幅広い知識タスクでは通常版より弱くなります** 。

[**ソフトウェアデザイン 2026年9月号** *www.amazon.co.jp*](https://www.amazon.co.jp/dp/B0H96XB7X6?tag=npaka-22&linkCode=ogi&th=1&psc=1&language=ja_JP)

[Amazon.co.jpで購入する](https://www.amazon.co.jp/dp/B0H96XB7X6?tag=npaka-22&linkCode=ogi&th=1&psc=1&language=ja_JP)73
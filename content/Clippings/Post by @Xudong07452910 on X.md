---
title: "Post by @Xudong07452910 on X"
source: "https://x.com/Xudong07452910/status/2099323420614041783"
author:
  - "[[@Xudong07452910]]"
published: 2026-09-14
created: 2026-09-17
description: "以前、私は李博傑について紹介しました@bojie_li著者の『AIエージェントの徹底的な理解』。最近、著者は同書の姉妹編である『AIインフラの徹底的な理解：定量的分析とシステム設計』をオープンソース化しました。特に、モデルの展開、推論、またはエージェントシステムに既に取り組んでい"
tags:
  - "clippings"
---
以前、私は李博傑について紹介しました@bojie\_li著者の『AIエージェントの徹底的な理解』。最近、著者は同書の姉妹編である『AIインフラの徹底的な理解：定量的分析とシステム設計』をオープンソース化しました。特に、モデルの展開、推論、またはエージェントシステムに既に取り組んでいる方には、こちらを強くお勧めします。本書は「どのような推論最適化手法があるか」という話で終わるのではなく、より根本的な疑問から始まります。モデルが遅いのはなぜか、そしてボトルネックは正確にはどこにあるのか？重みとKVキャッシュはメモリに収まるか、帯域幅は十分か、計算にはどれくらいの時間がかかるか、マルチGPU通信が最初に詰まるか…著者のアプローチは、まずこれらの制約を明確に計算し、次にシステムをどのように設計すべきかを決定することです。そこから、モデルアーキテクチャ、アクセラレータ、オペレータ、相互接続、推論最適化、分散推論、トレーニングシステムに至るまでを網羅しています。また、106の実験と計算が付属しており、多くの図はPythonで直接再計算できます。私はこのAIインフラの学習方法が本当に気に入っています。多くのシステム概念は個々には理解できますが、実際にエンジニアリングを行う際には、「このソリューションが物理的に実現可能かどうか」を判断できることがより重要になります。『AIエージェントの徹底理解』を既にお読みになった方は、本書はモデルをさらに深く掘り下げるのに最適です。プロジェクト：GitHub｜

[github.com GitHub - bojieli/ai-infra-book: 《深入理解 AI インフラ：量化分析と系统设计》（李博杰 著）开源书稿：从ハードアイテム约束和モデル架构出発行，量化推导 LLM 感覚与训练系...](https://t.co/SzFEEC4Apc)

---

## Comments

> **chengyongru @chengyongru** · [2026-09-14](https://x.com/chengyongru/status/2099327879029157933)
> 
> I'm still skeptical about the value of books that come out of this kind of vibe.
> 
> > **Xudong Han @Xudong07452910** · [2026-09-14](https://x.com/Xudong07452910/status/2099328540680531981)
> > 
> > It still depends on whether the author is hardcore enough.

> **安叫兽|Bird🕊️ 🔶 BNB @ajs6888** · [2026-09-14](https://x.com/ajs6888/status/2099349051552698815)
> 
> 做部署和推理的确实可以翻翻，量化这块一直挺容易踩坑的

> **Crio Songo @shuizhuyu** · [2026-09-14](https://x.com/shuizhuyu/status/2099414823499141592)
> 
> Just recently I've been messing around with model deployment and inference, and I'm right at a bottleneck. I'll go clone it right away and work through the calculations step by step—this kind of hands-on, practical resource is exactly what I need.

> **Gregor @bygregorr** · [2026-09-14](https://x.com/bygregorr/status/2099359675628052590)
> 
> the 'companion piece' framing might undersell it. infra constraints hit me first when i was building agent flows, before the architecture even made sense. felt more like the prerequisite than the follow-up.
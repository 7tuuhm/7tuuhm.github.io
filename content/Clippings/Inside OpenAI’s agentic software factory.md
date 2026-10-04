---
title: "Inside OpenAI’s agentic software factory"
source: "https://newsletter.pragmaticengineer.com/p/openai-software-factory"
author:
  - "[[Gergely Orosz]]"
published: 2026-09-16
created: 2026-09-17
description: "A deepdive into how Codex has “taken over” OpenAI, how the frontier lab builds its agentic software factory, and the engineering challenges of one billion users. Details from inside OpenAI"
tags:
  - "clippings"
---
[ディープダイブ](https://newsletter.pragmaticengineer.com/s/deepdives/?utm_source=substack&utm_medium=menu)

### CodexがOpenAIを「乗っ取った」経緯、この最先端研究所がエージェント型ソフトウェアファクトリーをどのように構築しているか、そして10億人のユーザーを抱えるエンジニアリング上の課題について深く掘り下げます。OpenAI内部からの詳細情報。

トークン予算に制限がない環境で仕事をするのは稀ですが、OpenAIでは、エンジニア、研究者、財務担当者、マーケティング担当者など、全員がそうしています。先日、世界有数の最先端研究所の一つであるOpenAIを訪れ、現在のOpenAIの運営状況や、ソフトウェアエンジニアリングという職業の将来像を探ってきました。

[私が昨年OpenAIの本社を訪れて](https://newsletter.pragmaticengineer.com/p/san-francisco-is-back) 以来、多くのことが変わりました 。わずか1年で、Codexは「あれば便利」なツールから、同社のほぼすべての業務の基盤となるツールへと進化しました。

さらに詳しく知るため、私はそこで7人のエンジニアリングリーダーとエンジニアに話を聞きました。Venkat Venkataramani氏（応用インフラ担当エンジニアリングVP）、Sulman Choudhry氏（ChatGPTエンジニアリング責任者）、Andrew Ambrosino氏（デスクトップ担当リーダー）、Joe Gershenson氏（コアエージェントチーム担当リーダー）、Akshay Nathan氏（生産性担当エンジニアリングリーダー）、Ahmed Ibrahim氏（Codex担当エンジニア）、そしてSteve Coffey氏（レスポンスAPI担当エンジニア）です。 *ご協力いただいた皆様に感謝いたします！*

本日は、以下の内容を取り上げます。

1. **CodexがOpenAIを席巻。** わずか数ヶ月のうちに、OpenAIのエンジニア以外のほぼ全員が、上層部からの指示なしにCodexとChatGPT Workに移行した。
2. **IDEとプルリクエストの終焉。Codex** の利用が急増し始めた1月以降、IDEの利用は減少している。プルリクエストとコードレビューのあり方を見直す必要がある。
3. **OpenAIのエージェント型ソフトウェアファクトリー。OpenAI** は、自動化されたエージェント型フィードバックループを複数備えた「ソフトウェアファクトリー」を構築しました。例えば、Perf Factoryは本番環境を監視し、Codexエージェントを起動してパフォーマンスの問題を自動的に修正します。
4. **エンジニアリングツールと手法はどのように変化しているか。** 手作業で構築された内部ツールは徐々にCodexに置き換えられつつあり、デバッグには専用ツールよりもCodexがますます好まれるようになっている。ソフトウェア開発工場では、効率性を確保することが極めて重要だ。
5. **10億人のユーザーのためのエンジニアリング：OpenAIはどのようにインフラを拡張しているのか。** まずは購入し、後から社内に取り込む。また、地理的なインフラ配置、キャパシティプランニングの戦略と課題についても解説する。
6. **OpenAIのAPIの信頼性とパフォーマンスを向上させる** 。CPUがボトルネックになりつつあるため、意図的にデプロイ速度を落とし、負荷に関する課題を解決する。
7. **ソフトウェアエンジニアリングの仕事はどのように変化しているのか。** エンジニアリングの専門分野は消滅しつつあり、判断力と主体性がより重要視され、これまで「不可能」とされていた書き換えや移行も、たった1人か2人のエンジニアで成功させることができるようになっている。

*始める前に、スケジュールのお知らせです。今週はニューヨークに滞在し、LDX3カンファレンスに参加したり、市内のいくつかのスタートアップ企業やテクノロジー企業を訪問したりする予定です。そのため、木曜日の「The Pulse」はお休みとなります。来週からは通常通り配信いたします！*

*この記事の末尾は、一部のメールクライアントでは途中で途切れて表示される場合があります。 [記事全文はオンラインでご覧ください。](https://newsletter.pragmaticengineer.com/p/openai-software-factory)*

## 1\. CodexがOpenAIを買収

同社の本社を訪問して最も印象に残ったのは、Codex（そして最近ではCodex *とChatGPT Work）が1月頃から本社内* *のあらゆる業務* を掌握し始めたということだ 。デスクトップ部門の責任者であるアンドリュー・アンブロシーノ氏は私にこう語った。

> 「ここ数ヶ月の大きなテーマは、あらゆるものがコーディングエージェントになったということです。目に見えるコードが出力であろうとなかろうと、エージェントが成果物を書き出すのです。」
> 
> こう考えてみてください。あなたの生活のすべてはソフトウェアによって成り立っています。コンピューターには強力なツール（エージェント）が搭載されており、ループ処理や推論、コード記述といった能力があれば、あらゆることが可能になります。

以下のトークン使用状況グラフは、この急激な普及拡大を示しています。

![](https://substackcdn.com/image/fetch/$s_!UFwx!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F117eaa31-8c91-41c3-9646-e06523263ca7_1846x1054.png)

OpenAIにおける部門別Codex利用状況（2025年8月以降）。出典： OpenAI

4か月間で、財務、採用、法務などの非エンジニアリング部門におけるCodexの利用率は約0%から90%に上昇しました。現在では、OpenAIのほぼすべての従業員が毎週CodexとChatGPT Workを利用しています。一体何が起こったのでしょうか？

OpenAIは2月にMac版のCodexアプリを、3月にWindows版をリリースし、7月にはChatGPT Work（Codexハーネスを利用）をリリースしました。その後、エンジニア以外の社員はすべてのワークフローをCodex、そしてWorkへと移行しました。ただし、注意点として、OpenAIの内部版Codexは *[、RampのInspect AIエージェント](https://newsletter.pragmaticengineer.com/p/why-ramp-built-inspect)* *と同様に* 、 *ほぼすべてのOpenAIシステムに接続されているため、外部版よりもはるかに高度な機能を* *備えています。*

興味深いのは、Codexアプリが非エンジニアリングユーザーにとって使いづらい（非エンジニアリングユーザーにとって不向きな）時期にもかかわらず、OpenAIが非エンジニアリングチームで40%近くまで採用されたことです。2月から4月にかけて、Codexアプリは画面上にコードを表示していましたが、それでもエンジニアリング以外の非技術系の同僚は、調査やプレゼンテーション、ドキュメント、スプレッドシートの作成、あるいは豊富な出力を生成するタスクなど、複雑な作業を実行できたため、このアプリを使用してい *まし* た。現在では、そうした人々はCodexアプリを非常に頻繁に利用しています。

**より複雑な作業を長時間続けられるようになったことが、普及の原動力となった。OpenAI** はCodexに/goal設定を追加し、エージェントに目標を設定して、それが完了するまで作業を継続できるようにした。4月から5月にかけて、利用率は60%から90%に急増した。Andrewは、ハーネスの長時間実行タスクの処理能力の向上が、この増加の一因だと考えている。

> 「一番大きな変化は、人々がスレッドを以前よりもずっと長く使うようになったことです。この長期使用は画期的な進歩と言えるでしょう。人々がスレッドに費やす時間の長さには驚かされます。何日もかかることもあるのです！彼らは目標を設定し、モデルを回し続けることが多いのです。」
> 
> Codexが長時間実行されるタスクに優れているため、人間が並行して行うタスクの数が減るようです。これは、長時間実行されるエージェントが他のエージェントを起動して別のタスクを実行させることが多く、人間が管理しなければならない範囲が縮小するためです。

**Codexチームは、「認知度の低さ」も急速な普及の要因の一つだと考えている。** 生産性チームのエンジニアリングリーダーであるアクシャイ・ネイサン氏は私にこう語った。

> 「長い間、私たちは『能力過剰』の状態でした。モデル自体は能力を備えていましたが、製品がそれを十分に引き出せていませんでした。今は、認知度のギャップが見られます。Codexを使ってSlackを監視したり、Airtableを更新したり、オンボーディング資料を作成したりできることに気づいた人もいますが、多くの人は依然として1つのタスクにのみ使用し、その後、チームメイトの口コミで他の用途を発見しています。」
> 
> しかし、Codexには表面上は見えなくても、まだまだできることがたくさんあるのです。」

**役割別およびチーム別のプラグインが作成され、配布されます。** 導入を加速させたもう一つの要因は、各グループが役割別の便利なワークフローをプラグインとして配布し始めたことです。アンドリューは、汎用的なコーディングエージェントを提供するだけでは不十分な理由を次のように説明しました。

> 「何でもできる製品を開発する場合、チームはそれを自分たちのニーズに合わせてカスタマイズする方法を必要とします。単に空の箱を渡すだけではダメなのです。スキルやプラグインによって、チームはエージェントを自分たちの業務に合わせて調整できます。場合によっては、エージェントがスキルと併用できるブラウザのような、新しいアプリ機能が必要になることもあります。しかし、同じ構成要素ですでに多くの異なる役割をカバーできています。」

**ChatGPT Workのエンジニアリングチームには、各分野の専門家が組み込まれています。** 一部の分野では、モデルが開発者よりも「賢く」なっているため、開発者はそうした領域で「好み」を反映させることができません。そのため、各分野の専門家がエンジニアリングチームに加わるようになりました。これは、ChatGPT Workがエンジニアリング以外の多くの分野で利用されていることによる成果の一つです。エンジニアリングチームに組み込まれた専門家が、優れたスライドデッキ、スプレッドシート、ビジネスレポートの見本などについて開発者にアドバイスするのです。

*もちろん、エンジニアリングチームにドメインエキスパートを配置することは、高品質な製品を開発するための数十年前から確立されたベストプラクティスです。そして、このことは数年ごとに様々な文脈で再発見されているようです。*

**OpenAIはCodexとWorkに完全に依存しています。** そのため、たとえ軽微な障害が発生した場合でも、同僚からの内部メッセージによって、自動アラートと同時、あるいはそれよりも早くCodexとWorkのチームに通知されます。

基本的に、作業はCodexとWorkを通じて行われ、それ以外はほとんど *何も行われません* 。 *外部から見ると、この単一の共有システムへの依存は特に目を引きます。2年前には、AIエージェントは存在せず、高度なAIオートコンプリート機能のみが存在していたのです。*

## 2\. IDEとプルリクエストの終焉

昨年末、CodexチームはCodexデスクトップアプリをリリースするかどうかで意見が分かれていた。アンドリューはその時の迷いをこう振り返る。

> 「2025年12月時点では、Codexアプリをリリースするかどうか確信が持てませんでした。Codex CLIはターミナルとして提供していましたが、世の中には大規模で機能豊富なIDEが数多く存在します。ターミナルとIDEの中間に位置する開発ツールに需要はあるのだろうか？という疑問がありました。私の頭の中では、iPadのように『場違い』な存在になってしまう未来像が浮かんでいました。」
> 
> 多くの人がiPadを購入しても、結局使わない。より小型で持ち運びやすいスマートフォン *（この比喩で言えばCLIに相当するもの）* を使うか、機能豊富なノートパソコン *（IDEに相当するもの）を使うかのどちらかだ。*
> 
> また、11月にAntigravityがVS Codeのフォークとしてリリースされたことも忘れてはなりません。このことで、CodexアプリのためにVS Codeもフォークすべきだったのではないかという思いが強まりました。しかし、私たちはその誘惑を退け、AIエージェントの性能が向上するにつれてIDEの重要性は低下していくという直感を信じました。

実際、1月以降、IDEの利用率は低下しており、OpenAIの戦略は正しかったと言えるだろう。しかし、Codexアプリは少し *ずつ* IDEに近づいてきている。例えば、アプリ内でファイルを編集できる機能は6月にリリースされた。

**CI/CDシステムでは、負荷が大幅に増加しています。Codex** による生産性向上を示す兆候の一つとして、OpenAIの開発インフラシステムを通過するコード量の増加が挙げられます。コードの作成とプッシュが増えることで、新たなスケーリングの課題が生じており、チームは現在その解決に全力で取り組んでいます。Applied Infraのエンジニアリング担当副社長であるVenkat Venkataramani氏は次のように述べています。

> 「エンジニア一人当たりのプルリクエスト（PR）の数は、まるでホッケースティックのように *（非常に速いペースで）* 増加しています。ビルド、テスト、デプロイのパイプラインのあらゆる部分で、負荷が劇的に増加しています。」
> 
> これは、一部のシステムにおける負荷が約10倍に増加することを意味します。ほとんどの企業では、このような成長は2～3年かけて起こるでしょう。しかし、OpenAIでは、わずか6ヶ月ほどでそれが実現します。
> 
> このような加速化はあらゆる場所でボトルネックを露呈させる。バージョン管理システムは、これまで以上に多くのコードが記述され、プッシュされるのを処理しなければならず、CI/CDシステムはそれに合わせて拡張する必要があり、本番リリースプロセスははるかに高い変更率を吸収しなければならない。
> 
> **毎月、私たちは新たなインフラ拡張の課題に直面しながら目を覚まします。** 次の成長段階に向けて十分なキャパシティを確保できたと思った矢先に、モデルは新たな機能の波を生み出し、システム内のどこかに新たなボトルネックが発生するのです。

**このような状況において、プルリクエストとコードレビューは再考されています。** これらはソフトウェアエンジニアリングにおける「コアプリミティブ」ですが、このレベルの開発加速は、それらを再考する絶好の機会です。再び、Venkat氏の言葉を引用します。

> 「こうした開発の加速化の中で、私たちが自問すべき問いは、これまで当然のこととしてきた多くの事柄をどのように再考するかということです。例えば、CI（継続的インテグレーション）とCD（継続的デプロイメント）のプロセスをどのように再考すべきでしょうか？この世界において、可観測性とは何を意味するのでしょうか？そして、人々はプルリクエストとどのようにやり取りすべきでしょうか？」
> 
> 私に言わせれば、今日のコードレビューのやり方はますます意味をなさなくなってきており、プルリクエストについても同じことが言える。
> 
> 現在、エージェントによるコードレビューが普及しており、さまざまな視点からコード変更を検証するようになっています。従来、クラウドインフラストラクチャエンジニアとセキュリティエンジニアがすべてのコード変更をレビューすることは非現実的でしたが、エージェントの導入により、それが可能になります。
> 
> **エージェントを活用することで、コードのデプロイ方法も再考できます。** 私たちは、コードの変更であれ、フィーチャーフラグによる変更であれ、変更を本番環境まで完全に「サポート」するエージェントを構築しています。このエージェントは、関連する監視グラフを監視するだけでなく、重要なシグナルを監視するための独自のダッシュボードを構築することもできます。このようなエージェントによる監視によって、より多くのコード変更が本番環境にデプロイされるようになっています。

**ネイティブモバイルアプリのデプロイは、ますます深刻なボトルネックとなっています。** プルリクエストが10倍になると、バックエンドやWebへのデプロイが困難になり、より多くのインフラストラクチャが必要になります。そして、CI/CDシステムを再構築し、十分な処理能力を確保した後、本番環境にデプロイされるプルリクエストの数も増えることになります。

これは、iOSおよびAndroidネイティブアプリのアップデート配信時に発生する問題です。なぜなら、すべてのアプリアップデートはAppleとGoogleの手動承認プロセスを経る必要があり、その完了には数時間から数日かかるからです。

ChatGPTのエンジニアリング責任者であるスルマン・チョードリー氏に話を聞いたところ、アプリレビューのボトルネックがイテレーションのスピードにどのような影響を与えているかを説明してくれた。スルマン氏は以前Facebookに勤務しており、ソーシャルメディア企業がモバイルリリースのスピードアップに取り組んでいたことを覚えている。

> 「2010年代、Facebookはネイティブモバイルコードをより迅速にリリースする方法において、非常に重要なブレークスルーを達成しました。App Storeのリリース頻度は、月1回から隔週、そして週1回へと増加しました。同時に、実験機能とフィーチャーフラグのおかげで、チームはリリース準備が整う前にコードをリリースし、準備が整った時点でリモートで機能を有効化できるようになりました。」
> 
> そのモデルはモバイル業界に飛躍的なスピードをもたらした。
> 
> Codexの時代において、私たちはこの問題の次の段階に直面していると思います。コード生成は劇的に速くなっていますが、そのコードをネイティブモバイル端末でユーザーに届けることはそうではありません。特にCodexのようにモバイルファーストの利用が主流の環境では、このギャップは既に私たちにとってもユーザーにとっても大きな問題となっています。
> 
> I expect the pressure here to increase quickly. If software can be written in minutes, waiting days or weeks to get it onto a phone starts to look increasingly absurd.
> 
> We should be aiming for a world where shipping code on native mobile is as fast as shipping on the web. Getting there will probably require some creative rethinking of what we ship, when we ship it, and what can be activated remotely. Today, we’re nowhere close.”

*There’s some irony in how shipping a native iOS or Android app has the exact same challenges today as in 2008, when the App Store was launched. In 18 years, not much has changed! Apple still does not officially allow apps to bypass the App Store review process to ship meaningful experience changes.*

## 3\. OpenAI’s agentic software factory

The idea of a “software factory” is similar to a physical factory where robots and humans produce autos together. In the software context, it is AI agents and humans producing software. Some manufacturing sites are fully automated “dark factories” where illumination isn’t needed because there are no humans. Could the same fully automated process emerge in software engineering? At OpenAI today, there’s a “software factory” running and it’s all built around Codex.

Here’s how the “traditional” software development pipeline used to look, compared to what OpenAI’s agentic infra pipeline looks like today, as described by VP of Engineering, Applied Infra, Venkat Venkataramani:

![](https://substackcdn.com/image/fetch/$s_!Eiw9!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F1e73721e-e4d8-473f-a5a1-b6211c14f6ca_2048x1762.png)

OpenAI’s “agentic software factory”

The pipeline:

**1\. A human builder defines the desired outcome.** A software engineer or product manager specifies the problem and desired outcome. Judgment, prioritization, and taste are becoming more important for this phase. Interestingly, Venkat told me that engineers at OpenAI are becoming more like product managers than traditional systems engineers.

**2\. Codex gathers context.** OpenAI has moved all its documentation inside of the source code, which makes it easier for agents to understand more of the code. Codex also has access to:

- Git repositories and GitHub
- Slack and Notion
- Internal data sources: Databricks, Datadog, internal logs, etc
- Internal Codex skills – some of which are maintained by OpenAI’s Codex implementation itself!

Codex is so “plugged” into OpenAI that new engineers are directed to ask Codex *any* questions they have during onboarding because it has a surprising amount of context.

**3\. Codex implements code changes.** This part is trivial enough: Codex gets to work and makes a series of code changes until it reaches its goal, and then verifies that the software works as it should.

**4\. Build & test, then CI.** The agent builds the code, runs the tests, fixes the code when it breaks the test, and then creates a pull request. This pull request triggers the continuous integration (CI) server to run and execute a more thorough suite of linters and tests. The agent babysits the PR until it’s “green”, fixing any CI failures and automatically updating the PR.

**New: a “perf harness”:** the agent also uses a perf harness to send problematic PRs to the Synthetics A/B framework for evaluating performance implications. As mentioned above, the load upon CI systems has increased greatly in the past six months.

**5\. Agentic code review.** Instead of using one generic AI code reviewer, OpenAI spins off multiple agents, each with a “domain specialist” configuration. Venkat told me they see this as equivalent to having a human domain expert from each relevant infrastructure team review every change.

*Note from Gergely: I was skeptical about the claim that an agent that’s told to be a cloud infra specialist would produce a different review from a generic agent. However, all Codex agents have full access to OpenAI’s code and docs, so this “cloud infra expert” agent likely has gathered a lot of context about cloud infra setup and best practices, meaning it should provide highly targeted feedback. The important thing is how these “domain specialist” agents are set up, the context they have access to, and how they focus only on their own domain to make best use of their limited context window.*

**Code changes are classified by risk.** High-risk changes can be sent through stricter processes; for example, they might invoke more AI code reviews, or mandate that a human reviews it after the AI agents finish. Low-risk changes follow an easier path; areas of the codebase can opt in to an agent that will auto-approve low risk PRs, removing human acceptance as a bottleneck and improving velocity.

A neat thing about risk assessment is that OpenAI can automate when additional compliance input is needed: either automated (via another agent) or human review. With the quantity of PRs being produced, it simply wouldn’t be possible for humans to review all code without assistance.

Just like with CI, the coding agent babysits the comments and updates the PR to fix issues surfaced.

**6\. Agentic deploy.** After a human approves a change to go to production, it is assigned its own agent with an instruction that could be summarized as:

“Handhold this change until it is safely and fully rolled out into production.”

Agents handhold both the code changes and the changes behind feature flags. For example, in the case of a change behind the feature flag, the agent will:

- Read the codebase and figure out where the feature flag lives
- Understand what the change does
- Decide which signals indicate success and failure
- **独自の監視ダッシュボードを構築して使用できるのは** 、 *非常に印象的な改善点であり、私にとっては新しい機能です。*
- 関連する生産シグナルとそのダッシュボードを監視します。

OpenAIの長期的な目標は、ほぼ自律的にデプロイできるエージェントの形で、「変更ごとに自律的に動作するSRE（サイト信頼性エンジニア）」のようなものを実現することです。

**7\. 生産状況の観察。** 生産システムを追跡するツール：

- エージェントが前のステップで生成したダッシュボード
- OpenAIの内部オブザーバビリティスタックには、ログ、メトリクス、トレース、および広範囲のイベントデータを生成するカスタムツールが多数含まれています。

私が以前訪問したCodex以前からOpenAIで大きく変わった点の1つは、当時はエンジニアがサービスを監視するためのダッシュボードを作成していたのに対し、現在はエージェントが変更ごとのデプロイメントという粒度で監視を行っていることです。

**8\. 本番環境の監視結果を開発にフィードバックする。OpenAI** の「Perf Factory」は、エージェントを使用してアラートやダッシュボードを精査し、重複するシグナルを排除し、実際のレイテンシ低下を特定し、その根本原因を突き止め、修正案を提案する。これにより、継続的なコード変更によって発生するパフォーマンスの問題を早期に発見し、ワークフローをデプロイメントから継続的な改善へと拡張できる。

**9\. 障害への対応。Sevbot** はOpenAIの社内インシデント対応エージェントであり、当然ながらCodexをベースに構築されています。インシデントが検出されると、ボットが「起動」します。その動作は以下のとおりです。

- 事件に関する背景情報を収集する
- 可能な緩和策を特定する（ただし、実際に緩和策を実行することはない）。
- 開発者からの質問に答えます（Slackチャンネルの一部です）
- エンジニアは、特定の緩和策を適用するように指示することができる。

OpenAIの目標は、Sevbotが障害発生時に自律的に対応できるレベルに到達することです。Sevbotが「日常的な」障害を自律的に処理し、人間は職場に戻ってからその対応を確認することで、勤務時間外に人間が起こされることがない状態を目指しています。しかし現状では、同社ではオンコール体制は過去のものとはなっていません。

## 4\. エンジニアリングツールと手法の変化

当然のことながら、Codexは社内ツールの構築方法を大きく変え、デバッグなどの標準的なエンジニアリング手法にも影響を与えています。OpenAIの関係者との会話から私が得た情報は以下のとおりです。

## この記事は有料購読者限定です

[既に有料会員ですか？ **ログインしてください**](https://substack.com/sign-in?redirect=%2Fp%2Fopenai-software-factory&for_pub=pragmaticengineer&change_user=false)
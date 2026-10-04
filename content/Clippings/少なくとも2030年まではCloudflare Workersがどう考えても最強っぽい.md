---
title: "少なくとも2030年まではCloudflare Workersがどう考えても最強っぽい"
source: "https://qiita.com/ikuosaito1989/items/ba0fcd9f7d67313f4203"
author:
  - "[[ikuosaito1989]]"
published: 2026-09-05
created: 2026-09-17
description: "Cloudflare WorkersのヤバさをAIと熱く語ってたら最強であることの理由が無限に出てきたのでこの記事で発散します。AI記事が苦手だったらスキップしてください GCPのCloud Runが出たとき、どう考えても最強でしょって思ってたけど色んな視点から見て..."
tags:
  - "clippings"
---
Cloudflare WorkersのヤバさをAIと熱く語ってたら最強であることの理由が無限に出てきたのでこの記事で発散します。AI記事が苦手だったらスキップしてください

GCPのCloud Runが出たとき、どう考えても最強でしょって思ってたけど色んな視点から見ても

**どう考えてもCloudflare Workers最強でしょ**

って思ってきた。少なくとも2030年あたりまではこれ以上のものは出ないと確信してる

もちろんAWS、GCP、Azureにはそれぞれ強いところがある。

巨大なVM、GPU、エンタープライズ向けの機能、サービス数まで全部含めてCloudflareが一番って話ではない。

ただ、

**Webサービスを作るためのプラットフォーム**

って目線で見ると、Cloudflare Workersはかなり抜けてると思う。

しかも自分が一番すごいと思ってるのは、単純に「安い」ことじゃない。

**ここまで安くできるってことは、それだけスケーリングの余白が大きい。**

ここがWorkersを見てて一番面白い。

単純な価格競争だったら、AWSやGoogleが値下げすれば終わる。

でもWorkersは、V8 isolateという実行モデル、Cloudflareが元々持ってた世界規模のネットワーク、その上にD1、R2、Durable Objects、Queues、Workflows、Containers、Workers AI、Emailまで積み上がってる。

なので個人的には、

**少なくともここ数年、AWS・GCP・AzureがWebアプリ向けのプラットフォームとしてこれを総合的に上回るのは無理だと思ってる。**

---

## サーバーを小さくしたんじゃなくて、サーバーという単位を消しにいった

Cloudflare Workersで一番でかいのがこれ。

クラウドって今まで、

```text
物理サーバー
↓
VM
↓
コンテナ
↓
プロセス
↓
アプリ
```

みたいに、どんどん実行単位を小さくしてきた。

EC2みたいなVMがあって、コンテナが出てきて、そのあとLambdaみたいなFunctionが出てきた。

でも根本的には、

**アプリを動かすためのプロセスやRuntimeを用意する**

って考え方が残ってる。

Workersはそこをさらに削ってる。

Cloudflare WorkersはV8 isolateを実行単位にしていて、1つのWorkers Runtime上で数百〜数千のisolateを動かせる。

Cloudflare公式にも、

- 1つのRuntimeで数百〜数千のisolateを動かせる
- isolateはコンテナやVM上のNode.js processより約100倍高速に起動できる
- 起動時のメモリ消費も一桁少ない

と書かれてる。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__67f637672d7d998cb92db0bd2468e70f" frameborder="0" height="112"></iframe>

イメージとしては、

```text
従来

Container A
 └ Node Runtime
     └ App A

Container B
 └ Node Runtime
     └ App B

Container C
 └ Node Runtime
     └ App C
```

じゃなくて、

```text
Cloudflare Workers

共通V8 Runtime
 ├ isolate A
 ├ isolate B
 ├ isolate C
 ├ isolate D
 ├ isolate E
 └ ...
```

みたいな感じ。

「超小さいコンテナ」みたいなものと考えるとイメージしやすいんだけど、実際にはもう少し根本的。

**コンテナを極限まで小さくしたんじゃなくて、コンテナやプロセスをアプリごとに持つ前提をやめてる。**

これ普通にすごすぎる。

Workersってサーバーレスというより、

**プロセスレスに近い**

と思ってる。

---

## 「安い」がすごいんじゃない。安くできるだけの余白があるのがすごい

Cloudflare Workersはとにかく安い。

ただ、自分は安さ自体が一番の強みだとは思ってない。

むしろ、

**なんでここまで安くできるんだ？**

ってところの方が重要だと思ってる。

Workersは1つのV8 Runtime上で大量のisolateを動かせる。

つまり、1アプリごとにNode.jsプロセスやコンテナを立てる構成と比べて、同じ物理サーバーにかなり高密度で処理を載せられる。

ざっくり、

```text
従来

1台のサーバー
├ Node process
├ Node process
├ Node process
└ ...
```

に対して、

```text
Workers

1つのV8 Runtime
├ isolate
├ isolate
├ isolate
├ isolate
├ isolate
└ ...
```

みたいな感じ。

同じ1台のサーバーでも、載せられる処理量が多ければ多いほど1リクエストあたりの原価は下がる。

だから、

**安い = 値下げしてる**

ではなく、

**安い = まだスケーリングできる余白がかなりある**

と見てる。

こっちの方がWorkersの強さを表してると思う。

Cloudflareが明日値上げしたとしても、Workersの構造的な強さは変わらない。

逆にAWSがLambdaを半額にしたからといって、Workersの実行モデルの優位性が消えるわけでもない。

価格は結果。

根本は、

**同じ物理リソースからどれだけ処理能力を引き出せるか。**

だと思う。

---

## V8やハードウェアが進化するほど、さらに余白が増える

この構造だと将来的な伸び代も大きい。

V8が高速化する。

Workers Runtimeが改善される。

スケジューラの効率が上がる。

CPUが速くなる。

メモリ容量が増える。

そうなると、

```text
1台で処理できるWorkerが増える
↓
実行密度が上がる
↓
余白が増える
↓
原価が下がる
↓
さらに低価格で提供できる
```

って方向になる。

しかもWorkersは共通Runtimeだから、Cloudflare側がRuntimeを改善すれば、その恩恵が大量のWorkerにそのまま乗る。

各ユーザーがNodeのRuntimeを更新したり、コンテナを作り直したりする必要もない。

これかなり強い。

今安いだけじゃなくて、

**構造的にまだ伸ばせる余地がある。**

個人的にはWorkersの怖さってこっちだと思ってる。

---

## しかもCloudflareは世界中にサーバーを置く理由を最初から持ってた

ここがAWS・GCP・Azureとかなり違う。

CloudflareはWorkersをやるために世界中へサーバーを置き始めた会社じゃない。

元々、

- CDN
- DNS
- WAF
- DDoS対策
- Zero Trust

なんかを提供してた会社。

つまりWorkersがなくても、世界中にサーバーを置く必要がある。

Cloudflareは現在、348都市にネットワークを展開していて、

**Every service runs in every data center**

と掲げてる。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__3270a1bdeabe69dfe4422114dd4f1381" frameborder="0" height="29"></iframe>

この資産の上に、

```text
Workers
D1
R2
Durable Objects
Queues
Workflows
Workers AI
Email
Containers
```

を積み上げてる。

これがかなり効いてると思う。

AWSやGCPが同じことをやろうとしたら、世界中に超分散したCompute基盤を作る必要がある。

Cloudflareは違う。

**もうある。**

しかもCDNやWAFで元々使ってる資産。

その上にV8 isolateを高密度で載せられる。

だからCloudflareの強みって、

**V8 isolateだけじゃない。**

```text
V8 isolate
×
世界規模のネットワーク
×
CDN事業ですでに必要な設備
×
長年のエッジ運用知見
```

これ全部が噛み合ってる。

この組み合わせはさすがに強すぎる。

---

## AWS・GCP・Azureは技術的には真似できても、同じコスパでは勝てないと思う

AWSなら技術力も資金もある。

GoogleなんてV8を作ってる会社だし、Microsoftも当然強い。

だから、

「isolate型のサービスを作れるか？」

って話なら普通に作れると思う。

でも、

**Cloudflareと同じコスパで出せるか？**

ってなると話が変わる。

AWSにはすでに、

```text
EC2
ECS
Fargate
Lambda
RDS
S3
SQS
...
```

っていう巨大なサービス群がある。

その中で、Cloudflare Workersみたいな

- 超軽量
- グローバル
- リージョンをあまり意識しない
- かなり安い

Computeを本気で出す。

しかもCloudflareと同じレベルの価格まで下げる。

既存サービスとの競合もかなり大きいと思う。

それなら、

**技術的に作れないというより、同じ原価構造・料金体系でやるメリットが薄い。**

って方が近い。

Cloudflareは逆。

世界規模のエッジネットワークという既存資産をComputeへ転用すること自体が強みになる。

なのでCloudflareの優位性は、

**技術 × 資産 × 原価構造**

のセットだと思ってる。

ここまで揃ってると、AWS・GCP・Azureが数年でひっくり返すのはさすがに難しい。

---

## Cloudflareは「クラウドをエッジ化」したんじゃなくて「エッジをクラウド化」してる

AWSなんかは、

```text
巨大なデータセンター
↓
リージョン
↓
クラウド
↓
エッジへ拡張
```

って方向で進化してきた。

Cloudflareは逆。

```text
世界中のCDN
↓
Workers
↓
D1
R2
Durable Objects
Queues
Workflows
Containers
AI
Email
```

元々あったエッジネットワークを、そのまま汎用クラウドに進化させてる。

この違いはかなり大きい。

AWSが、

**データセンターをクラウド化した会社**

だとすると、

Cloudflareは、

**インターネットそのものをクラウド化しようとしてる会社**

って感じがする。

やってることすごすぎる。

---

## 「リージョン」という概念をかなり薄くした

AWSを使うと、

```text
東京リージョン？
大阪？
us-east-1？
```

みたいな話が普通に出てくる。

Workersの場合、基本的にはCloudflareのグローバルネットワーク上で実行される。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__ac60ddb8ca43cac614235a2b8d500a13" frameborder="0" height="29"></iframe>

開発者側は、

```text
東京にサーバー置く
アメリカにも置く
EUにも置く
```

じゃなくて、

```text
wrangler deploy
```

で終わる。

もちろんDBや外部サービスとの位置関係は考える必要がある。

でもComputeに関して、

**どこにデプロイするか**

って判断をかなりPlatform側に持っていった。

昔ならマルチリージョン構成ってかなり高度なインフラ設計だった。

それがデフォルト。

やばない？

---

## サーバーレスにはStateを持ちづらいっていうデカい弱点がある

ここまでWorkersを推してるけど、V8 isolate型にも当然弱点がある。

基本的にはステートレス。

```text
Request
↓
Worker
↓
終了
```

だから、

- 同じメモリ状態を保持したい
- WebSocket接続を管理したい
- 同じデータへの処理を直列化したい
- 複数ユーザーを協調させたい

みたいな処理は苦手。

これはサーバーレス全般の弱点でもある。

普通なら、

**RedisとかDBに逃がす**

って話になる。

CloudflareはそこにDurable Objectsを用意した。

---

## Durable ObjectsでStateまでサーバーレスにした

Durable Objectsは、

**Compute + Storage**

を持つ、グローバルに一意なstateful object。

Cloudflare公式にも、

> Durable Objects enable stateful serverless applications.

と書かれてる。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__cc980409ea10767ebe3942ceec05920c" frameborder="0" height="29"></iframe>

例えばチャットなら、

```text
room-123
↓
Durable Object
├ users
├ messages
├ WebSocket
└ state
```

ゲームなら、

```text
match-456
↓
Durable Object
├ player A
├ player B
├ timer
└ game state
```

みたいにできる。

普通なら、

> サーバーレスなんだから状態を持たないように設計しよう

ってなるところを、

Cloudflareは、

> **じゃあState自体もサーバーレスのPrimitiveにしよう**

ってした。

この発想はかなりすごい。

Workersが、

**サーバーやプロセスを消す**

技術だとすると、

Durable Objectsは、

**サーバーを持たずにサーバーのStateだけ残す**

技術みたいに見える。

---

## D1やQueuesまでDurable Objectsベースなのがすごすぎる

Durable Objectsって最初、

「WebSocketとかリアルタイム用途の特殊な機能かな」

って思ってた。

でも全然違う。

D1は、各データベースが1つのDurable Objectをバックエンドに持ってる。

CloudflareのD1ドキュメントにも、

**Each individual D1 database is backed by a single Durable Object.**

と書かれてる。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__37834827330ce89b7131fc4afbaa4b67" frameborder="0" height="29"></iframe>

QueuesもCloudflare自身がDurable Objectsを使って再構築してる。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__869f19ecb74f8719faadd55a92696019" frameborder="0" height="29"></iframe>

これを見ると、

**DOってCloudflareのStateful系の根本Primitiveなんじゃないか**

ってなる。

ざっくり、

```text
Workers
↓
Durable Objects
↓
D1
Queues
Containers
...
```

みたいな世界。

Cloudflare自身が、ユーザーに提供してるPrimitiveを使って、自分たちのクラウドサービスまで作ってる。

これは相当おもしろい。

DOが改善される。

その上に乗ってるCloudflareのサービスも改善される。

基盤の進化がそのまま上位サービスに波及する。

こういう積み上げ方はかなり好き。

---

## Workersを無理やり万能にしてないのも強い

Workersには当然向いてない処理がある。

でもCloudflareは、Workers自体をNode.jsサーバーみたいにどんどん重くする方向には行ってない。

足りないものを別Primitiveに切ってる。

```text
HTTP / API
→ Workers

State
→ Durable Objects

SQL
→ D1

Object Storage
→ R2

非同期処理
→ Queues

長時間処理
→ Workflows

AI
→ Workers AI

フルLinux
→ Containers
```

この考え方がかなりいい。

Workersの、

**小さい・速い・安い**

って強みを残したまま、できないことだけ別サービスに逃がす。

Workers自体を万能化しない。

必要な機能だけ適材適所で使う。

この設計はかなり綺麗だと思う。

---

## ContainersでNode.jsが使えない弱みもほぼ消えた

昔のWorkersで一番気になってたのがNode.jsとの互換性。

Node前提のライブラリだったり、ネイティブバイナリだったり、PythonやJavaだったり。

V8 isolateなんだから当然なんだけど、ここは明確な弱点だった。

でも今はContainersがある。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__71744a2600366950d573f6d56133bc81" frameborder="0" height="29"></iframe>

Workerで動くならWorker。

無理ならContainer。

これでいい。

```text
API
→ Workers

State
→ Durable Objects

重い処理
→ Containers
```

この逃げ道があるのは普通にすごすぎる。

しかも、Workersを無理やりNode.js化して重くしたわけじゃない。

WorkersはWorkersのまま軽量に保って、フルLinuxが必要な処理だけContainerに逃がせる。

これでだいぶ話が変わった。

以前なら、

**Node.js前提だからWorkersは無理**

で終わってたものが、

**じゃあそこだけContainerでよくない？**

になる。

既存のNodeアプリをContainerで動かして、新しく切り出すAPIだけWorkersにするのもあり。

全部Workerに寄せる必要もない。

ここまで逃げ道が用意されると、さすがに採用しない理由がかなり減る。

---

## しかもContainersの管理にもDurable Objectsを使ってる

Containersも単に、

「Cloudflare版ECSを追加しました」

ではない。

Cloudflare公式の説明では、各ContainerをDurable Objectが管理する。

`Container` class自体が `DurableObject` を継承してる。

DOが、

- routing
- persistent state
- lifecycle
- sleep timeout

なんかを管理して、実際のコンテナプロセスはLinux VM上で動く。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__fbf14b4739691b783be90d85570c1eaf" frameborder="0" height="29"></iframe>

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__f0b31b6226f65f1e8cab00896c6a6e5c" frameborder="0" height="29"></iframe>

構造としては、

```text
Worker
↓
Durable Object
↓
Linux Container
```

って感じ。

Stateが必要だからDO。

Containerのidentityとlifecycleを管理したいからDO。

Queueを作るのにもDO。

D1のデータベースもDO。

こういうPrimitiveの再利用がかなり綺麗。

---

## Bindingも地味だけど相当強い

CloudflareではD1やR2なんかをBindingとしてWorkerから使える。

例えば、

```ts
env.DB
env.BUCKET
env.AI
```

みたいな感じ。

Cloudflare公式でもBindingsは、Workersからリソースへアクセスする時にREST APIより高性能で制約が少ないと説明してる。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__d1ff32efd8b30a133c31ea04a7132036" frameborder="0" height="29"></iframe>

普通のクラウドだと、

```text
Application
↓
SDK
↓
Endpoint
↓
API key / Credential
↓
Cloud Service
```

みたいになる。

Cloudflareは、

```text
Worker
↓
Binding
↓
Resource
```

で済む。

クラウドサービスが外部APIというよりRuntimeの機能に近い。

この感覚はかなり違う。

---

## Service Bindingでマイクロサービスもかなり綺麗に作れる

Worker同士もService Bindingでつなげられる。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__d8ba0cd2c6e54b1ae8785154ca8ca1b1" frameborder="0" height="29"></iframe>

例えば、

```text
Main Worker
├ User Worker
├ Billing Worker
└ Notification Worker
```

みたいにできる。

普通のマイクロサービスなら、

```text
Main Service
↓
HTTP
↓
認証
↓
API endpoint
↓
User Service
```

みたいになる。

Service Bindingなら、

```text
Main Worker
↓
Binding
↓
User Worker
```

でいい。

しかも各サービスに、

```text
User Worker
└ User D1

Post Worker
└ Post D1

Media Worker
└ R2
```

みたいにリソースを持たせられる。

一見BindingってCloudflare独自で使いづらそうに見えるんだけど、逆にWorker単位でサービスを分離しやすい。

**マイクロサービスの分離メリットを残しながら、通信・認証・ネットワーク設定の面倒をかなり減らせる。**

これは普通に強い。

---

## R2があんなに安いのも結局同じ話

R2もかなり面白い。

現在Standard Storageは、

**$0.015 / GB-month**

で、インターネットへのegressは無料。

さらにStandardでは10GB-month/月の無料枠もある。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__02f813a39abb92b93a4d024cba9a8dc3" frameborder="0" height="29"></iframe>

これも、

**S3より安くしました**

ってだけの話ではないと思ってる。

CloudflareはそもそもCDN事業者。

世界中へ大量のデータを配信するためのネットワークが、本業として存在してる。

つまりCloudflareにとって、

**ネットワークは単なるコストじゃなくて資産そのもの。**

だから、

```text
R2
↓
Cloudflare Network
↓
Internet
```

って構造を作れる。

S3的なObject Storageを作ってからCDNを付けたんじゃなくて、

**巨大なネットワークの中にObject Storageを作ってる。**

だからR2のegress無料もかなり納得感がある。

Workersと同じで、

**安いことより、安くできる構造を持ってることの方がすごい。**

---

## R2とWorkersの組み合わせも相性がいい

Workerで画像や動画、PDFなんかを扱う時、外部S3を使うと、

```text
Worker
↓
外部Cloud
↓
Storage
```

になる。

R2なら、

```text
User
↓
Cloudflare Network
↓
Worker
↓
R2
↓
Cloudflare Network
↓
User
```

とCloudflareの中でまとめられる。

CloudflareのWebアプリ向け公式ドキュメントでも、

- Workers
- D1
- KV
- R2
- Durable Objects
- Queues

を組み合わせた構成が紹介されてる。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__a0176a56ffd685a70bc65c48c1cef011" frameborder="0" height="29"></iframe>

もちろんR2の全ファイルが世界中の全エッジに常に置かれてるって意味ではない。

でも、

**Compute、Storage、CDN、Cache、WAFが同じNetworkの中にある**

ってだけでもかなり相性がいい。

---

## Email Serviceまで来たのはさすがにすごい

これは結構びっくりした。

Cloudflare WorkersからEmail Bindingでそのままメールを送れる。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__7696e533c1dbab59aee724b4608e9806" frameborder="0" height="29"></iframe>

しかも送信だけじゃなく、Email Routingを使えば受信メールもWorkerで処理できる。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__71caa351c4841bb62b251c401b8aff22" frameborder="0" height="29"></iframe>

つまり、

```text
メール受信
↓
Worker
↓
Workers AI
↓
D1
↓
Queue
↓
メール送信
```

みたいな構成までCloudflareだけで作れる。

Webサービス作ってると、メールって地味に外部サービスが必要になりがち。

Resend、SendGrid、SESとか。

そこまでCloudflareに入ってきた。

これはデカい。

Workers、D1、R2、Queues、AI、Emailまで揃ってくると、

**普通のSaaSならCloudflareだけでかなり完結するんじゃない？**

ってところまで来てる。

## AI分野でもかなり強い

AIもCloudflareとかなり相性がいい。

Workers AIを使えば、WorkerからそのままGPU推論を呼べる。

```ts
const result = await env.AI.run(model, input)
```

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__5e17a27acce617c9b80ee7d464847b1c" frameborder="0" height="29"></iframe>

普通ならGPUインスタンスを用意して、モデルを載せて、スケーリングやコストを考える必要がある。

Workers AIはそこもserverlessにしてる。

**CPUはWorkers、GPUはWorkers AI。**

必要な計算資源を自分で持たずに、Primitiveとして呼び出せる。

この考え方はWorkersとかなり一貫してる。

さらにAI Gatewayもある。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__b5a81603e044c2ca723559f8d8f364d3" frameborder="0" height="29"></iframe>

これはOpenAI、Anthropic、Googleみたいな外部AIの前段に置けて、

- ログ
- Rate Limit
- Cache
- Analytics
- Retry
- Fallback

なんかをまとめて持てる。

つまりCloudflare自身のAIを使わなくても、

```text
Worker
↓
AI Gateway
├ OpenAI
├ Anthropic
├ Google
└ Workers AI
```

みたいにできる。

これ結構えぐい。

**どのAIモデルが勝っても、AIアプリの入口はCloudflareにできる。**

Cloudflareはモデルそのもので勝負するというより、AIアプリ全体の基盤を取りにいってる感じがする。

Workers、D1、R2、Durable Objects、Queuesに加えてAIまで同じPlatformに入ってくると、AIアプリ作る時もかなり強い。

## 普通のSaaSならCloudflareだけでかなり完成する

今のCloudflareを並べると、

```text
API / Web
→ Workers

SQL
→ D1

State
→ Durable Objects

Object Storage
→ R2

Queue
→ Queues

Long Running
→ Workflows

AI
→ Workers AI

Email
→ Email Service

Node / Linux
→ Containers

CDN
WAF
DNS
DDoS
→ Cloudflare Network
```

まで揃ってる。

もうこれ、

**Edge Function**

って呼ぶには無理がある。

普通に汎用クラウド。

ただAWS型のクラウドとは考え方が違う。

**エッジネットワークを中心に作った汎用クラウド。**

って言った方がしっくりくる。

---

## Cloudflareは「採用しない理由」を一個ずつ消してる

昔なら、

```text
Node.js完全互換じゃない
↓
Workers使えない

Stateを持てない
↓
Workers使えない

長時間処理したい
↓
Workers使えない

Linuxが必要
↓
Workers使えない
```

ってなってた。

今は、

```text
State
→ Durable Objects

Long Running
→ Workflows

Node / Linux
→ Containers

SQL
→ D1

Storage
→ R2

AI
→ Workers AI

Email
→ Email Service
```

って逃げ道がある。

Workers本体を重くせずに、

**Workersを選ばない理由だけどんどん減ってる。**

Containersなんかまさにそう。

V8 isolateとは別のフルLinux環境だけど、

**どうしてもWorkerで無理ならこっち使えばいい**

って逃げ道として同じPlatformに置いてある。

ここまで揃うとさすがに強い。

---

## サービスが増えてるのに思想がバラバラになってない

AWSは本当に何でもある。

ただ、サービスが増えるほど、

```text
IAM
Role
Policy
VPC
Endpoint
Region
Security Group
SDK
```

みたいな知識も増えていく。

Cloudflareも当然簡単なことばかりじゃないけど、かなりのサービスが、

```text
Worker
↓
Binding
↓
Resource
```

って同じモデルに寄ってる。

Workersから、

```ts
env.DB
env.BUCKET
env.AI
env.SERVICE
```

みたいに触る。

StateならDO。

重いならContainer。

長時間ならWorkflow。

機能が増えてる割に、根本のMental Modelがそこまで増えてない。

**クラウドサービスを大量に横並びで増やすというより、一つのRuntimeを中心に周りを広げてる感じ。**

これもかなり使いやすい理由だと思う。

---

## 最終的にはインターネットそのものをRuntimeにするんじゃないかと思ってる

従来のWebアプリって、

```text
Internet
↓
CDN
↓
Load Balancer
↓
Server
↓
Application
```

だった。

Cloudflareは今、

```text
Internet
↓
Cloudflare Network
├ Workers
├ D1
├ R2
├ Durable Objects
├ Queues
├ Workflows
├ AI
├ Email
├ Containers
├ CDN
├ WAF
└ DNS
```

って世界を作ってる。

つまり、

**Cloudflare Networkに入った時点で、もうアプリケーション基盤の中にいる。**

世界中にあるCloudflareのサーバーを、

**1台の巨大な分散コンピューターみたいに扱おうとしてる。**

これ以上Cloudflareらしい形もない気がする。

---

## 少なくともここ数年、AWS・GCP・Azureがこれを上回るのは無理だと思う

もちろんAWSの方が強い分野はいくらでもある。

巨大Compute。

GPU。

企業向けサービス。

特殊なManaged Service。

だから、

**CloudflareがAWSより全部上**

って話ではない。

ただ、

**Webアプリケーションを作るプラットフォーム**

って目線なら、少なくともここ数年Cloudflare Workersを総合的に上回るのは無理だと思ってる。

理由は、

```text
V8 isolateによる高密度実行
+
世界中の既存Edge Network
+
リージョンレスに近いCompute
+
Durable Objects
+
D1
+
R2
+
Queues
+
Workflows
+
Containers
+
Workers AI
+
Email
+
Bindings
```

を全部再現する必要があるから。

しかも機能だけ同じにしても足りない。

Cloudflareと同じ価格帯まで持っていくなら、

**同じ実行効率と原価構造**

まで必要になる。

さらにCloudflareはWorkersを2017年から運用してる。

大量のisolateを世界中のエッジで安全に動かすための、

- Isolation
- Scheduling
- Memory Management
- CPU Control
- Code Distribution
- Security

みたいな知見も積み上がってる。

<iframe src="https://qiita.com/embed-contents/link-card#qiita-embed-content__f93e494cba91d24635ad9c519518ec84" frameborder="0" height="29"></iframe>

AWSが明日からisolate型のサービスを始めたとしても、同じ運用効率まで一気に持っていけるとは思えない。

GoogleはV8を持ってる。

AWSは資金力も物理的なサーバー量も圧倒的。

Microsoftも当然強い。

それでも、

**Cloudflareが持ってるエッジ運用の知見、既存ネットワーク、実行モデル、Developer Platform全体まで含めると、数年で逆転する未来はかなり見えにくい。**

---

## まとめ：安いのが正義じゃない。安くできるだけの余白があるのが強い

結局一番言いたいのはこれ。

Cloudflare Workersは安い。

R2も安い。

無料枠も大きい。

でも、

**安いから最強**

じゃない。

**ここまで安くできるだけのスケーリング余白があるから最強。**

だと思ってる。

V8 isolateで1台あたりの実行密度を上げる。

CDNとしてすでに存在してた世界規模のサーバーを使う。

Runtimeやハードウェアの改善を大量のWorkerで共有する。

StateはDurable Objects。

SQLはD1。

StorageはR2。

非同期はQueues。

長時間処理はWorkflows。

NodeやLinuxが必要ならContainersへ逃がす。

しかも、それらをBindingsでつないでいく。

Cloudflareは、

**サーバーを速くしてるんじゃない。**

**サーバーを効率よく使う構造そのものを変えてる。**

だから安い。

そして安いってことは、それだけまだスケールできる余白がある。

AWSがデータセンターをクラウド化したとするなら、

**Cloudflareはインターネットそのものをクラウド化してる。**

少なくとも今のWebアプリ向けプラットフォームで考えるなら、

**Cloudflare Workersがどう考えても最強だと思ってる。**

## わるいところ

ない（断言）

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
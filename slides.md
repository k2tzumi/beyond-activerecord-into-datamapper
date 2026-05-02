---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: ActiveRecordの呪縛を越えて：DataMapperと不変性の間で「コシ」のある設計のバランスを模索する
info: |
  PHPカンファレンス香川2026
  https://phpcon.kagawa.jp/2026/
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
seoMeta:
  ogImage: auto
# duration of the presentation, default is '30min'
duration: 30min
# timer mode, can be 'countdown' or 'stopwatch', default is 'stopwatch'
timer: stopwatch
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
highlighter: shiki
css: unocss
colorSchema: dark
routerMode: hash
addons:
  - '@katzumi/slidev-addon-qrcode'
  - '@katzumi/slidev-addon-ogp-image'
  - slidev-addon-components
  - slidev-addon-rabbit
---

# ActiveRecordの呪縛を越えて：DataMapperと不変性の間で「コシ」のある設計のバランスを模索する

PHP カンファレンス香川 2026 May 9, 2026.  
v0.0.3  
@katzumi(かつみ)

<div @click="$slidev.nav.next" class="mt-12 py-1" hover:bg="white op-10">
  Press Space for next page <carbon:arrow-right />
</div>

<div class="abs-br m-6 text-xl">
  <button @click="$slidev.nav.openInEditor" title="Open in Editor" class="slidev-icon-btn">
    <carbon:edit />
  </button>
  <a href="https://github.com/k2tzumi/beyond-activerecord-into-datamapper" target="_blank" class="slidev-icon-btn">
    <carbon:logo-github />
  </a>
</div>

<!--
本日は「ActiveRecordの呪縛を越えて：DataMapperと不変性の間で「コシ」のある設計のバランスを模索する」というタイトルで、お話しさせていただきます。  
どうぞよろしくお願いいたします。
-->

---
transition: fade-out
layout: two-cols-header
class: text-left
---

# <carbon-user-avatar /> 自己紹介

katzumi（かつみ）と申します。

「障害のない社会をつくる」をビジョンとする「LITALICO（りたりこ）」に所属しています。
<a href="https://litalico.co.jp/" target="_blank">
  <img src="https://litalico.co.jp/ogp.png" class="w-40 mt-3 mb-8" />
</a>

以下のアカウントで活動しています。

::left::

<div class="flex items-center mb-6">
  <img src="https://pbs.twimg.com/profile_images/1961960367351005184/LpDD3fDz_400x400.jpg" class="rounded-full w-24 mr-6"/>

  <QRCode text="https://twitter.com/katzchum" class="w-28 h-28" />
</div>

<div class="flex items-center text-xl mt-4">
  <simple-icons-x class="mr-2" />
  <a href="https://twitter.com/katzchum" target="_blank" class="font-bold">katzchum</a>
</div>

::right::

<div class="flex flex-col space-y-4">
  <div class="flex items-center">
    <img src="https://avatars.githubusercontent.com/u/1182787?v=4" class="rounded-full w-24 mr-6"/>
    <div class="flex flex-col">
      <div class="flex items-center text-xl">
        <logos-github-octocat class="mr-2" /> 
        <a href="https://github.com/k2tzumi" target="_blank" class="font-bold">k2tzumi</a>
      </div>
      <div class="flex items-center text-xl mt-2">
        <simple-icons-zenn class="mr-2 text-green-400" />
        <a href="https://zenn.dev/katzumi" target="_blank" class="font-bold">katzumi</a>
      </div>
    </div>
  </div>
</div>

<style>
/* タイトルのスタイルはそのまま維持 */
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

<!--
みなさん、こんにちは。LITALICOの「かつみ」と申します。  
ご覧のXとGitHub等で活動しています。
-->

---
layout: two-cols-header
transition: fade-out
---

# <carbon-information /> お願い 🙏

写真撮影、SNS での実況について

登壇者の励みになるので是非ともご意見やご感想など、フィードバック頂けると助かります mm  
スライドの内容は、すでに以下の場所で公開されていますので、ぜひお手元でご覧ください  

* [forteeのプロポーザルページ](https://fortee.jp/phpconkagawa-2026/proposal/589a8ec7-ab79-4c14-893a-03b4dff32795)
* または <fa6-brands-square-x-twitter /> の投稿

::left::

<Transform :scale="2.5">
　　　🙆‍♀📷<ph-projector-screen-chart-light /><br />
　　　🙅‍♂📹💸<br />
　　　🙅📸👨‍👦‍👦<br />
</Transform>

::right::

<br />
<Transform :scale="2">
<fa6-brands-square-x-twitter />
</Transform>
<br />
<a href="https://x.com/search?q=%23phpconkagawa%20%23sotetsu&f=live">#phpconkagawa #sotetsu</a>

<!--
まずはじめにお願いです。写真撮影、SNSでの実況、大歓迎です。スライドも公開済みですので、ぜひハッシュタグをつけて、ご意見やご感想をフィードバックいただけると励みになります。
-->

---

# <carbon-presentation-file /> 本日のお話すること
お品書き

<div class="mt-6 grid grid-cols-4 gap-4">
  <div class="rounded-xl border-2 border-amber-300/70 bg-amber-100/10 px-4 py-5 min-h-[340px] flex flex-col shadow-lg">
    <div class="text-sm text-amber-200/90 tracking-widest flex items-center gap-1"><mdi-noodles class="text-amber-200" /><mdi-noodles class="text-amber-200" /> 1杯目</div>
    <div class="mt-4 text-lg leading-snug font-bold">Active Record / Eloquent 運用で何がつらかったのか <carbon-face-dissatisfied class="inline" /></div>
    <div class="mt-4 text-sm text-white/80 leading-relaxed">詰め替えコストとドメイン汚染</div>
  </div>

  <div class="rounded-xl border-2 border-green-300/70 bg-green-100/10 px-4 py-5 min-h-[340px] flex flex-col shadow-lg">
    <div class="text-sm text-green-200/90 tracking-widest flex items-center gap-1"><mdi-noodles class="text-green-200" /><mdi-noodles class="text-green-200" /> 2杯目</div>
    <div class="mt-4 text-lg leading-snug font-bold">Data Mapper / Doctrine に何を期待したのか <carbon-idea class="inline" /></div>
    <div class="mt-4 text-sm text-white/80 leading-relaxed">POPO と責務分離の魅力</div>
  </div>

  <div class="rounded-xl border-2 border-sky-300/70 bg-sky-100/10 px-4 py-5 min-h-[340px] flex flex-col shadow-lg">
    <div class="text-sm text-sky-200/90 tracking-widest flex items-center gap-1"><mdi-noodles class="text-sky-200" /><mdi-noodles class="text-sky-200" /> 3杯目</div>
    <div class="mt-4 text-lg leading-snug font-bold">なぜ途中でハマったのか <carbon-warning-alt class="inline" /></div>
    <div class="mt-4 text-sm text-white/80 leading-relaxed">immutable な設計と Unit of Work の衝突</div>
  </div>

  <div class="rounded-xl border-2 border-rose-300/70 bg-rose-100/10 px-4 py-5 min-h-[340px] flex flex-col shadow-lg">
    <div class="text-sm text-rose-200/90 tracking-widest flex items-center gap-1"><mdi-noodles class="text-rose-200" /><mdi-noodles class="text-rose-200" /> 4杯目</div>
    <div class="mt-4 text-lg leading-snug font-bold">最後にどう着地したのか <carbon-checkmark-outline class="inline" /></div>
    <div class="mt-4 text-sm text-white/80 leading-relaxed">mutable な Entity とガードレール、そして残るジレンマ</div>
  </div>
</div>

<!--
本日お話しする内容のお品書きです。 
現在私が携わっている とあるプロジェクトでの、データマッパー（Doctrine）を導入した際の事例になります。  
ActiveRecodパターンからDataMapperパターンに寄せたときに何が楽になり、一方でどこで嵌って悩んでしまったのか？  
そして、どの様に考え、どう対処していったかという過程を整理してお話しします。
-->

---
layout: center
transition: fade-out
---

# <mdi-layers-triple-outline /> クリーン (レイヤード) アーキテクチャ 採用されていますか？
LLM 時代で再注目されるアーキテクチャ

<span v-mark.underline.blue>クリーンアーキテクチャ / レイヤードアーキテクチャ</span>は、
「きれいな設計」のためだけでなく <br />

<span v-mark.highlight.orange>LLM へ渡すコンテキストを小さく保つための実践知</span>として
改めて注目されている気がしています。


<div 
  v-click="3"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 3 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'">
  <div 
    class="semantic-overlay semantic-ask p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500">
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-head-lightbulb-outline class="inline mr-2 text-yellow-300" />
      適切なコンテキストを提供し、<br />不要な認知負荷を削ることができていますか？
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-layers-triple-outline class="inline mr-2" />ドメインとインフラの分離
      </span>
      <br />
      <span class="text-yellow-300 font-bold">
        <mdi-arrow-decision-outline class="inline mr-2" />依存方向
      </span>
      <br />
      <span class="text-yellow-300 font-bold">
        <mdi-dots-horizontal-circle-outline class="inline mr-2" />etc..
      </span>
    </p>
  </div>
</div>

<!--
本題に入る前のアイスブレイクとして、私が最近感じていることをこのスライドにまとめました。  
[click] クリーンアーキテクチャやレイヤードアーキテクチャは、昔から“きれいな設計”を行うという文脈で語られてきました。  
[click] ただ今はそれだけでなく、LLMに渡すコンテキストを小さく保つための実践として、価値が上がっていると感じています。  
[click] その中で大量に生成されるコードに対して、皆さんはどの様にして認知負荷を下げる取り組みをされていますでしょうか？
-->

---
class: py-10
glowSeed: 215
---

# <carbon-model-alt /> ドメインとインフラの分離

<span>ドメインを守るための 3つの狙い</span>

<div mt-8 />

<div flex flex-col items-center>
  <div
    grid grid-cols-3 gap-6
    w-full max-w-200
  >
    <div
      v-click="1"
      border="2 solid yellow-600" bg="yellow-600/20"
      rounded-lg overflow-hidden
      transition duration-500 ease-in-out
      :class="$clicks < 1? 'opacity-0 translate-y-20' : 'opacity-100 translate-y-0'"
    >
      <div bg="yellow-600/40" px-4 py-2 flex items-center justify-center text-sm>
        <span font-bold><mdi-set-split />関心を分離したい</span>
      </div>
      <div px-4 py-4 flex flex-col gap-3>
        <div flex items-center gap-3>
          <div class="text-3xl w-10 text-center">🧠</div>
          <div>
            <div font-semibold><span class="text-yellow-200">ドメインモデル</span> = 業務ルール</div>
          </div>
        </div>
        <div flex items-center gap-3>
          <div class="text-3xl w-10 text-center">🗄️</div>
          <div>
            <div font-semibold><span class="text-yellow-200">永続化</span> = インフラ層の責務</div>
          </div>
        </div>
        <div class="flex items-start gap-3 mt-1 p-3 rounded-md border border-yellow-300/50 bg-yellow-200/10">
          <div class="text-3xl w-10 text-center">🕊️</div>
          <div>
            <div class="text-xs tracking-wider text-yellow-200/85 mb-1">狙い</div>
            <div font-semibold>ドメインは「どうDB操作するか」の都合から自由でありたい</div>
          </div>
        </div>
      </div>
    </div>
    <div
      v-click="2"
      border="2 solid green-600" bg="green-600/20"
      rounded-lg overflow-hidden
      transition duration-500 ease-in-out
      :class="$clicks < 2 ? 'opacity-0 translate-y-20' : 'opacity-100 translate-y-0'">
      <div bg="green-600/40" px-4 py-2 flex items-center justify-center text-sm>
        <span font-bold><mdi-check-decagram-outline />テストしやすくしたい</span>
      </div>
      <div px-4 py-4 flex flex-col gap-3>
        <div flex items-center gap-3>
          <div class="text-3xl w-10 text-center">🧪</div>
          <div>
            <div font-semibold text-base>業務ルールのテスト失敗を、インフラ要因と分離しやすい</div>
          </div>
        </div>
        <div flex items-center gap-3>
          <div class="text-3xl w-10 text-center">⚡</div>
          <div>
            <div font-semibold text-base>テストの実行速度が格段に上がる</div>
          </div>
        </div>
        <div class="flex items-start gap-3 mt-1 p-3 rounded-md border border-green-300/50 bg-green-200/10">
          <div class="text-3xl w-10 text-center">🎯</div>
          <div>
            <div class="text-xs tracking-wider text-green-200/85 mb-1">狙い</div>
            <div font-semibold text-base>POPOならDB接続なしで高速にユニットテストできる</div>
          </div>
        </div>
      </div>
    </div>
    <div
      v-click="3"
      border="2 solid blue-600" bg="blue-600/20"
      rounded-lg overflow-hidden
      transition duration-500 ease-in-out
      :class="$clicks < 3 ? 'opacity-0 translate-y-20' : 'opacity-100 translate-y-0'"
    >
      <div bg="blue-600/40" px-4 py-2 flex items-center justify-center text-sm>
        <span font-bold><mdi-autorenew />変更耐性を上げたい</span>
      </div>
      <div px-4 py-4 flex flex-col gap-3>
        <div flex items-center gap-3>
          <div class="text-3xl w-10 text-center">✂️</div>
          <div>
            <div font-semibold text-base>コードの変更範囲を限定できる</div>
          </div>
        </div>
        <div flex items-center gap-3>
          <div class="text-3xl w-10 text-center">🧭</div>
          <div>
            <div font-semibold text-base>設計の意図が明確に残る</div>
          </div>
        </div>
        <div class="flex items-start gap-3 mt-1 p-3 rounded-md border border-blue-300/50 bg-blue-200/10">
          <div class="text-3xl w-10 text-center">🛡️</div>
          <div>
            <div class="text-xs tracking-wider text-blue-200/85 mb-1">狙い</div>
            <div font-semibold text-base>業務変更と無関係な最適化（DB インデックス、SQL チューニング等）でもドメインへの波及を抑えたい</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<div 
  v-click="4"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 4 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-ask p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-2xl font-bold text-white4">
      <mdi-noodles class="inline mr-2 text-yellow-300" />
      この「コシ」のある設計を<br />どう実現するのか？
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-arrow-collapse class="inline mr-2" />責務を十分小さくする
      </span>
      <br />
      <span class="text-yellow-300 font-bold">
        <mdi-set-split class="inline mr-2" />認知できるサイズとなるように分離する
      </span>
      <br />
      <span class="text-yellow-300 font-bold">
        <mdi-numeric-1-circle-outline class="inline mr-2" />修正する理由は1つにする
      </span>
      <br />
    </p>
  </div>
</div>

<!--
今回のテーマとしてORMマッパーを扱いますが、「コシのある設計」の関心事が、まさにドメインとインフラの分離となります。  
これには3つの狙いがあり
[click] まず関心事を分離して、データの永続化という技術的な詳細をモデルから隔離します。  
[click] 次に、モデルをPOPO（Plain Old PHP Object）にして、DBやネットワークなどのインフラに依存させないようにすることで、テストを高速化できます。  
[click] 最後に変更耐性については、技術的な課題は技術的な層だけで完結させる様にします。  
[click] この様な責務分離という「コシ」が求められる設計をどう実現するのかについて、本日はお話ししたいと思います。
-->

---

# <carbon-choices /> 皆さんのEloquentの向き合い方はどれ？
ActiveRecord 指向のフレームワークで Repository パターンを無理に導入するとアレなの？

<Transform :scale="1.3">

<v-clicks>

1. Eloquent を ActiveRecord として素直に使う<mdi-railroad-light />  
Repository は不要。UseCase から Eloquent を直接叩く。  
Laravel のレールに最も乗った形  
2. Eloquent を QueryBuilder としてのみ使う<carbon-query-queue />  
Eloquent をインフラ層（Repository）に限定し、  
アプリケーション層へは POPO なドメインモデルとして渡す  
3. Eloquent 自体を捨てる<carbon-trash-can />  
DDD を本気でやるなら適材適所。  
他のツールを選んだり、PDO で生の SQL 書いたり

</v-clicks>

</Transform>

<!--
その前提の上で、改めて皆さんに質問です。  
Laravelを利用しているプロジェクトで、Eloquentとどう向き合っていますでしょうか？   
おそらく、以下に挙げる3つのパターンのうち、どれかに当てはまるのではないでしょうか。  
[click] 1つ目は純粋にActiveRecordとして使うパターン  
[click] 2つ目はQueryBuilderとしてのみ使い、ドメインモデルへ詰め替えるパターン。  
[click] そして3つ目は、DDDなどを本気でやるためにEloquent自体を捨てるパターンです。
-->

---
layout: two-cols-header
transition: fade-out
---

# <mdi-map-marker-account-outline /> 私のこれまでの立ち位置
「Eloquent を QueryBuilder として使い、ドメインモデルへ詰め替える」というアプローチ

::left::

<v-click>

<mdi-database-search-outline /> 読み取り時:
```php {all}{lines: true}
/**
 * 読み取り時の詰め替え (Eloquent -> Domain Model)
 */
public function find(int $id): ?User
{
  $eloquentUser = UserEloquent::find($id);

  if (!$eloquentUser) return null;

  // ここでプロパティを一つずつマッピングする作業が発生する
  return new User(
    id: $eloquentUser->id,
    name: $eloquentUser->name,
    email: $eloquentUser->email,
    address: $eloquentUser->address,
    phoneNumber: $eloquentUser->phone_number,
    isActive: (bool)$eloquentUser->is_active,
    createdAt: 
      new \DateTimeImmutable($eloquentUser->created_at)
  );
}
```
</v-click>

::right::

<v-click>

 <mdi-database-edit-outline /> 書き込み時:
```php {all}{lines: true}
/**
 * 書き込み時の詰め替え (Domain Model -> Eloquent)
 */
public function save(User $user): void
{
  $eloquentUser = UserEloquent::findOrNew($user->id);

  // 保存時も同様に一つずつ値を詰め直す
  $eloquentUser->name = $user->name;
  $eloquentUser->email = $user->email;
  $eloquentUser->address = $user->address;
  $eloquentUser->phone_number = $user->phoneNumber;
  $eloquentUser->is_active = $user->isActive;

  $eloquentUser->save();
}
```

</v-click>

<!--
私のこれまでの立ち位置としては、2番目の「Eloquentをクエリビルダーとして使って、ドメインモデルへ詰め替える」というアプローチを取っていました。  
[click] 読み取り時は、Eloquentモデルからドメインモデルへ。   
[click] 書き込み時は、ドメインモデルからEloquentモデルというように、このようにプロパティとカラムを一つずつマッピングして詰め直す作業をそれぞれ行っていました。
-->

---

# <carbon-warning-filled /> 辛い。。
変換コードが増え続ける

<Transform :scale="1.3">

カラムが増えるたびに、この相互変換のコードが膨れ上がる。   

本質的なツラミは、ドメインモデルと永続化モデル ( `Eloquent` ) が二重化して、  
整合性コストが増えること。  

その結果、変更のたびに読み書き両方を直す必要がある。  

又、ユースケースが増えるたびに同様の変換が増殖する。

</Transform>

<!--
しかし、これが辛いんですよね。  
カラムが増えるごとに、この変換コードがどんどん膨れ上がっていきます。  本質的なツラミとしては、ドメインモデルと永続化モデル（今回でいえばEloquent）が二重管理になってしまって、整合性を保ち続けるためのコストが増え続けることでした。  
Repositoryにユースケース固有のメソッドが増えるたびに、それぞれで変換コードを書く必要が生じるという構造的な問題がありました。
-->

---

# <carbon-drill-down /> もっと根深い

<OgpImage url="https://www.docswell.com/s/katzumi/Z989R9-activerecord-pattern-unlearning-clean-architecture" />


今回のタイトルの `呪縛` というワードはこのスライドからきています　　

<div class="mt-4 p-6 rounded-2xl border-2 border-red-500/70 bg-red-500/10 shadow-lg shadow-red-900/20">

<carbon-table-split />　ActiveRecord パターンは「1 テーブル ＝ 1 クラス」という制約を強います<br /><br />
<carbon-data-base-alt />「DB 設計 ＝ ドメイン設計」という呪縛に陥りがち

</div>

<!--
以前、私が「ActiveRecordパターンの呪縛を学びほぐして挑むクリーンアーキテクチャへの入り口」というタイトルでPHPカンファレンス沖縄で登壇しました。 
今回のタイトルにも入っている「呪縛」というワードは、ここから来ています。
データベースは単なる永続化の手段であって、ビジネスの関心事そのものではない。まずはデータベースのことは忘れて、ピュアなビジネスロジックを設計しよう、という内容でした。  
今回は、その取り組みからさらに発展した学びを発表したいと思っています。
-->

---
layout: two-cols-header
transition: fade-out
---

# <mdi-compare-remove /> 不協和音の正体
責務は正しい、でも前提が噛み合わない

::left::

<div
  v-click="1"
  class="min-h-[300px] p-6 rounded-xl border border-amber-400/50 bg-amber-500/10"
>
  <h3 class="text-2xl font-bold text-amber-200 mb-4">
    <mdi-database-outline class="inline mr-2 text-amber-300" />
    ActiveRecord / Eloquent 側の前提
  </h3>

  <div class="space-y-3 text-lg leading-relaxed">
    <div class="flex items-start gap-2">
      <mdi-database-outline class="mt-1 text-amber-300 shrink-0" />
      <span>モデルが永続化と密接に結びつく</span>
    </div>
    <div class="flex items-start gap-2">
      <mdi-table class="mt-1 text-amber-300 shrink-0" />
      <span>1テーブル = 1モデルで扱う発想</span>
    </div>
    <div class="flex items-start gap-2">
      <mdi-content-save-outline class="mt-1 text-amber-300 shrink-0" />
      <span>保存操作を中心にユースケースを組み立てやすい</span>
    </div>
  </div>
</div>

::right::

<div
  v-click="2"
  class="min-h-[300px] p-6 rounded-xl border border-sky-400/50 bg-sky-500/10"
>
  <h3 class="text-2xl font-bold text-sky-200 mb-4">
    <mdi-layers-triple-outline class="inline mr-2 text-sky-300" />
    クリーンアーキテクチャ側の前提
  </h3>

  <div class="space-y-3 text-lg leading-relaxed">
    <div class="flex items-start gap-2">
      <mdi-cube-outline class="mt-1 text-sky-300 shrink-0" />
      <span>ドメインモデルをPOPOとして独立させる</span>
    </div>
    <div class="flex items-start gap-2">
      <mdi-database-remove-outline class="mt-1 text-sky-300 shrink-0" />
      <span>永続化の都合をドメインに持ち込まない</span>
    </div>
    <div class="flex items-start gap-2">
      <mdi-call-split class="mt-1 text-sky-300 shrink-0" />
      <span>境界で責務を分離して変更影響を局所化する</span>
    </div>
  </div>
</div>

<div
  v-click="3"
  class="absolute bottom-10 left-1/2 -translate-x-1/2 w-[92%] p-5 rounded-xl border-2 border-red-400/70 bg-red-500/15 text-center"
>
  <p class="text-xl font-bold text-red-200">
    <mdi-alert-circle-outline class="inline mr-2 text-red-300" />
    <span v-mark.highlight.orange>モデル単体の責務はどちらも妥当</span><br />
    ただし境界で前提が衝突し、<span v-mark.underline.red>詰め替え・変換コードが増殖</span>する
  </p>
</div>

<!--
[click] まず左はEloquent側の前提です。これはLaravel標準の使い方で、モデルと永続化の責務が密接に結びつけて、1テーブル1モデルで扱う発想です。トランザクションスクリプトを書くには非常に便利です。  
[click] 右はクリーンアーキテクチャ側の前提です。ドメインモデルをPOPOとして独立させ、境界で責務を分離して変更を局所化するという取り組みで、これも設計として正しいです。  
[click] 問題は、どちらかが正しいかではなくて、両方を一緒に使う時にこの「境界」で前提条件がぶつかり合うことなんです。  
その衝突の副作用として、詰め替えや変換コードが増えてしまうという構図になっていました。
-->

---

# <carbon-sun /> DataMapperパターンという希望


<div class="mx-auto mt-6 w-full max-w-5xl rounded-xl bg-white p-6 shadow-2xl">
  <img src="/data-mapper.svg" class="w-full h-auto" />
</div>

<!--
そして、その課題に対応するための希望の光となったのが、「DataMapperパターン」です。  
こちらの図でよく説明されています。
-->

---

# <carbon-map-boundary /> DataMapperとは？

<blockquote>
オブジェクトとデータベースの間でデータをやり取りするためのマッパー層。これにより、オブジェクトとデータベース、そしてマッパー自身が互いに独立した状態に保たれます。<br />

`A layer of mappers that moves data between objects and a database while keeping them independent of each other and the mapper itself.`
</blockquote>

<div class="text-xs opacity-65 mt-4">
  <a href="https://martinfowler.com/eaaCatalog/dataMapper.html"><mdi-open-in-new class="inline mr-1" /> https://martinfowler.com/eaaCatalog/dataMapper.html</a>
</div>

<!--
マーティン・ファウラーの定義によると、DataMapperとは「オブジェクトとデータベースの間でデータをやり取りするためのマッパー層」であり、「オブジェクトとデータベースを互いに独立した状態に保つ」ものと定義されています。
-->

---

# <carbon-document-vertical /> ActiveRecordとは？

<blockquote>
データベースのテーブルやビューの1行をラップし、データベースへのアクセスをカプセル化して、そのデータに対するドメインロジックを追加するオブジェクト。<br />

`An object that wraps a row in a database table or view, encapsulates the database access, and adds domain logic on that data.`
</blockquote>

<div class="text-xs opacity-65 mt-4">
  <a href="https://www.martinfowler.com/eaaCatalog/activeRecord.html"><mdi-open-in-new class="inline mr-1" /> https://www.martinfowler.com/eaaCatalog/activeRecord.html</a>
</div>

<div class="mx-auto mt-4 w-full rounded-xl bg-white p-4 shadow-2xl">
  <img src="/active-record.svg" class="mx-auto max-h-[30vh] w-auto max-w-full object-contain" />
</div>

<!--
対してActiveRecordは、「データベースの行をラップし、データベースアクセスをカプセル化して、そこにドメインロジックを追加するオブジェクト」です。  
下の図のようなイメージです。
-->

---

# <carbon-compare /> ActiveRecordとの違い
Persistent ModelからDomain Modelへの昇華

<div class="relative mx-auto w-240 max-w-full h-76 border rounded-xl border-slate-500/40 bg-white/5 overflow-hidden">
  <div
    v-motion
    :initial="{ x: 0, y: 0, opacity: 1, scale: 1, height: 268 }"
    :enter="{ x: 0, y: 0, opacity: 1, scale: 1, height: 268 }"
    :click-1="{ height: 218 }"
    style="position: absolute; left: 22px; top: 18px; width: 240px; border: 1px solid rgba(148,163,184,0.7); border-radius: 8px; background: rgba(255,255,255,0.04);"
  >
    <div style="border-bottom: 1px solid rgba(148,163,184,0.7); padding: 8px 12px; font-weight: 600;">Person</div>
    <div style="padding: 10px 12px; font-family: ui-monospace, SFMono-Regular, Menlo, monospace; font-size: 12px; opacity: 0.88; line-height: 1.6;">
      lastName<br>
      firstName<br>
      numberOfDependents
    </div>
    <div style="border-top: 1px solid rgba(148,163,184,0.7); padding: 10px 12px 0; font-family: ui-monospace, SFMono-Regular, Menlo, monospace; font-size: 12px; line-height: 1.65;">
      getExemption()<br>
      isFlaggedForAudit()<br>
      getTaxableEarnings()
    </div>
    <div v-click.hide="1" class="absolute right-10px bottom-10px text-11px text-indigo-300">Persistent Model</div>
    <div v-click="1" class="absolute right-10px bottom-10px text-11px text-indigo-300">Domain Model</div>
  </div>

  <div
    v-motion
    :initial="{ x: 0, y: 0, width: 212, height: 138, opacity: 0 }"
    :enter="{ x: 0, y: 0, width: 212, height: 138, opacity: 0 }"
    :click-1="{ x: -30, y: 28, width: 232, height: 182, opacity: 1 }"
    style="position: absolute; left: 336px; top: 24px; border: 1px solid rgba(148,163,184,0.8); border-radius: 8px; background: rgba(255,255,255,0.06);"
  >
    <div style="border-bottom: 1px solid rgba(148,163,184,0.65); padding: 8px 12px; font-weight: 600;">Person Mapper</div>
    <div v-click="1" class="absolute right-10px bottom-10px text-11px text-indigo-300">DataMapper</div>
  </div>

  <div
    v-motion
    :initial="{ x: 0, y: 0 }"
    :enter="{ x: 0, y: 0 }"
    :click-1="{ x: 8, y: -18 }"
    style="position: absolute; left: 568px; top: 164px; width: 92px; height: 58px; border: 1px solid rgba(148,163,184,0.75); border-radius: 999px / 48px; display: flex; align-items: center; justify-content: center; font-size: 12px;"
  >
    DB
  </div>

  <div
    v-click.hide="1"
    style="position: absolute; left: 266px; top: 193px; width: 294px; border-top: 2px dashed rgba(148,163,184,0.75);"
  ></div>
  <div
    v-click.hide="1"
    style="position: absolute; left: 560px; top: 189px; width: 0; height: 0; border-top: 4px solid transparent; border-bottom: 4px solid transparent; border-left: 8px solid rgba(148,163,184,0.95);"
  ></div>

  <div
    v-click="1"
    style="position: absolute; left: 270px; top: 136px; width: 36px; border-top: 2px solid rgba(148,163,184,0.82);"
  ></div>
  <div
    v-click="1"
    style="position: absolute; left: 262px; top: 132px; width: 0; height: 0; border-top: 4px solid transparent; border-bottom: 4px solid transparent; border-right: 8px solid rgba(148,163,184,0.95);"
  ></div>

  <div
    v-click="1"
    style="position: absolute; left: 540px; top: 175px; width: 28px; border-top: 2px solid rgba(148,163,184,0.82);"
  ></div>
  <div
    v-click="1"
    style="position: absolute; left: 568px; top: 171px; width: 0; height: 0; border-top: 4px solid transparent; border-bottom: 4px solid transparent; border-left: 8px solid rgba(148,163,184,0.95);"
  ></div>

  <div
    v-motion
    :initial="{ x: 0, y: 0, opacity: 1 }"
    :enter="{ x: 0, y: 0, opacity: 1 }"
    :click-1="{ x: 284, y: -110, opacity: 1 }"
    style="position: absolute; left: 44px; top: 234px; font-family: ui-monospace, SFMono-Regular, Menlo, monospace; font-size: 12px; color: #f59e0b;"
  >
    insert()
  </div>
  <div
    v-motion
    :initial="{ x: 0, y: 0, opacity: 1 }"
    :enter="{ x: 0, y: 0, opacity: 1 }"
    :click-1="{ x: 284, y: -98, opacity: 1 }"
    style="position: absolute; left: 44px; top: 250px; font-family: ui-monospace, SFMono-Regular, Menlo, monospace; font-size: 12px; color: #f59e0b;"
  >
    update()
  </div>
  <div
    v-motion
    :initial="{ x: 0, y: 0, opacity: 1 }"
    :enter="{ x: 0, y: 0, opacity: 1 }"
    :click-1="{ x: 284, y: -86, opacity: 1 }"
    style="position: absolute; left: 44px; top: 266px; font-family: ui-monospace, SFMono-Regular, Menlo, monospace; font-size: 12px; color: #f59e0b;"
  >
    delete()
  </div>

  <div v-click.hide="1" class="absolute left-270px top-232px text-12px text-amber-500">DB 永続化の責務</div>
</div>

<div v-click="1" class="text-sm mt-3 opacity-85">
  Person は純粋なドメインオブジェクトとして保ち、DB とのやり取りは Person Mapper に委譲する
</div>

<!--
図解して説明します。  
ActiveRecordパターンでは、ドメインモデルがそのまま永続化モデルになっていて、insert や update といった永続化に関する責務も持ち合わせています。  
[click] これをDataMapperパターンではドメインオブジェクトとDBに間にマッパー層を設けて分離させます。  
元々Personが持っていたDBのやり取りがマッパーへ移譲される形となります。  
これによって、オブジェクトは自身のビジネスルールに集中できるようになります。
-->

---

# <mdi-database-cog-outline /> なぜDoctrineなのか？
ドメインモデルがDBやORMの都合を一切知らなくても良くする

<Transform :scale="1.3">

* <carbon-data-enrichment /> ドメイン自身が永続化の関心ごとから完全に隔離されることができる  
POPO な Entity ＋ 独立した XML マッピングによる完全な分離。
* <mdi-link-variant /> ドメインモデルと永続化モデルを1つのクラスで扱える効率性。

</Transform>

<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'">
  <div 
    class="semantic-overlay semantic-positive p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500">
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-incognito class="inline mr-2 text-green-300" />
      永続性の無視 (Persistence Ignorance)
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-shield-lock-outline class="inline mr-2" />
        永続化の細部をアプリケーションの他の部分からカプセル化
      </span>
      <br />
    </p>
  </div>
</div>

<!--
では、なぜDoctrineなのか?  
PHPでDataMapperパターンを実現する際によく使われるのがSymfonyでお馴染みのDoctrineです。   
Doctrineを採用する最大の理由は、ドメインが永続化の関心事から完全に分離されるからです。  
[click] つまり、「永続性の無視（Persistence Ignorance）」を実現したいと考えました。簡単に言うと、ドメインモデルがDBやORMの都合を一切知らなくても良い状態です。これを目指したかったのです。
-->

---

# <logos-doctrine /> Doctrineの導入後の世界

```php {*}{lines:true}
// src/Domain/Model/Product.php
class Product {
    // 永続化に付随するメタデータ（ハイドレーションでセット）
    private readonly　int $id = null;
    private readonly \DateTimeImmutable $updatedAt;
    
    public function __construct(
        private readonly Code $code, // ビジネス上の識別子
        private readonly　string $name,
        private readonly　Price $price
    ) {
        if (mb_strlen($name) < 3)
            throw new \InvalidArgumentException("商品名は3文字以上必要です");
    }

    public function applyCampaignPrice(Price $campaignPrice): self {
        if ($campaignPrice->amount >= $this->price->amount) {
            throw new \DomainException("キャンペーン価格は通常価格より安く設定してください");
        }
        return new self($this->code, $this->name, $this->price);
    }
}
```

<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'">
  <div 
    class="semantic-overlay semantic-positive p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500">
    <h3 class="text-5xl font-extrabold text-white mb-4">純度の高いPOPOとしてドメインモデルとして定義できる</h3>
    <div class="text-xl text-green-300 font-bold leading-relaxed space-y-2">
      <div class="flex items-start gap-2">
        <mdi-form-select class="mt-1 text-yellow-300 shrink-0" />
        <span class="text-yellow-300">コンストラクタがビジネス上必要な情報だけを受け取る</span>
      </div>
      <div class="flex items-start gap-2">
        <mdi-lock-outline class="mt-1 text-yellow-300 shrink-0" />
        <span class="text-yellow-300">永続化に付属するメタデータ ( id 等 ) は非公開で保持</span>
      </div>
      <div class="flex items-start gap-2 pl-6">
        <mdi-eye-off-outline class="mt-1 text-green-200 shrink-0" />
        <span class="text-green-200 font-semibold text-lg">ドメイン層・アプリケーション層からは見えない（private）</span>
      </div>
      <div class="flex items-start gap-2 pl-6">
        <mdi-refresh class="mt-1 text-green-200 shrink-0" />
        <span class="text-green-200 font-semibold text-lg">Doctrineがリフレクション経由でハイドレーション時にセット</span>
      </div>
      <div class="flex items-start gap-2">
        <mdi-call-split class="mt-1 text-yellow-300 shrink-0" />
        <span class="text-yellow-300">同じクラスでも「ドメインの責務」と「永続化の責務」は分離</span>
      </div>
    </div>
  </div>
</div>

<!--
実際にDoctrineを導入すると、ドメインモデルはこのようになります。  
ご覧の通り、特に依存関係がないプレーンなクラス（POPO）になっています。 
[click]
コンストラクタはビジネス上必要な情報だけを引数で受け取り、IDや更新日時といったデータベースの永続化に必要となるメタデータは、privateで保持することができます。  
アプリケーション層からは見えない状態で隠蔽できているのが分かるかと思います。
-->

---

# <carbon-code-reference /> XML Mapping (インフラ層)

```xml {*}{lines:true}
<!-- src/Infrastructure/ORM/mapping/Product.dcm.xml -->
<doctrine-mapping>
  <entity name="App\Domain\Model\Product" table="products">
    <!-- 永続化に付随するメタデータをマッピングできる -->
    <id name="id" type="bigint" column="id">
      <options>
        <option name="unsigned">true</option>
      </options>
      <generator strategy="AUTO" />
    </id>
    <!-- ビジネスキー Custom Type でVOと変換 -->
    <field name="code" type="code" length="19" column="code" nullable="false" />
    <field name="name" type="string" length="255" />
    <field name="price" type="price" column="price" nullable="false"/>    
    <!-- 更新日時 -->
    <field name="updatedAt" type="datetime_immutable" column="updated_at" />
  </entity>
</doctrine-mapping>
```


<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'">
  <div 
    class="semantic-overlay semantic-positive p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500">
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-file-xml-box class="inline mr-2 text-green-300" />
      独立したXMLマッピング
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-gesture-tap-button class="inline mr-2" />
        手続きなし
      </span>
      <br />
      <span class="text-yellow-300 font-bold">
        <mdi-tag-text-outline class="inline mr-2" />
        AttributeでModelに埋め込みも可能
      </span>
      <br />
      <span class="text-yellow-300 font-bold">
        <mdi-call-split class="inline mr-2" />
        永続化責務はマッピングXMLで分離できる
      </span>
      <br />
    </p>
  </div>
</div>

<!--
先ほどはモデル側の説明でしたが、これをどうDBに永続化するかというと、このようなXMLで保存や読み出し方法を定義する形になります。  
フィールドの名前とカラムの名前、それに変換する型のtypeを定義する形になります。  
[click] このような独立したXML（またはAttribute）でマッピングでき、これによって永続化のための手続きを書く必要がなくなり、完全に責務を分離することができます。
-->

---

# <mdi-source-repository /> リポジトリ層の実装
雑な最小実装だとこんな感じ

```php {*}{lines:true}
<?php
final class DoctrineProductRepository implements ProductRepositoryInterface
{
    // Doctrineの永続化を管理する
    public function __construct(private EntityManagerInterface $em) {}

    // モデルの永続化
    public function save(Product $product): void {
        // XMLマッピングで定義した構造に基づいて、永続化される
        $this->em->persist($product);
        $this->em->flush();
    }

    // モデルの呼び出し
    public function findById(int $id): ?Product {
        // XMLマップ済みなため、Doctrineが自動で変換してくれる
        return $this->em->find(Product::class, $id);
    }
}
```

<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-positive p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-check-decagram-outline class="inline mr-2 text-green-300" />
      非常にシンプル！
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-swap-horizontal class="inline mr-2" />
        プロパティの詰め替えは不要
      </span>
      <br />
      <span class="text-green-200 font-semibold text-xl">
        <mdi-database-sync-outline class="inline mr-2" />
        永続化も基本persistしてflushするだけ
      </span>
      <br />
      <span class="text-yellow-300 font-bold">
        <mdi-cube-outline class="inline mr-2" />
        登場するModelは一つのドメインモデルのみ！
      </span>
      <br />
    </p>
  </div>
</div>

<!--
次にリポジトリ層の実装を見ていきます。  
こちらは最小の実装のイメージとなりますが、非常にシンプルなものです。  
[click] モデルの永続化と呼び出し共にプロパティの詰め替えは不要になります。  
基本的には persist して flush するだけです。  
登場するモデルも「一つのドメインモデルのみ」となり、非常にシンプルです。
-->

---

# <logos-laravel /> LaravelerによるDoctrineの1stインプレッション
狙い通りの効果はありそう

* <carbon-checkmark-filled /> リポジトリ層でのデータの詰め直しはなくなった
* <carbon-data-class /> ドメインモデルと Persistence モデルを同一クラスで運用しつつ、責務はマッピング層で分離できる

<!--
LaravelでEloquentに親しんできた私からの第一印象としては、「狙い通りの効果はありそうだ」と感じました。  
データの詰め替えはなくなり、ドメインモデルと永続化モデルを同一クラスで運用しつつ、責務はマッピング層で分離できます。
-->

---
layout: center
class: text-center
---

<div class="flex flex-col items-center gap-8">
  <div class="text-8xl font-extrabold tracking-tight">
    いい感じ
    <span class="ml-4">👍️</span>
  </div>
  <div class="text-7xl font-extrabold tracking-tight text-green-400">
    やっていき 💪
  </div>
  <div class="mt-4 text-2xl text-white/60">
    〜 <logos-doctrine /> Doctrine の旅、はじまる 〜
  </div>
</div>

<!--
これはいい感じだぞと。このまま開発を進めていきました。
-->

---

# <carbon-thumbs-up /> 見た目はかなり素直

```php
$user = $repo->findById($id);

$user->rename('新しいお名前');

$repo->save($user);
```
※説明の為に簡略化したコードになっています


<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-warning p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-trophy-outline class="inline mr-2 text-yellow-300" />
      これで勝ったのでは？
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-content-save-check-outline class="inline mr-2" />
        Eloquentの save 感覚に近い
      </span>
      <br />
      <span class="text-yellow-300 font-bold">
        <mdi-swap-horizontal-bold class="inline mr-2" />
        詰め替えコードも不要
      </span>
      <br />
    </p>
  </div>
</div>

<!--
呼び出し側もすごく素直です。  
findById で取得して、メソッドを呼んで、save する。  
[click]
Eloquentの save 感覚に近く、詰め替えコードも不要。これで勝ったな、と思いました。  
ここまでは本当に良かったんです。これならいけるじゃんと。。
-->

---

# <carbon-face-activated-add /> 結構いいかんじ…のはずだった
※ここから先は演出上の脚色を含みます。

ところが、ある日バグ報告が来る

<v-click at="1">
更新したつもりだった。。<br />
</v-click>

<v-click at="2">

<div class="speech-bubble">
「値引き更新したのに、商品が増えてませんか？」
</div>
<br />

```php
$product = $repo->findById($id);

$discounted = $product->applyCampaignPrice($campaignPrice);

$repo->save($discounted);
```
※説明の為に簡略化したコードになっています
</v-click>

<div 
  v-click="3"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 3 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-analysis p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-database-alert-outline class="inline mr-2 text-red-300" />
      UPDATE のつもりが INSERT？🤔
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-green-200 font-semibold text-xl">
        <mdi-table-row-plus-after class="inline mr-2 text-amber-300" />
        DB を見ると行が増えたり
      </span>
      <br />
      <span class="text-green-200 font-semibold text-xl">
        <mdi-alert-circle-outline class="inline mr-2 text-red-300" />
        一意制約エラーが発生する
      </span>
      <br />
    </p>
  </div>
</div>

<!--
ところが、ある日バグの報告が来ました。(このスライドから演出上の脚色が含まれています)  
[click] 更新したつもりが。。  
[click]「値引きの更新をしたはずなのに、商品レコードが増えてませんか？」という報告がありました。   
[click] 実はUPDATEしたつもりが、なぜかINSERTされてしまっていたんです。
-->

---
class: flex justify-center items-center gap-20 px40 text-xl
---

<div
  text-3xl
  absolute
  :class="$clicks < 1 ? 'text-white' : 'translate-y-[-1rem] scale-50 text-white/10'"
  transition
  duration-500
  ease-in-out
>
  <mdi-check-circle-outline class="inline mr-2 text-green-300" />
  モデルによっては更新がうまくいったり
</div>
<div flex flex-col items-center>
  <v-clicks>
    <div mt-20>
      <h1 flex items-center text="5xl!">
        <mdi-database-plus-outline class="mr-3 text-red-300" />
        更新でレコードが<br />増えたり。。
      </h1>
    </div>
  </v-clicks>
</div>

<!--
[click] 実際にDBを見ると、行が増えていたり、一意制約エラーが発生したりしていました。
-->

---
layout: two-cols-header
transition: fade-out
---

# <carbon-debug /> 何が起きているのか分からない

::left::

## <carbon-arrow-up-right /> update されるケース

```php
$user = $repo->findById($id);
$user->rename('新しいお名前');
$repo->save($user);
```

::right::

## <carbon-add-alt /> insert されるケース

```php
$product = $repo->findById($id);
$discounted = $product->applyCampaignPrice($campaignPrice);
$repo->save($discounted);
```

<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-warning p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-code-tags-check class="inline mr-2 text-sky-300" />
      コードレビューではどちらも自然に見える
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-alert-circle-outline class="inline mr-2 text-amber-300" />
        でも結果だけが違う
      </span>
      <br />
    </p>
  </div>
</div>

<!--
コードレビューで見てもですね、UPDATEされるケースと、なぜかINSERTされてしまうケース、どちらも非常に自然なコードに見えます。  
[click] も結果だけが違うんです。  
一体何が起きているのか、すごく分かりづらい状況でした。
-->

---
layout: two-cols-header
transition: fade-out
---

# <carbon-view /> おわかり頂けただろうか？。。

::left::

## <carbon-arrow-up-right /> update されるケース

```php {all|2-3}
$user = $repo->findById($id);
$user->rename('新しいお名前');
$repo->save($user);
```

<v-click at="1">

<div class="speech-bubble">
🙆 同じインスタンスをそのまま保存
</div>

</v-click>


::right::

## <carbon-add-alt /> insert されるケース

```php {all|2-3}
$product = $repo->findById($id);
$discounted = $product->applyCampaignPrice($campaignPrice);
$repo->save($discounted);
```
<v-clicks at=2>
<div class="speech-bubble">
🙅 新しいインスタンスを作って保存
</div>
</v-clicks>

<!--
皆様、おわかり頂けましたでしょうか？。。ということで答え合わせをします。  
[click] 左側のUPDATEされるケースは「同じインスタンスをそのまま保存」していました。  
[click]  一方、右側のINSERTされてしまったケースは、メソッドの中で「新しいインスタンスを作って保存」してしまっていたんです。
-->

---
layout: center
---

# 🤔 どうすればよかったのか？。。

<!--
では、どうすればよかったのでしょうか？
-->

---

# <carbon-task-add /> 色々悩みながらこの様に対処しました
save を辞めて DQL(Doctrine Query Language)で UPDATE 文を記述する

```php {*}{lines:true}
public function updatePrice(Code $code, Price $newPrice, \DateTimeImmutable $lastUpdatedAt): void
{
    // 1. DQLを組み立てる（プロパティが増えるたびにここが肥大化・複雑化する）
    $query = $this->em->createQuery(
        'UPDATE ' . Product::class . ' p 
         SET p.price.amount = :newPrice, 
             p.updatedAt = :now
         WHERE p.code = :code 
         AND p.updatedAt = :expectedUpdatedAt' // 楽観ロック
    );
    // 2. メソッドチェーンでパラメータをセット（非常に冗長）
    $query->setParameter('newPrice', $newPrice->amount)
          ->setParameter('now', new \DateTimeImmutable())
          ->setParameter('code', $code)
          ->setParameter('expectedUpdatedAt', $lastUpdatedAt); // 型やミリ秒精度の問題に泣かされる
    // 3. 実行
    $affectedRows = $query->execute();

    // 4. 楽観ロックの判定を自前で行う
    if ($affectedRows === 0) {
        throw new \RuntimeException("楽観ロック失敗：他者により更新、または同時刻の更新による不一致。");
    }
}
```

<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-analysis p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">一気にコード量が増える 🤮</h3>
  </div>
</div>

<!--
色々悩みながら、まずはこのようなコードで対処しました。  
saveを諦めてDoctrineのDQL（Query Builderのようなもの）を使って、手動でUPDATE文を書くようにしました。  
楽観ロックの処理もwhere条件に更新日時を付け加えるなど手探りで実装しようとしました。  
[click] 見てわかるように一気にコード量が増えてしまいました。  
これだとプロパティが増えるたびにDQLを修正したり、ユースケースが増えればDQLを量産する形になってしまいます。
-->

---

# <carbon-face-wink-filled /> もっと楽になると思ってたのに
DataMapper に来たのに、また手で更新処理を書いてる…

変更箇所だけ DQL で update
* <carbon-branch /> 更新系だけ DQL を個別に書き始める
* <carbon-growth /> ユースケースごとに update メソッドが増えていく  
* <mdi-lock-outline /> 楽観ロックの対応ってこれで良かったっけ？

<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-analysis p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-account-voice class="inline mr-2 text-red-300" />
      開発者からの悲鳴
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-head-question-outline class="inline mr-2 text-amber-300" />
        もっと楽にできないの？
      </span>
      <br />
    </p>
  </div>
</div>

<!--
DataMapperに来たのに、また手で更新処理を書く必要が出てきてしまいました。  
[click] 楽観ロックの対応も含めて複雑になってしまい、開発チームからは「もっと楽にできないの？」という悲鳴が上がりました。
-->

---

# <carbon-split-screen /> どうしてこうなった？
バグの原因は save メソッドそのものではなかった

```php {all|1,5}{lines:true}
public function applyCampaignPrice(Price $campaignPrice): self {
 if ($campaignPrice->amount >= $this->price->amount) {
  throw new \DomainException("キャンペーン価格は通常価格より安く設定してください");
  }
  return new self($this->code, $this->name, $this->price);
}
```

<v-click at=1>
<div class="speech-bubble">
immutable にしたことで、新しい Product を返していた
</div>
</v-click>

<div 
  v-click="2"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 2 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-analysis p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-eye-outline class="inline mr-2 text-sky-300" />
      Doctrine から見ると
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-source-fork class="inline mr-2 text-amber-300" />
        それは「更新対象」ではなく「別のオブジェクト」
      </span>
      <br />
    </p>
  </div>
</div>

<!--
深掘りしていくと、バグの原因はsaveメソッド側にはありませんでした。   
[click] 原因は、ドメインモデルの中でイミュータブル（不変）な実装をしていて、メソッドが自分自身を「新しいインスタンス」として返していたことでした。  
[click] これは、Doctrineから見るとそれは更新対象ではなく、「全く別のオブジェクト」として認識されてしまうんです。それがゆえにINSERTされていました。
-->

---

# <carbon-workflow-automation /> Doctrineは「同じオブジェクトの変化」を追いかける

- <carbon-document-import /> `findById()` で取った Entity は管理対象
- <carbon-update-now /> その同じインスタンスが変更されれば UPDATE できる
- <carbon-new-tab /> でも新しいインスタンスを返すと追跡対象から外れる
- <carbon-error /> だから save しても UPDATE のつもりが INSERT 側に倒れる

<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-positive p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-link-off class="inline mr-2 text-red-300" />
      以下の相性がよくなかった
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-shield-check-outline class="inline mr-2 text-emerald-300" />
        Doctrine は悪くない
      </span>
      <br />
      <span class="text-green-200 font-semibold text-xl">
        <mdi-source-branch-sync class="inline mr-2 text-sky-300" />
        Doctrine が前提としている変更モデル
      </span>
      <br />
      <span class="text-green-200 font-semibold text-xl">
        <mdi-lock-outline class="inline mr-2 text-amber-300" />
        immutable な設計
      </span>
      <br />
    </p>
  </div>
</div>

<!--
Doctrineは、findByIdで取得したのと同じインスタンスが更新されればUPDATEできますが、新しいインスタンスが渡されると追跡対象から外れてしまい、INSERT側に倒れるという挙動になります。  
[click] つまり、Doctrineが前提としている変更モデルと、私たちのイミュータブルにしたいという設計の相性が悪かった、というのが根本原因でした。
-->

---

# <carbon-cics-system-group /> Unit of Workというもの
同じオブジェクトの変化を追いかけるのは Unit of Work の中核的機能

* <carbon-event-schedule /> 業務トランザクション中に触ったオブジェクトを覚えて、何を最後に DB へ書くかを調整する仕組み
* <carbon-function-math /> 以下の機能を有しています
  * 新規追加、削除、更新の取りまとめ
  * 書き込み順序の調整（集約ルートの更新）
  * 同時実行の問題への対処（楽観ロック等）
  * 同じレコードを同じインスタンスとして扱う仕組み( [Identity Map](https://martinfowler.com/eaaCatalog/identityMap.html) )

<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-analysis p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-rocket-launch-outline class="inline mr-2 text-sky-300" />
      Unit of Workの恩恵を最大限受けるには
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-chart-timeline-variant class="inline mr-2 text-amber-300" />
        Entityをmutableにして変更を追跡させる必要がある
      </span>
      <br />
    </p>
  </div>
</div>

<!--
ここで重要になるのが「Unit of Work」という概念です。  
これは、トランザクション中に触ったオブジェクトの変更を追跡し、最後にまとめてDBへ書き込む調整をしてくれる機能です。  
他にも、参照関係を見てDB制約でコケない順番に自動的にSQLを並べたり、楽観ロックのエラー検知をしたり、同じレコードを1つのインスタンスとして扱う機能も含まれています。  
[click] このUnit of Workの恩恵を最大限受けるには、エンティティを「ミュータブル（可変）」にして変更を追跡させる必要があります。
-->

---

# <carbon-arrows-horizontal /> つまり、衝突していたのは設計思想だった
歴史は繰り返す。DataMapper パターンも設計思想がある

- <carbon-data-format /> ドメインは immutable にしたい
- <carbon-change-catalog /> Doctrine は mutable な変更追跡が前提となる
- <carbon-zoom-area /> そのズレを埋めるために DQL や個別更新が増えた
- <carbon-warning-square /> 便利になるはずが、別の複雑さを呼び込んだ

<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-positive p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-call-split class="inline mr-2 text-sky-300" />
      DataMapper を選んだつもりでしたが、、
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-source-branch-sync class="inline mr-2 text-amber-300" />
        実際には Unit of Work の作法まで引き受ける必要があった
      </span>
      <br />
    </p>
  </div>
</div>

<!--
つまり、ドメインをイミュータブルに保ちたい私たちの設計思想と、変更追跡のためにミュータブルを前提とするDoctrineの思想が衝突していたということです。  
その溝を埋めるためにDQLや個別の更新が増え、期待した単純化の代わりに新たな複雑さが生まれてしまっていました。  
[click] DataMapperを選んだつもりでしたが、実際にはそれと不可分な「Unit of Work」の作法まで引き受ける必要がありました。  
ドメインをイミュータブルにしたいという思想と、Doctrineのミュータブルな変更追跡が根本で衝突していたわけです。
-->

---

# <carbon-rule /> なぜイミュータブルにしたいのか？
状態変化はバグの温床である

ミュータブルなオブジェクトが引き起こす問題

- <mdi-alert-outline /> 予期せぬ副作用
- <carbon-time /> 時間的結合（メソッドの呼び出し順序に依存する壊れやすさ）
- <carbon-connection-signal /> スレッド安全性の欠如（並行処理でのデータ競合）
- <carbon-search-advanced /> 変更追跡の困難さ（どこで状態が変わったか追いにくい）  

コードの実行順序（時間軸）を脳内でシミュレーションしなければならず認知負荷が高い  
その関数（またはクラス）の中身だけを読めば、何が起きるか 100%理解できる状態が崩れる


DDD の文脈では、Eric Evans が Value Object はイミュータブルであるべきと触れられています

<!--
そもそも、なぜイミュータブルにしたかったのか？ですが、それは「状態変化はバグの温床になる」からです。  
予期せぬ副作用や、メソッドの呼び出し順序への依存などを防いで、認知負荷を下げたいという意図がありました。  
状態が変わる可変な設計だと、実行順序を頭の中で追う必要があり、総じて認知負荷が高くなります。結果として、関数やクラス単体で読んでも挙動を把握しにくくなります。   
DDDの文脈でも、Eric EvansがValue Objectでのイミュータブルの重要性を述べていたりします。
-->

---

# <carbon-locked /> イミュータブルな設計とは？
関数型ドメインモデリングのアプローチ

1. 不正な状態を「表現不可能」にする<carbon-data-check />   
強力な型システム（代数的データ型など）を活用して、 **「ビジネスルールに反する状態は、そもそもコンパイルエラーになる」** ように設計する  
コンストラクタで制約を定義して、不正な状態を作れない様にします  
直接プロパティを書き換えるインターフェースは定義しません
2. ビジネスロジックと「副作用」を完全に分離する<mdi-call-split />  
データベースにもネットワークにも依存しない「純粋な関数」の集まりとして定義します  
「現在の状態」と「コマンド」を受け取り、「新しい状態」または「発生したイベント」を返却します

<!--
イミュータブルな設計では、不正な状態をそもそも作れないようにコンストラクタ等で制約をかけます。  
直接プロパティを書き換えるインターフェースは持たず、新しい状態を返却するアプローチをとります。  
このような形にしていくと、単なる値の上書きではなく、「何が起きたか」という変更をドメインイベントとして明示しやすくなります。
-->

---

# <carbon-security /> イミュータブルの強制ギブスとしてのread only class
手っ取り早くイミュータブルな設計を担保させるのであれば、read only class が楽

PHP の言語機能で制約を強制できる

readonly クラスは、PHP 8.2 から導入されました
readonly class は、宣言された全プロパティを暗黙的に readonly にします

<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-positive p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-clipboard-check-outline class="inline mr-2 text-sky-300" />
      レビューが楽
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-shield-check-outline class="inline mr-2 text-amber-300" />
        カッチリした設計になる
      </span>
      <br />
    </p>
  </div>
</div>

<div 
  v-click="2"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 2 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-positive p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-noodles class="inline mr-2 text-red-300" />
      ワシワシ！
    </h3>
  </div>
</div>

<!--
最近ですと、PHP 8.2から導入された readonly class が便利で、強制的にイミュータブルなモデルにすることができます。  
[click] 言語機能としてこれを強制でき、レビューも楽で、かっちりした設計にすることができます。 
[click] 非常に「ワシワシ」とした食感が楽しめるかなと思っていますw
-->

---

# <carbon-machine-learning-model /> なんとかインチキできんのか
immutable なモデルのまま Unit of Work は使えないのか？

<v-click>
できるにはできる！
</v-click>

<v-click>

* <carbon-data-view-alt /> リポジトリ側内で find して Unit of Work させる
* <carbon-wikis /> リフレクションでゴニョゴニョさせる

</v-click>

<!--
やっぱりイミュータブルが良い！ということでイミュータブルなモデルのままでUnit of Workを使う裏技はないのか？というのを検討しました  
[click] 一応、できるにはできます！  
[click] リポジトリ内でfindして、リフレクションを使ってゴニョゴニョする方法です。
-->

---

# <carbon-scales /> 異論は認めます
インフラ層でリフレクションすれば出来なくもない

```php {*}{lines:true}
public function updatePrice(Code $code, Price $newPrice): void
{
  /** @var Product|null $managed */
  $managed = $this->findOneBy(['code' => $code]);

  if ($managed === null) {
    throw new \RuntimeException('対象の商品が存在しません。');
  }

  // Repository層
  $classMetadata = $this->em->getClassMetadata(Product::class);
  // privateなプロパティを強引に書き換える「おまじない」
  $classMetadata->setFieldValue($managed, 'price', $newPrice); 
  $classMetadata->setFieldValue($managed, 'updatedAt', new DateTimeImmutable());

  // 楽観ロックも動きます
  $this->em->flush();
}
```

<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-warning p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-api-off class="inline mr-2 text-red-300" />
      ClassMetadata::setFieldValue()は非公開なI/F?
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-alert-circle-outline class="inline mr-2 text-amber-300" />
        Breaking Change怖い
      </span>
      <br />
      <span class="text-yellow-300 font-bold">
        <mdi-file-search-outline class="inline mr-2 text-amber-300" />
        汎用的に実装すると、変更検知が大変
      </span>
      <br />
      <span class="text-yellow-300 font-bold">
        <mdi-source-branch-sync class="inline mr-2 text-amber-300" />
        集約の更新となると更に複雑になる
      </span>
      <br />
    </p>
  </div>
</div>

<!--
リポジトリ内でリフレクションを使って、強制的にprivateなプロパティを上書きしてしまうという荒業です。  
[click]  動くには動くんですが、非公開なインターフェースに依存するリスクがありますし、変更検知を汎用的に実装するのが難しくなります。  
特に集約ルートの更新となると煩雑になりすぎて、これは「筋が悪い」と判断しました。
-->

---

# <carbon-edit /> モデルをミュータブルにするのが良さそう

````md magic-move {lines: true}
```php
// src/Domain/Model/Product.php
class Product {
    // 永続化に付随するメタデータ（ハイドレーションでセット）
    private readonly　int $id = null;
    private readonly \DateTimeImmutable $updatedAt;
    
    public function __construct(
        private readonly Code $code, // ビジネス上の識別子
        private readonly　string $name,
        private readonly　Price $price
    ) {
        if (mb_strlen($name) < 3)
            throw new \InvalidArgumentException("商品名は3文字以上必要です");
    }

    public function applyCampaignPrice(Price $campaignPrice): self {
        if ($campaignPrice->amount >= $this->price->amount) {
            throw new \DomainException("キャンペーン価格は通常価格より安く設定してください");
        }
        return new self($this->code, $this->name, $this->price);
    }
}
```
```php
// src/Domain/Model/Product.php
class Product {
    // 永続化に付随するメタデータ（ハイドレーションでセット）
    private int $id = null;
    // 楽観ロック用
    private int $version;
    private \DateTimeImmutable $updatedAt;
    
    public function __construct(
        private readonly Code $code, // ビジネス上の識別子（キー項目は変更されてはいけないのでreadonlyのまま）
        private string $name,
        private Price $price
    ) {
        if (mb_strlen($name) < 3)
            throw new \InvalidArgumentException("商品名は3文字以上必要です");
    }

    public function applyCampaignPrice(Price $campaignPrice): void {
        if ($campaignPrice->amount >= $this->price->amount) {
            throw new \DomainException("キャンペーン価格は通常価格より安く設定してください");
        }
        $this->price = $campaignPrice;
    }
}
```
````

<!--
チームで議論した結果、現実的な解決策としては、「エンティティモデルをミュータブルにする」のが最もシンプルで良いという結論に至りました。 
[click] 一部のプロパティを書き換え可能にして、ビジネスルールの制約はメソッド内のガード節で担保する形にしました。  
元のコードでは新しいインスタンスを返却していましたが、素直にpriceを書き換えています。
-->

---

# <carbon-version /> マッピングXMLにversion追加も忘れずに

````md magic-move {lines: true}
```xml
<!-- src/Infrastructure/ORM/mapping/Product.dcm.xml -->
<doctrine-mapping>
  <entity name="App\Domain\Model\Product" table="products">
    <!-- 永続化に付随するメタデータをマッピングできる -->
    <id name="id" type="bigint" column="id">
      <options>
        <option name="unsigned">true</option>
      </options>
      <generator strategy="AUTO" />
    </id>
    <!-- ビジネスキー Custom Type でVOと変換 -->
    <field name="code" type="code" length="19" column="code" nullable="false" />
    <field name="name" type="string" length="255" />
    <field name="price" type="price" column="price" nullable="false"/>    
    <!-- 更新日時 -->
    <field name="updatedAt" type="datetime_immutable" column="updated_at" />
  </entity>
</doctrine-mapping>
```
```xml
<!-- src/Infrastructure/ORM/mapping/Product.dcm.xml -->
<doctrine-mapping>
  <entity name="App\Domain\Model\Product" table="products">
    <!-- 永続化に付随するメタデータをマッピングできる -->
    <id name="id" type="bigint" column="id">
      <options>
        <option name="unsigned">true</option>
      </options>
      <generator strategy="AUTO" />
    </id>
    <!-- ビジネスキー Custom Type でVOと変換 -->
    <field name="code" type="code" length="19" column="code" nullable="false" updatable="false" />
    <field name="name" type="string" length="255" />
    <field name="price" type="price" column="price" nullable="false"/>
    <field name="version" type="integer" column="version" nullable="false" version="true">
      <options>
        <option name="unsigned">true</option>
        <option name="default">1</option>
        <option name="comment">バージョン(楽観ロック用)</option>
      </options>
    </field>
    <!-- 更新日時 -->
    <field name="updatedAt" type="datetime_immutable" column="updated_at" />
  </entity>
</doctrine-mapping>
```
````

<!--
モデルをミュータブルにしたことで、Doctrineの機能にしっかり乗っかることができるようになりました。  
[click]  こちらのマッピングXMLに version を1つ追加するだけで、簡単に楽観ロックも実現できるようになりました。
-->

---

# <carbon-ref-evapotranspiration /> リポジトリの再定義

````md magic-move {lines: true}
```php
final class DoctrineProductRepository implements ProductRepositoryInterface
{
    public function __construct(private EntityManagerInterface $em) {}

    // モデルの永続化
    public function save(Product $product): void {
        $this->em->persist($product);
        $this->em->flush();
    }

    // モデルの呼び出し
    public function findById(int $id): ?Product {
        return $this->em->find(Product::class, $id);
    }
}
```
```php
final class DoctrineProductRepository extends EntityRepository implements ProductRepositoryInterface
{
    public function __construct(private EntityManagerInterface $em) {}

    // 新規作成用
    public function create(Product $product): void {
        // 新規エンティティ以外の場合は不正操作として扱う
        if ($this->em->getUnitOfWork()->getEntityState($product) !== UnitOfWork::STATE_NEW) {
            throw new \LogicException("新規エンティティ以外はcreateできません。");
        }
        
        $this->em->persist($product);
        $this->em->flush();
    }

    // 更新用
    public function update(Product $product): void {
        // UoW管理下（このリクエストで取得済み）でない場合は不正操作
        if ($this->em->getUnitOfWork()->getEntityState($product) !== UnitOfWork::STATE_MANAGED) {
            throw new \LogicException("管理外のエンティティはupdateできません。先に取得してください。");
        }
        // UoWが差分を検知し、必要であれば最適化されたUPDATE文を発行する
        $this->em->flush();
    }
}
```
````

<div 
  v-click="2"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 2 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-decision p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-call-split class="inline mr-2 text-sky-300" />
      CreateとUpdateを分離
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-check-decagram-outline class="inline mr-2 text-amber-300" />
        完全に共通化できる
      </span>
      <br />
      <span class="text-yellow-300 font-bold">
        <mdi-source-repository class="inline mr-2 text-amber-300" />
        EntityRepositoryを継承することで基本的なQueryもカバー
      </span>
      <br />
    </p>
  </div>
</div>

<!--
リポジトリも再定義していきます。  
[click] リポジトリの実装も見直して、CreateとUpdateを明確に分離しました。  
[click] メソッドを分割した理由としては、先程の例の様に誤って、updateしたつもりがinsertにならないようにする為です。  
単純なCRUDは共通化することができ、またEntityRepositoryを継承することで基本的なQueryもカバーすることができて、コード量がかなり削減することができました
-->

---

# <carbon-chart-line-data /> Unit of Work（ミュータブル）ってどうよ？

<Transform :scale="1.4">

* <carbon-development /> リポジトリの実装コストを下げれることができた  
create と update と delete は Trait を use するだけ
* <carbon-category-new-each /> リポジトリの責務が明確になった様な気がする
* <carbon-flow-data /> 集約ルートの更新が楽になった分、集約の制約に集中できるようになった  
子レコードが追加や更新されたら、親のカラムとの整合性を保つように更新する

</Transform>

<!--
ドメインをミュータブルにしてUnit of Workを使ってみた所感となります。  
リポジトリの実装コストは大幅に下がったのと、リポジトリの実装が薄くなり、余計なことが出来なくなったので、責務が明確になった気がします。  
また、Unit of Workで集約ルートをうまく扱えるようになって、積極的にモデルを集約するマインドとなり「親レコードと子レコードの整合性を保つ」といった集約の制約やビジネスルールに集中できるようになりました。
-->

---

# <carbon-pause-future /> イミュータブルは諦めるのか？

* <carbon-badge /> ValueObject はイミュータブルな実装にする  
積極的に Value Object を定義していくスタイル
* <carbon-edge-enhancement-01 /> 言語仕様や型での制約以外のガードレールを整備する

<!--
では、イミュータブルの良さは全て諦めたのかというと、そうではありません。  
Value Object（値オブジェクト）は引き続きイミュータブルな実装を徹底しています。  
言語仕様での強制だけでなく、他のガードレールを整備することにしました。
-->

---

# <carbon-event /> イミュータブルは“解きたい課題”のための手段


* <carbon-checkmark-filled-warning /> mutable にすると Unit of Work とは<span v-mark="{ at: 1, color: 'orange', type: 'circle' }">噛み合う</span>
* <carbon-view-off /> ただし<span v-mark="{ at: 2, color: 'orange', type: 'underline' }">「何が起きたか」が状態変化の陰に埋もれやすい</span>
* <carbon-categories /> 同じ最終状態でも、値引きなのか、手動補正なのか、キャンペーン終了なのかは別の出来事
* <mdi-calendar-outline /> <span v-mark="{ at: 3, color: 'orange', type: 'highlight' }">ドメインイベント</span>を明示しないと、後から業務の意図を取り出しにくい

<!--
ここで mutable に寄せれば解決！とならず、無視できない要素が残り続けると考えています。  
[click] ミュータブルにするとUnit of Workとは噛み合い、ロジックとして成立します。  
[click] しかし「何が起きたか（経緯）」が状態変化の意味がぼやけてしまうと感じています。    
[click] 値段という一つの状態変化をとっても、それが値引きなのか、手動補正なのかという意図を見失わない設計が求められます。  
[click] 単なる状態の保存ではなく、「出来事」をイベントとして扱うような設計にしていきたいと考えています。前のコード例のaplyCampaignPriceの様に、意思を持った名前にして制約も設けたいのです。
-->

---

# <mdi-scale-balance /> 管理したいのは「結果」か「経緯」か

<div class="w-full h-90 grid grid-cols-2 gap-8 mt-10">
  <div class="flex flex-col p-6 bg-gray-800/70 rounded-lg shadow-xl border-l-4 border-red-500 transition duration-500 ease-in-out">
    <h3 class="text-3xl font-bold text-red-300 mb-4">
      <mdi-database-cog-outline class="inline mr-2 text-red-200" />
      State Sourcing × CRUD<br />(Unit of Work)
    </h3>
    <ul class="list-disc list-inside text-xl space-y-3 text-left">
      <li><mdi-shield-check-outline class="inline mr-2 text-red-200" />現在状態を正しく保つのが得意</li>
      <li><logos-doctrine class="inline mr-2" />Doctrine と相性がよい</li>
      <li><mdi-cash-multiple class="inline mr-2 text-red-200" />実装コストを抑えやすい</li>
    </ul>
  </div>
  <div
    v-click="1"
    class="flex flex-col p-6 bg-gray-800/70 rounded-lg shadow-xl border-r-4 border-green-500 transition duration-500 ease-in-out"
    :class="$clicks < 1 ? 'opacity-0 scale-90' : 'opacity-100 scale-100'"
  >
    <h3 class="text-3xl font-bold text-green-300 mb-4">
      <mdi-calendar-star class="inline mr-2 text-green-200" />
      Event Sourcing × CQRS
    </h3>
    <ul class="list-disc list-inside text-xl space-y-3 text-left">
      <li><mdi-star-four-points-outline class="inline mr-2 text-green-200" />起きた出来事を一級市民として扱える</li>
      <li><mdi-file-search-outline class="inline mr-2 text-green-200" />監査、再計算、後追い分析に強い</li>
      <li><mdi-chart-line-variant class="inline mr-2 text-amber-300" />ただし学習コスト、運用コスト、設計コストは上がる</li>
    </ul>
  </div>
</div>
<div
  v-click="2"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 2 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div class="semantic-overlay semantic-decision p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500">
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-scale-balance class="inline mr-2 text-yellow-300" />
      一律どっちに寄せるというものではない
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-tune-variant class="inline mr-2" />ケースバイケースで取り組むべき内容
      </span>
      <br />
      <span class="text-yellow-300 font-bold">
        <mdi-noodles class="inline mr-2" />コシが求められる
      </span>
    </p>
  </div>
</div>

<!--
これはORMの使い方の問題ではなくて、状態を主役（ステートソーシングを意識してCRUD）にしたいのか？  
[click]「出来事」を主役に（Event Sourcingにフォーカス）するのかというアーキテクチャの分岐点となる設計判断です。  
[click] ただこれについては一律にどちらかに寄せるのではないと考えます。  
ケースバイケースで選ぶ、ある意味「コシ」のある判断・設計が求められるものだと思っています。  
つまり、私たちが悩んでいたのはORMの使い方だけではなくて、その先にあるドメインに向き合う際に発生する設計判断でした。
-->

---

# <carbon-result-draft /> 【Decision】現実解としては CRUD を選ぶ
全てをイベントとして処理するのは現実的ではない

柔らかい設計の余地がある部分を Copilot の力で調整を行う  
既存の Linter ではカバーしきれないモデリングに対してもガードレールを整備できる

* <carbon-rule-filled /> instruction を整備して以下をルール化
  * 単純は setter は禁止  
  メソッド名は業務用語で動詞+対象にして、ドメインロジックを表現する  
  1 つのビジネスルール変更 = 1 メソッド。複数の制約が必要な場合は分ける  
  変更時に不正な状態が出来ないように、メソッド冒頭にまとめてガード節で validation してから処理を実行
  * <carbon-tag /> キー項目は read only のまま
  * <carbon-locked /> 値オブジェクトはすべて readonly 必須
  * <carbon-parent-child /> 集約の子エンティティは親経由のアクセスのみに限定

<div 
  v-click="1"
  class="absolute inset-0 z-20 flex items-center justify-center bg-black/50 backdrop-blur-sm transition duration-700 ease-in-out"
  :class="$clicks < 1 ? 'opacity-0 pointer-events-none' : 'opacity-100 pointer-events-auto'"
>
  <div 
    class="semantic-overlay semantic-ask p-10 backdrop-blur-lg rounded-2xl shadow-2xl transform scale-125 transition duration-500"
  >
    <h3 class="text-5xl font-extrabold text-white mb-4">
      <mdi-noodles class="inline mr-2 text-yellow-300" />
      弾力・コシだけでない（設計の軸）
    </h3>
    <p class="text-2xl text-green-300 font-bold tracking-wide leading-relaxed">
      <span class="text-yellow-300 font-bold">
        <mdi-water-outline class="inline mr-2" />
        なめらかさ（つるみ）・喉ごしも重要（プロセスの軸）
      </span>
      <br />
    </p>
  </div>
</div>

<!--
今回の私たちのプロジェクトでは、、現実解としてはイベントソーシングではなく、CRUD（ステートソーシング）的なアプローチを採用することにしました。  
その上で、単純なsetterは禁止する、Value Objectはreadonly必須にするなど、既存のLinterでは防げない部分を、GitHub CopilotなどのAIのインストラクション（指示）を用いてガードレール化し、運用でカバーする方針にしました  
[click] こうしたAIの力を借りて、開発プロセスに組み込むというのは、開発体験の向上に繋がります。開発プロセス全体を滑らかにする（つるみを出して喉越しよくする）ということも、今後の開発では重要になってくるかと思っています。  
設計はコシよく、プロセスはなめらかにしたいものですｗ
-->

---
layout: fact
---

# <carbon-report-data /> 総論
DataMapperパターンはつるっと導入できるものではない<br/>  
以外と「コシ」があり、考えさせられるテーマがそこにはありました<br/>  
ただ結果としてドメインに集中して向き合えるようになった！

<!--
つるっとDataMapperパターンを導入できるものかと考えていましたが、思っていたよりも「コシ」があり、考えさせられるテーマがそこにはありました。  
色々と衝突はありましたけれども、結論としては、ドメインのビジネスルールに集中して向き合える環境というものが、Doctrineを使うことで実現できるようになったと感じています。
-->

---

# <carbon-notebook-reference /> まとめ その１

* <carbon-map /> Data Mapper  
ドメインオブジェクトと DB の間にマッパー層を置き、永続化の都合をドメインから切り離す設計パターン  
「ドメインを守る」ための分離戦略。  
ただし実運用では Unit of Work の作法まで含めて設計する必要があった  
* <carbon-cics-program /> Unit of Work  
変更をまとめて、最後に一括コミットする"賢い秘書"  
* <mdi-database-cog-outline /> Doctrine で「Eloquent ライクな体験」を求めると、Data Mapper の思想と摩擦が生じる  
Eloquent（ActiveRecord パターン）は「変更したらすぐ SQL を投げる」   
Unit of Work は「変更を全部メモしておいて、最後にまとめて SQL を投げる」　
* <carbon-events /> 完全にイミュータブルで Unit of Work にする場合は、イベント駆動に切り替えると良さそう  
Insert だけになるので、Unit of Work の恩恵はそれほどなくなるけれど

<!--
本日のまとめです。  
Data Mapperは、ドメインとDBの間にマッパーを置いて、永続化の都合をドメインから切り離す設計です。  
ポイントは「分離して終わり」ではなく、実運用ではUnit of Workの前提までセットで設計しないと、更新の挙動でつまずくというところが注意点です。  
Unit of Workは「変更を全てメモしておいて、最後にまとめてSQLを投げる「賢い秘書」的な立ち位置です。   
DoctrineでEloquentライクな手軽さを求めると摩擦が生じます。  
もし完全にイミュータブルな設計を貫くなら、いっそイベント駆動に切り替えるような考え方の方が相性が良いと感じています。
-->

---

# <carbon-checkmark-filled /> まとめ その２
結論「クリーンアーキテクチャでは Eloquent よりも Doctrine のほうが相性がよい」

1. <carbon-data-class /> ドメインモデルと永続化モデルを同一クラスで運用しつつ、責務はマッピング層で分離できる
2. <carbon-view-next /> POPO の公開インターフェースを保ったまま、永続化情報は非公開で保持できる
3. <carbon-scales-tipped /> 完全分離ではなく、責務分離を維持した同居という現実解

<!--
最終的な結論として、「クリーンアーキテクチャにおいては、EloquentよりもDoctrineの方が相性が良い」と感じています。  
Laravel/EloquentのRails Wayな生産性の高さも素晴らしいですが、ドメインをシンプルに保つ思想とは根本でぶつかりやすいためです。 
ドメインの永続化の知識はマッピング層に閉じ込めることができます。  
永続化の知識はドメインの公開インターフェースからは隠すこともできます。  
ドメインモデルと永続化モデルの完全な分離ではなく、永続化の責務を分離して、モデルとしては同居しているイメージです。  
この「責務分離を維持した同居」というアプローチが、今回のプロジェクトにおける私たちの現実解でした。
-->

---
layout: end
zoom: 2.0
---

ご清聴ありがとうございました

<!--
ご清聴ありがとうございました。
-->


---
layout: center
class: text-center
transition: fade
---

# <icon-park-outline-pigeon /> あなたの技術が、社会を変える 🌎✨

<div mt-10 text-3xl font-bold>
  <span class="text-yellow-300">教育・福祉領域×Tech</span> で、<br />
  <span class="text-green-300">「障害のない社会をつくる」</span>挑戦をしませんか？
</div>

<div class="flex justify-center items-center gap-12 mt-16">
  
  <div class="flex flex-col items-center">
    <h3 class="text-3xl font-extrabold text-white mb-4">
      🚀 募集要項はこちら
    </h3>
    <div class="p-4 bg-white rounded-lg shadow-2xl">
      <QRCode text="https://herp.careers/v1/litalico/requisition-groups/ea1f6b9d-88b7-4254-a06a-8cfdd83bd575" class="w-36 h-36" />
    </div>
    <a href="https://herp.careers/v1/litalico/requisition-groups/ea1f6b9d-88b7-4254-a06a-8cfdd83bd575" target="_blank" class="text-xl mt-4 font-bold text-blue-400 hover:text-blue-300">
      詳細を見る（HERP）
    </a>
  </div>

  <div class="flex flex-col items-center border-l-2 border-gray-600 pl-12">
    <h3 class="text-3xl font-extrabold text-white mb-4">
      🗣️ 登壇者へ話しかける
    </h3>
    <div class="p-6 bg-yellow-800/20 border-2 border-yellow-500 rounded-lg shadow-lg">
      <p class="text-xl font-bold text-white">
        ご興味のある方は、ぜひ<br />
        <span class="text-yellow-300 text-4xl">katzumi</span> まで<br />
        お気軽にお声がけください！
      </p>
    </div>
  </div>

</div>

<!--
最後に宣伝させてください。  
私たちは「障害のない社会をつくる」チャレンジをしています。ご興味のある方は、ぜひお気軽にお声がけください！
-->

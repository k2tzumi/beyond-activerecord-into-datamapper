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

PHPカンファレンス香川 2026 May 9, 2026.  
v0.0.1  
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
TODO

* TODO

<!--
本日は、TODO
-->


---
layout: end
zoom: 2.0
---

ご清聴ありがとうございました

<!--
ご清聴ありがとうございました。
-->

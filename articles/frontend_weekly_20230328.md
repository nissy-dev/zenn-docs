---
title: "React の新しい公式ドキュメントが公開されたなど : Cybozu Frontend Weekly (2023-03-28号)" # 目立ったニュースを選ぶ
emoji: "🌸" # お好きな絵文字を
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["CybozuFrontendWeekly", "frontend"]
published: true
publication_name: "cybozu_frontend"
---

こんにちは! サイボウズ株式会社フロントエンドエンジニアの [nissy](https://twitter.com/nissy_dev) です。

# はじめに

サイボウズでは毎週火曜日に Frontend Weekly という「1 週間の間にあったフロントエンドニュースを共有する会」を社内で開催しています。

今回は、2023/03/28 の Frontend Weekly で取り上げた記事や話題を紹介します。

# 取り上げた記事・話題

## Astro 2.1 | Astro

https://astro.build/blog/astro-210/

Astro 2.1 のリリースです 🎉 次のような内容が新しく実装されました。

- ビルドインでの画像最適化 (experimental)
- [Markdoc](https://markdoc.dev/) のサポート (experimental)
- `astro check` コマンドの `watch` フラグ
- dynamic routes における `params` や `props` の型推論の改善

## Picture-in-Picture for any Element, not just - Chrome Developers

https://developer.chrome.com/docs/web-platform/document-picture-in-picture/

`<video>` 要素以外でも Picture-in-Picture を可能にする [Document Picture-in-Picture API](https://wicg.github.io/document-picture-in-picture/) の紹介記事です。ブログに紹介されている [ポモドーロタイマー](https://developer.chrome.com/docs/web-platform/document-picture-in-picture/#pomodoro) のデモを起点に、ユースケースが色々考えられて面白そうとの声が社内でもあがりました。

## W3C launches beta of its new website

https://www.w3.org/blog/2023/02/w3c-launches-beta-of-its-new-website/

W3C の[新しいサイト](https://beta.w3.org/)がベータ版として公開されました 🎉 デザインやアクセシビリティの改善が行われており、日本語や中国語版のサイトも今後公開する予定とのことです。

## Today, we’re thrilled to launch react.dev, the new home for React and its documentation

https://twitter.com/reactjs/status/1636441676506906626?s=20

しばらくの間、ベータ版として更新していた React のドキュメントが、[react.dev](https://react.dev/) として正式なリリースを迎えました 🎉 ドキュメントの内容が刷新されたことに伴って、図やイラストなども改善され、Playground 付きのチュートリアルも多く追加されています。

## React Labs: What We've Been Working On – March 2023 – React

https://react.dev/blog/2023/03/22/react-labs-what-we-have-been-working-on-march-2023

2023 年 3 月時点における React 内部で活発に研究や開発が行われているプロジェクトの紹介です。Next.js に導入された React Server Components をはじめとして、Asset Loading や Document Metadata の改善、React Conf 2021 で紹介された React Forget の続報などについて報告しています。

## Announcing the ESLint Community GitHub organization

https://eslint.org/blog/2023/03/announcing-eslint-community-org/

ESLint Community が 公式の [GitHub organization](https://github.com/eslint-community) を持つようになったという記事です。特定の GitHub や npm アカウントに依存した ESLint プラグインの開発を減らし、継続的なエコシステムの運営を目指しているようです。

## The 2023 ESLint User Survey is now live!

https://twitter.com/geteslint/status/1638643024262725632?s=20

ESLint がユーザー向けのアンケートを行なっています。OSS へフィードバックを送る機会はあまりないと思うので、この機会に送ってみるのはいかがでしょうか！

## Node.js compatibility for Cloudflare Workers – starting with Async Context Tracking, EventEmitter, Buffer, assert, and util

https://blog.cloudflare.com/workers-node-js-asynclocalstorage/

Cloudflare Workers が Node.js との互換性の改善を進めていて、Buffer などの Node.js の core API の一部が利用できるようになったという記事です。`nodejs_compat` フラグを通じて利用することが可能であり、今後は利用できる core API を増やしていく予定のようです。

## We've been working on improving `next export`

https://twitter.com/timneutkens/status/1636693508223270912?s=20

Next.js 13 で導入された App Router (app directory) を有効にしている場合でも `next export` を利用できるように改善を進めているというツイートです。すでに実装は Next.js の canary バージョンに入っているので、stable な機能として利用できる日も近いようです。

## These proposals advanced @TC39 this week 🎉

https://twitter.com/robpalmer2/status/1639042932559970304?s=20

TC39 の会議が行われたようです。Stage 2 になっていた Import Assertions のプロポーザルが Import Attributes として Stage 3 に戻ってくるなどの変更がありました。

## ビギナー向け エッジランタイムのすすめ | エッジランタイムを意識した開発をはじめよう

https://speakerdeck.com/aiji42/biginaxiang-ke-etuzirantaimunosusume-etuzirantaimuwoyi-shi-sitakai-fa-wohazimeyou

Cybozu にも[交換留学](https://www.a-tm.co.jp/news/34656/)で来てくださっていた上島さん ([@aiji42_dev](https://twitter.com/aiji42_dev)) によるエッジランタイムの入門者向けのスライドです。エッジランタイムとは何かというところから説明が始まり、実サービスでの経験を踏まえたユースケースや留意点などを紹介しているスライドになっています。

## 6 CSS snippets every front-end developer should know in 2023

https://web.dev/6-css-snippets-every-front-end-developer-should-know-in-2023/

2023 年に全フロントエンド開発者が知っておくべき 6 つの CSS スニペットという記事です。Container Query や `scroll-*` プロパティを用いたスクロール挙動の制御などの Tips が紹介されていました。

## Add the <search> element

https://github.com/whatwg/html/pull/7320

WAI-ARIA のランドマークロールに対応する `<search>` 要素が HTML Standard に 追加されました。検索やフィルタリングに関連するセクションに対して、`<search>`要素を使用してセマンティックにマークアップすることができるようになりました。

# あとがき

React の公式ドキュメントがとうとうベータ版ではなくなったので、 まだ読めてない箇所をちゃんと読もうと思った回でした。。。また Rome の開発をやっていることもあって parser や compiler に興味があり、React Labs の記事で紹介されている React Optimizing Compiler の話もかなりワクワクしました！今後の React も楽しみです〜

---

サイボウズでは毎月、最終火曜日の 17 時から Frontend Monthly というイベントを YouTube Live で開催しています。その月のフロントエンド注目ニュースや、ゲストを呼んでの対談など、フロントエンドに関する発信をしていますので是非どうぞ！

https://cybozu.github.io/frontend-monthly/

また、サイボウズでは一緒にサイボウズのフロントエンドをより良くする仲間を募集しています。興味ある方はこちら ↓ から！

**【フロントエンドエキスパート】**
サイボウズのフロントエンドを最高にするためのチームです。

https://cybozu.github.io/frontend-expert/

https://cybozu.co.jp/recruit/entry/career/front-end-expert.html

**【フロントエンドエンジニア（kintone アーキテクチャ刷新 PJ）】**
クラウドサービス「kintone」のフロントエンドを新しくしています。

https://cybozu.co.jp/recruit/entry/career/front-end-engineer-kintone.html

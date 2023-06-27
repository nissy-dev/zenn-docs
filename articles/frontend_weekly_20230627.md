---
title: "Next.js の App Router の最新状況など : Cybozu Frontend Weekly (2023/06/27号)"
emoji: "⚡" # お好きな絵文字を
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["CybozuFrontendWeekly", "frontend"]
published: false
publication_name: "cybozu_frontend"
---

こんにちは! サイボウズ株式会社フロントエンドエキスパートチームの [nissy](https://twitter.com/nissy_dev) です。

# はじめに

サイボウズでは毎週火曜日に Frontend Weekly という「1 週間の間にあったフロントエンドニュースを共有する会」を社内で開催しています。

今回は、2023/06/27 の Frontend Weekly で取り上げた記事や話題を紹介します。

# 取り上げた記事・話題

## Next.js App Router Update

https://nextjs.org/blog/june-2023-update

App Router の最新状況について共有している記事になっています。現在は、パフォーマンスの改善、安定性の向上、開発者向けの概念や利用方法の解説の 3 つにフォーカスしているようです。

## An Introduction to Streaming on the Web

https://vercel.com/blog/an-introduction-to-streaming-on-the-web

Web Stream の概要の紹介しつつ、Vercel での応用例について触れている記事です。図を交えながら Web Stream の特徴を解説しており、Web Stream にあまり詳しくない人におすすめの記事になっています。

## Nuxt 3.6

https://nuxt.com/blog/v3-6

Nuxt 3.6 がリリースされました 🎉 パフォーマンス改善などが主なリリースになっていそうです。

## Say Goodbye to Spread Operator: Use Default Composer

https://dev.to/aralroca/say-goodbye-to-spread-operator-use-default-composer-3c2j

デフォルト値など設定する場合に、スプレッド構文より [default composer](https://github.com/aralroca/default-composer) の利用を進めている記事です。Weekly では、コメント欄での lodash などが引き合いに出された議論が話題になりました。

## Introducing HTTP/3 Prioritization

https://blog.cloudflare.com/better-http-3-prioritization-for-a-faster-web/

Cloudflare が HTTP/3 での Prioritization をサポートしたという記事です。ウェブサイトの表示の際に読み込まれる各リソースの優先度を適切に決めることで、LCP などのメトリクスを改善できる可能性があるようです。この記事では、優先度の設定の方法などについても解説されています。

## Web Components を使用してマップ作成時間を短縮

https://developers-jp.googleblog.com/2023/06/build-maps-faster-web-components.html

Google Map をサイト上に表示できる Web Components がプレビュー版として利用できるようになりました。Google Map をウェブサイトへ導入する際の開発者体験が改善されそうで、Web Components の良い活用例となりそうです。

## New W3C website deployed

https://www.w3.org/blog/2023/new-w3c-website-deployed/

以前から予告されていた W3C のウェブサイトが公式にデプロイされました。デザインが刷新されて見やすくなっています。

Weekly では、どのようなフレームワークでウェブサイトが実装されているのかが話題になりました。ソースコードは [w3c/w3c-website-frontend](https://github.com/w3c/w3c-website-frontend) で確認することができ、[Symfony](https://symfony.com/) というフレームワークをベースに実装されているようです。

## node_modules の問題点とその歴史 npm, yarn と pnpm

https://zenn.dev/saggggo/articles/dbd739508ac212

npm、yarn、pnpm のパッケージマネージャーがどのように node_modules をインストールしてきたのかを時系列で振り返る記事です。最近だと pnpm を利用する人が増えている印象がありますが、この記事では pnpm の特徴や解決する課題などを理解することができます。

## Zero-config support for Tailwind, MUI, styled-components, and Emotion

https://storybook.js.org/blog/zero-config-support-for-tailwind-mui-styled-components-and-emotion/

TailwindCSS、Material UI などの CSS フレームワーク向けの設定を自動で作成する [addon-styling](https://storybook.js.org/addons/@storybook/addon-styling) というライブラリが Storybook チームから公開されました。この addon を導入することで、簡単に Storybook の開発を始められそうです。

# あとがき

個人的には、学生時代のインターンで WordPress のサイトに Google Map を埋め込む実装に苦労した記憶があるので、Web Components で埋め込めるのは便利な時代になったな〜と感じました。

サイボウズでは毎月、最終火曜日の 17 時から Frontend Monthly というイベントを YouTube Live で開催しています。その月のフロントエンド注目ニュースや、ゲストを呼んでの対談など、フロントエンドに関する発信をしていますので是非どうぞ！

https://cybozu.github.io/frontend-monthly/

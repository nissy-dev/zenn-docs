---
title: "オランダで開催された JS Nation と React Summit に参加してみた"
emoji: "🇳🇱"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["javascript", "react", "typescript", "vercel"]
published: false
publication_name: "cybozu_frontend"
---

6/1 ~ 6/2 にオランダのアムステルダムで開催された [JSNation](https://jsnation.com/) と [React Summit](https://reactsummit.com/) に参加してきました！この記事では、初めての海外カンファレンス参加についての感想を書きたいと思います。

![](/images/js-nation-and-react-summit-2023/card.jpg =400x)
_JSNation と React Summit のストラップ。_

## JSNation と React Summit とは？

どちらも [GitNation](https://gitnation.org/) という団体が主催する JavaScript に関するカンファレンスです。

JSNation に関しては JavaScript に関する発表を広く扱っており、今年のセッションとしては Webpack と Turbopack の開発者である [Tobias Koppers](https://twitter.com/wSokra) さんをはじめとして、Solid.js や Node.js などの OSS メンテナーや Netlify や Google などに所属する開発者による発表が予定されていました。

https://jsnation.com/

React Summit に関しては React に関連する発表を多く扱っており、JSNation と同様に Redux や Vercel のような有名な OSS や企業に所属する開発者による発表が予定されていました。

https://reactsummit.com/

今回は、この２つのカンファレンスの in-person のセッションが 6/1 ~ 6/2 にオランダのアムステルダムで行われたので、初の海外カンファレンスとして参加してきました。今回の参加のきっかけとしては、最近自分が core contributor になった Rome のメンバーの一人 [@ematipico](https://twitter.com/ematipico) が JSNation で登壇するとのことで、その発表を生で聴いて実際にコミュニケーションをとってみたかったというところが大きかったです。カンファレンス自体は、土日を挟んで 6/5 ~ 6/6 にもライブ配信のみのセッションがありました。

GitNation が主催するカンファレンスにはこの２つ以外にもヨーロッパを中心に JavaScript 関連のものがいくつか開催されているようなので、海外カンファレンスに興味がある人にはおすすめです。[^1]

[^1]: https://gitnation.org/#events や https://confs.tech/ などを使って海外カンファレンスを検索するのがおすすめです

## 印象に残ったセッション

いくつか個人的に印象に残ったセッションを紹介したいと思います。ちなみにセッションに関しては、後ほど Youtube で公開されるとのことなので、詳細は [JSNation](https://www.youtube.com/@JavaScriptConferences) や [React Summit](https://www.youtube.com/@ReactConferences) の YouTube チャンネルに上がる動画をみてもらうのが良いと思います。

### Bun, Deno, Node.js? Recreating a JavaScript Runtime From Scratch

C++ で提供されている V8 の API を使いながら setTimeout などの関数を実装し、JavaScript Runtime に実装されている API の詳細を理解するという発表でした。

発表では、独自の print 関数や setTimeout 関数を実装し、そのコードの解説を行なっていました。登壇者は、[Node.js v0.0.1 のコード](https://github.com/nodejs/node/tree/v0.0.1)を参考にしたらしく、確かにコードがシンプルで理解するには良さそうだと思いました。ちなみに今回の発表は次の動画が元になっているようなので、こちらの動画を見るのもおすすめです。

https://www.youtube.com/watch?v=ynNDmp7hBdo

### The Core of Turbopack Explained (Live Coding)

Webpack と Tubopack の開発者 [sokra さん](https://twitter.com/wSokra)によるライブコーディングの発表でした。内容としては、Turbo Engine のコア処理の紹介ということで、JavaScript のモジュールグラフのキャッシュの登録や削除の実装をしていました。

https://twitter.com/marcelcutts/status/1664229678071119874?s=20

実装自体は、Map でモジュールグラフの計算処理をキャッシュさせるといういかにもな感じの内容だったのですが、ロジックがすごい速さで実装されていくのでついていくのが大変でした。ただ、世界でも有名な OSS の開発者のコーディングを生で見れたのは良い経験でした。

### Rome, a Modern Toolchain!

[Emanuele Stoppa さん](https://twitter.com/ematipico)による Rome のモチベーションや特徴的な機能を紹介する発表でした。全体的に v10 リリースのブログの内容を紹介しており、「ツールごとにパースを繰り返さないため高速」、「エラーメッセージが豊富で分かりやすい」といった特徴を取り上げていました。

https://rome.tools/blog/2022/11/08/rome-10/

発表では、TypeScript と Ant Design のリポジトリのコードを Prettier と Rome でフォーマットするデモが行われました。Rome の場合は Prettier と比較して一瞬でフォーマットが完了し、会場が盛り上がりました。メンテナーの 1 人としてこの瞬間に立ち会うことができて、日本からこの会場に来て本当に良かったなと思いました。

![](/images/js-nation-and-react-summit-2023/rome.jpg)
_Emanuele Stoppa さんの登壇。直接お話ししたのですがとても優しい方でした。_

### Reactivity: There and Back Again

MobX の開発者の [Michel Weststrate さん](https://twitter.com/mweststrate)による reactive なプログラミングの再流行についての発表でした。React が発表され Redux による immutability を原則にしたプログラミングが流行ったものの、MobX や Vue の登場を経て、最近では Signal による reactive なプログラミングに再度注目が集まっていると述べていました。

https://twitter.com/coding_garden/status/1664540702825488385?s=20

以前 Signal 関連の話題が Twitter で盛り上がっていた時に社内でも似たような話をしたので、話の内容には親近感が湧きました。発表の最後には、「everything old is new again」や「Dogma kills innovation」という言葉でしめていたのも印象的でした。

### You Can’t Use Hooks Conditionally… or Can You?

昨年話題になった [React の `use`](https://github.com/reactjs/rfcs/pull/229) という Hooks が条件分岐の中でも呼び出せるということをきっかけに、条件分岐の中で呼べる Hooks と呼べない Hooks の違いを解説した発表でした。

`use` 以外には `useContext` も条件分岐の中で呼びだすことができるようで、発表では通常の Hooks と useContext がどのように fiber ツリーの中で管理されているのかを解説していました。[^2] 個人的には、React の fiber ツリーの知識もあまりなかったので勉強になる発表でした。

[^2]: React の開発者 [acdlite さんのツイート](https://twitter.com/acdlite/status/1581401077915975680?s=20)を参考のこと

![](/images/js-nation-and-react-summit-2023/hooks.jpg)
_スライドの図がとても分かりやすい発表でした。_

### Next.js Metamorphosis

Vercel の [Lee Robinson さん](https://twitter.com/leeerob)による Next.js の pages ディレクトリのコードを app ディレクトリのコードに置き換えるライブコーディングの発表でした。(TODO) ライブコーディングでは、React Server Components によってデータ取得のコードとコンポーネントのコードのコロケーションが可能になることをとても強調していたのが印象的でした。

発表後の質問では、個人的にも気になっていた Next.js の pages ディレクトリをすぐに app ディレクトリに移行すべきかどうかの話がありました。登壇者によると、数年単位でのサポートを予定しており、すぐに移行する必要はそこまでないと述べていました。

![](/images/js-nation-and-react-summit-2023/app-router-qa.jpg)
_実際に投稿された質問。質疑応答は [slido](https://www.slido.com/) で行われていました。_

## 参加してみての感想

### 参加者を楽しませる工夫がたくさん見られた

カンファレンスの各トラックにはセッションを進行させる MC がいるのですが、有名な企業の DevRel をやっている人が多く、登壇前に技術的なジョークや登壇者のエピソードなどで会場を盛り上げていたのが印象的でした。

また、[JavaScript に関する OSS の開発者やコミュニティーの功労者を表彰したり](https://osawards.com/)、React の 10 周年をお祝いしたりするなど、参加者を楽しませるための多くのイベントが企画されていました。

https://twitter.com/ReactSummit/status/1664642335903215616?s=20

### セッションの聞き方が大きく違った

日本では、セッション中に PC でメモを取ったり、Twitter のハッシュタグでセッションへのコメントを投稿したりすると思います。一方で海外では、セッション中に PC を開いている人はおらず、スライドや登壇者をみてセッションを聞いていました。

このこともあってなのか、海外の発表の方がシンプルな内容のものが多い印象受けました。個人的には、後から内容を見返したりリアルタイムに疑問を残せたりするので、日本の聞き方もいいなと思います。

ちなみに今回のカンファレンスの Twitter のハッシュタグは、[#jsnation](https://twitter.com/hashtag/jsnation) と [#reactsummit](https://twitter.com/hashtag/reactsummit) になっています。ハッシュタグからはカンファレンス雰囲気を知ることができるので、気になる人は見てみると良いと思います。

### 知らない内容ばかりではなく自信になった

セッションの内容に関しては、もちろん初めて知る内容もあったのですが、予想してたよりも知っている内容や自分の考えと同じような内容も多かったです。また質疑応答でも、日本でも目にするような質問が上がっていて、日本にいても世界の動向を捉えられているということを実感できました。これらの経験は、自分の技術に対する向き合い方への自信につながりました。

### 英語を喋れるとエンジニアのコミュニティが広がる

英語力に関しては、自分の実力不足でセッションを聞き取るのが難しいことも多かったのですが、スライドもあるので内容を理解するにはなんとかなることも多かったです。

一方で、話したかった Rome のコントリビューターとも話せたのですが、深い関係を築くことが難しかったです。特にリスニングがあまりできなかったので、スムーズなコミュニケーションができませんでした。英語を喋れるようになると、Rome のコントリビューターともっと深い関係を築けたり、より多くの人と交流できたりすると思うので、この経験は今後の自分への大きな糧にしていきたいです。

## 最後に

正直、初めての海外カンファレンスに参加するのは不安でしたが、自信がついたことからもっと頑張らなきゃと思えるようなことまで色々な経験をできて良かったと思います。英語力に関しては多少わかる程度で参加しても、カンファレンスから得られるもはそれなりにあると思います。

有名な OSS のコントリビューターに会える数少ない機会なので、今後もこのような海外カンファレンスに積極的に参加して、自信を持って海外のスピーカーを日本に招待できるようになろうと思います。また、いつか自分もこういうところで発表できるような OSS コントリビューターになれたらいいなと思います。

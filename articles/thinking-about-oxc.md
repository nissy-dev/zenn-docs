---
title: "Rome の contributor からみた Oxc の印象"
emoji: "🦀"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["JavaScript", "lint", "formatter", "rome"]
published: false
publication_name: "cybozu_frontend"
---

最近、Boschen さんが開発している Oxc に注目しています。

https://github.com/Boshen/oxc

社内で Oxc の近況を slack に投稿していたところ、「Oxc は Rome で話題になっていますか？」や「Oxc はうまくいくと思いますか？」と聞かれたことで Rome の現状を整理するいいきっかけになったので、記事に残しておこうかなと思います。

## Rome と Oxc の違い

Rome と Oxc はどちらも linter、formatter、transpiler などを提供するつもりではあるので、ユーザーからみた違いは分かりにくいかなとは思います。現時点での大きな違いは、次の２点だと思っています。

- プロジェクトのスコープ
  - Rome: JS/TS に限らず Web 開発に関連する言語全般にツールを提供することを試みている
  - Oxc: JS/TS に関するツールにフォーカスしている
- 提供するツールの拡張性に対する考え方
  - Rome: 設定できるパラメータは極力減らし、良いデフォルト値を提供する
  - Oxc: ユーザーによる拡張や独自ツールの作成を前提とした API を設計する

Rome は、近年の Web 開発に必要なツールチェーンの設定の多さや複雑さを解消したいというモチベーションから始まったプロジェクトです。このため、Rome のスコープは広く、提供されるツールは非常に opinionated なものになっています。

一方で、Oxc はプロジェクトのゴールに次の観点を掲げていたこともあり、非常に拡張性を意識していることがわかります。[^1]

> Provide the basic building blocks for creating your own tools by having good API designs

[^1]: https://github.com/Boshen/oxc/tree/v0.1.1 を見るとわかりますが、以前の README の Goals に書いてありました。

## Rome の現状

Oxc の話の前に、まずは Rome について振り返ってみます。

Rome は、npm の weekly download に表れているとおり、昨年の 10 月のリリース以降はなかなかユーザーも増やせていません。

https://www.npmjs.com/package/rome

ユーザーへの導入が進んでない大きな原因としては、ESLint からの移行パスを提供できてない点が挙げられると思います。

ESLint は非常に大きなプラグインのコミュニティを持っていますが、Rome は linter に関するプラグインの仕組みを提供しておらず、プラグインの仕組みを提供するのも簡単ではありません。移行しやすいように JS でかけるような仕組みにすると SWC のプラグイン作成時に議論されていたパフォーマンスの問題が生じる可能性があります。[^2] 一方で、Rust でかけるような仕組みにすると、既存のプラグインを Rust で一から書き直す必要があります。

[^2]: https://github.com/swc-project/swc/issues/18#issuecomment-482805616

実際に SWC は Rust でのプラグインの仕組みを提供しましたが、コミュニティー主体でのプラグインの実装はあまり進まず、作者自身が多くのプラグインを実装することになっていたように見えます。[^3] ESLint の場合は、プラグインとして対応しなければいけないルールの数が SWC の時の比ではないので、もし Rust での仕組みを提供したとしても移行の障壁は非常に高いことが予想されます。

[^3]: https://github.com/swc-project/swc/discussions/4768 を見る限り、多くのプラグインをコアメンバーが実装しているように見えます。

また、Rome は現在よく使われている ESLint のルールを中心に 150 以上のルールを実装していますが、それらが実際の ESLint のどのルールと対応しているのかをすぐに確認する方法がありません。次のような ESLint の設定がよく使われていると思うのですが、このような設定で導入されるルールの多くは Rome でも実装されています。

```js
module.exports = {
  parser: "@typescript-eslint/parser",
  plugins: ["react", "@typescript-eslint", "jsx-a11y", "import"],
  extends: [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:jsx-a11y/recommended",
    "plugin:import/recommended",
    "prettier",
  ],
  ...
}
```

また、Web 開発に必要なツールを統一するという目標に関しては、開発体制の変化が大きな障壁になっています。

開発体制が完全なボランティアベースになってしまったので、計画していた CSS と HTML に関する開発にまだあまり着手できていません。[^4] また、近年 Astro や Qwik などの独自のパーサーが必要なフレームワークが公開されることが多く、これらの対応についても後回しになっているのが現状です。

[^4]: ちなみに CSS の開発に着手しようとしているコアメンバーはいます。https://discord.com/channels/678763474494423051/1125350182507266178

## Oxc のこれから

このような Rome の現状を踏まえながら、Oxc の今後についてみていきます。

まず、Oxc はスコープを JS/TS に絞っているので、個々のフレームワークを含むスコープ外の対応に関してはコミュニティーで実装が進んでいく必要があります。この場合、コミュニティーの形成が非常に重要になりますが、Twitter を見ている限り Boschen さんはこの点が非常に上手だと感じます。

具体的には、次に実装する機能を Twitter の投票機能を使って決めたり[^5]、リリースを頻繁に行って開発の勢いを見せたりしています。また、Evan You さんからコメントをもらった後は Vue 関連の人と積極的にコミュニケーション取ったり、プラグインや TS の type checker 周りでのコラボレーションも非常に積極的です[^6] [^7]。

[^5]: https://twitter.com/boshen_c/status/1670662562634158082?s=20
[^6]: https://twitter.com/boshen_c/status/1671000640645120001?s=20
[^7]: https://twitter.com/kaleidawave/status/1672210943533633543?s=20

次に、ESLint からの移行パスについてですが、こちらに関してはつい先日 Oxc が linter のロードマップを公開しました。

https://github.com/Boshen/oxc/issues/481

このロードマップによると、ESLint と完全に互換性を保った移行は不可能なので、次の方針で linter を実装していくようです。

- コードの correctness のみにフォーカスしてルールを実装する
  - bike-shedding になりがちな スタイルや慣習に関するルールは提供しない
  - 有名な 10 個ほどのプラグインのレコメンドルールも実装する
- プラグインは DSL として GraphQL のサブセットを利用する

Rome の開発者としては、プラグインを Rust ではなく GraphQL like な DSL で提供するところに注目しています。

https://github.com/Boshen/oxc/discussions/476

これがコミュニティで受け入れられるかどうかが linter として成功するかの鍵を握っていると思います。Rust での仕組みを提供するよりはユーザーも馴染みやすそうで非常に面白いと思っていますが、複雑な linter のロジックをどこまで再現できるのかは未知数だなと思います。

Oxc に関してもいくつか障壁はあると思うのですが、それらに対して新しいアプローチを取り入れながら乗り越えていこうとしているのが印象的です。

ちなみに、Rome の開発を支えていた [Emanuele さん](https://twitter.com/ematipico)と [Micha さん](https://twitter.com/MichaReiser)は Oxc の開発初期の頃からアドバイスを積極的に行なっています。また、Rome も Oxc から Rust の細かい Tips を導入することもあります。そのため、Rome と Oxc はより良いツールチェーンを提供するためにお互いに高め合う関係になっています。

## 最後に

Rome は、JS/TS にフォーカスして色々なツールを実装するというよりも、ある程度基盤ができている linter や formatter の基盤を JSON や CSS の複数の言語に広げていくようなマイルストーンで動きそうです。

https://github.com/rome/tools/milestone/11

一方で、CSS に関しては Lightning CSS で同様の動きがすでに進んでおり、一から Rome がそのような動きをやり始める必要性も少ないのかなと感じることもあります。

https://github.com/parcel-bundler/lightningcss

Oxc に関しては、JS/TS にフォーカスしながら色々なツールチェーンの実装をしていき、直近は linter や minifier の実装に集中しています。個人的には、JS/TS のツールチェーンに一番興味があり、それらを統一していけるような活動をしたいので、Oxc についても引き続き注目しつつコントリビュートもしていきたいです。

現在 Turborepo の開発をしており、以前は Rome にもこのトリビュートしていた Nicholas さんは、Twiiter で次のような[コメント](https://twitter.com/NicholasLYang/status/1642330243942809600?s=20)をしていました。

> Maybe this is naive, but I still think we haven't made a good enough parser framework. I want something that has good error messages, a high fidelity CST, an AST facade, decent recovery, and compiles down to multiple languages (Rust, C, JS)

Rome が全ての言語をサポートしていくというよりは、Rome でのベストプラクティス部分を切り出して、特定の言語のツールチェーンを作成するための汎用的なフレームワークを提供するのは面白いアイデアだなとは思いました。そうすることで、 Lightning CSS のような動きもコミュニティで起きやすくなるかもしれません。このコメントのより詳しい話は Nicholas さんがブログにしていて、自分の知らない視点が多く面白かったです。

https://uptointerpretation.com/posts/tooling-for-tooling/

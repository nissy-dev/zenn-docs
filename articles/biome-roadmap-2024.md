---
title: "2024年のロードマップ、新しいロゴとホームページの公開、v1.5.0のリリース"
emoji: "🛣️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["biome", "javascript", "linter"]
published: false
publication_name: "cybozu_frontend"
---

このドキュメントは、次の Biome のブログの日本語訳になります。

https://biomejs.dev/blog/roadmap-2024/

細かい内容については、ぜひ原文の方を読んでもらえると嬉しいです。

---

## ロードマップ 2024

コアコントリビューターとメンテナーが 2024 年にフォーカスしようとしていることを共有できることはとても嬉しいです。一方で、Biome はコミュニティ主導のプロジェクトであるため、取り上げている全てのアイデアのリリースを約束することはできません。

もしいくつかのアイデアに共感し、それらの開発を加速させたいと思った際には、次のような方法でサポートすることができます。

- [**プロジェクトとコミュニティに参加する。**](https://github.com/biomejs)アイデアを実装するためのサポートをすることができます。
- [**スポンサーになる。**](https://opencollective.com/biome)Biome をお使いの会社では、スポンサーになることができます。Biome は CI の時間を削減し、費用を節約できるほど速いです。パフォーマンスは、プロジェクトにおいて重要視しています。また、スポンサーになることは、会社の宣伝の手段としても有効です。
- [**ドキュメントを改善する。**](https://biomejs.dev/guides/getting-started)ドキュメンテーションを翻訳し、英語が得意でない人々にも Biome を利用できるように手助けすることができます。

### 前書き

このプロジェクトは始まったばかりで、Prettier、ESLint、Webpack、Vite、ESBuild などと比較してユーザーを獲得できていないのが現状です。一方で、最近の出来事 (Biome が Rome をフォークしたこと、スポンサーの獲得、bounty challenge) を通じて、プロジェクトに興味を持っているユーザーが存在し、いくつかのニーズを満たすツールであることがわかりました。

小さなプロジェクトでは、ESLint/Prettier から Biome へ移行するのは簡単です。しかし、**大規模なコードベースにおいて移行することは難しく、時間がかかります。** これは、Biome における重要な課題になっています。

ユーザーには様々なニーズがあり、すべてを満たすことは不可能です。すべての機能とコントリビューションが[プロジェクトの理念を満たし](https://biomejs.dev/internals/philosophy/)、デフォルトで最善の開発体験を提供するように努めます。

### 主にフォーカスする領域

1. Biome への移行のサポートする
2. 対応言語を増やし、より多くの Web 開発のエコシステムへの適用する
3. より多くの機能を提供するために既存の機能の強化する
4. プラグイン機能を提供する
5. トランスパイル機能を提供する
6. コミュニティを拡大し、学習コンテンツを提供する

### Biome への移行のサポートする

- Prettier や ESLint から移行したいユーザー向けのガイドを提供する。
- ESLint のルールと Biome のルールの対応関係を示すドキュメントを提供する。
- 移行を容易にするためのコマンドを提供する。
  - `biome migrate prettier` というコマンドを提供する。このコマンドは、`.prettierrc` および `.prettierignore` を読み取り、`biome.json` の設定を更新（または作成）します。
  - `biome migrate eslint` というコマンドを提供する。このコマンドは、ESLint の JSON ファイルによる設定や ignore ファイルを読み取る予定ですが、いくつかの限界もありそうです。

### 対応言語を増やす

CSS は、次の対応言語としてフォーカスしており、その次は HTML や Markdown の開発が続きます。進捗状況については、[最新の対応状況](https://biomejs.dev/internals/language-support)を確認してください。CSS の開発を進めると、多くの新しい機能や PoC の実装を始めることできます。例えば、CSS の linter や formatter の実装を開始でき、stylelint の lint ルールのいくつかを実装できます。

また、複数の言語を扱う linter (cross-lint) の PoC も新しく始めることができます。複数の言語を扱う linter (cross-lint) のアイデアには、次のようなものがあります。

例：プロジェクトで定義されている CSS スタイル/クラスを解析し、そのスタイルが JSX/HTML ファイル内で使用されていない場合にユーザーに警告する。

さらに、組み込み言語に関する PoC も始めることができます。HTML と Markdown は、CSS の次にフォーカスする言語です。HTML の開発を進めると、フロントエンドの開発でよく使われている HTML を拡張した言語 ([Vue](https://vuejs.org/)、[Svelte](https://svelte.dev/)、[Astro](https://astro.build/) など) をパースできるようになります。これには、HTML を拡張した言語を構文木で表現する方法を検討する必要があります。

### より多くの機能を提供するために既存の機能の強化する

#### プロジェクト全体の解析と依存関係の解決

無効なライセンスを持つライブラリを報告するような、マニフェストを読み取り任意の操作を行う package.json に関する lint ルールを提供できるようになります。プロジェクト全体を解析し、使用されていないモジュールを検出するような lint ルールを提供できるようになります。モジュールの依存関係を解析し、プロジェクト内で使用されていない依存モジュールを検出するような lint ルールを提供できるようになります。また LSP の機能も強化され、次のような機能を提供することができるようになります。

- プロジェクト全体での変数名の変更
- インポートの自動補完
- インラインの型システム

#### 型システム

TypeScript のような完全な型システムを構築するには膨大な労力が必要になります。そのため、厳密な型付けを必要とする型システムのサブセットを構築しようと考えています。

このアプローチを取ることで、ユーザーが求めていた型システムが必要な重要な lint ルールのいくつかを構築できるようになります。一方で、厳密に型付けされたコードへの依存することやコンパイラの型推論が最小限になるなどの欠点があります。もし型システムに関する安定した基盤を持つことができた際には、徐々に型チェックの機能を拡張することもできます。

#### CLI

コマンドラインツールについては、次のような機能を提供することができます。

- オフライン時のドキュメントとしての `explain` コマンド
- JSON などの異なる形式での実行結果の出力
- `zsh` などのいくつかのシェルのための自動補完
- 変更された行のみを対象にする `--modified` 引数
- パフォーマンスのボトルネックを追跡するためのメトリクスの公開

### プラグイン機能を提供する

プラグインに関しては、まずは Biome に適した設計を調査していく予定です。これは、Biome が複数のツールを含むツールチェーンであり、ほかのツールとは異なっているためです。既存のアイデアにとらわれず、現在広く使われているツールとは異なる設計を検討する必要があるかもしれません。

Biome のプラグインがどのようなものになるかはまだわかりませんが、Biome が提供するすべての機能でプラグインを利用できるようにするべきだと考えています。考えられるアイデアとしては、次のようなものを利用する方法があります。

- DSL
- WASM
- Runtime

### トランスパイル機能を提供する

コードの変換と生成は、コンパイラーを実装する上での最初のステップとなる予定です。TypeScript および JSX を JavaScript に変換する機能を提供することを考えています。

### コミュニティを拡大し、学習コンテンツを提供する

Biome には、VSCode 拡張機能、IntelliJ 拡張機能、Discord ボットなど成長を続けるエコシステムがあります。 私たちはこれらのツールが提供する機能を拡張したいと考えており、開発をサポートしたいと考えているユーザーを歓迎しています。

Biome のコミュニティはゆっくりと成長しており、Biome に貢献し続けている人には感謝したいです。**Biome ではあらゆる種類の貢献を評価します。** Biome をサポートするために、Rust に習熟している必要はありません。 ディスカッションに参加して機能の形成に協力したり、他の人を支援したりすることさえも「貢献」とみなされます。 引き続きエコシステムに貢献したいと思っている方は、[プロジェクトのメンテナーになること](https://github.com/biomejs/biome/blob/main/GOVERNANCE.md#maintainer-nomination)をお勧めします。

最近、Biome は[YouTube チャンネル](https://www.youtube.com/channel/UC6ssscaFgCSlbv1Pb6krGVw)を開設しました。このチャンネルを使用して、学習コンテンツをコミュニティと共有する予定です。

## 新しいロゴとホームページの提供

このブログを通じて、新しいロゴとホームページのリニューアルを正式に発表したいと思います。新しいロゴを持つことで、プロジェクトに今までとは異なる意味を持たせたいと考えています。**Biome は単純な Rome のフォークだけではなくなり**、独自のプロジェクトとして新たな道を歩み始めます。

ロゴの三角形は山、つまり**土壌**を表し、左のカーブした図形は海の波、つまり**水**を表しています。これらの２つの要素は、自己完結型のエコシステムを作り出すために重要であり、それらによって成長し繁栄していくことができます。

## Version 1.5.0 のリリース

2024 年のロードマップとともに、新しいバージョンを公開します。このバージョンでは、CLI 周りの新機能や **フォーマッターの多くの修正** が含まれています。**TypeScript、JSX、および JavaScript のフォーマットにおける Prettier との互換性は、ついに 97%を超えました。**

### 新機能

- 変更されたファイルのみを処理する
- `biome ci` の結果を GitHub の PR で表示させる
- `biome explain` コマンドを提供する
- `biome migrate` コマンドが `$schema` の値を更新する
- 新しい Lint ルールの提供する

### 変更されたファイルのみを処理する

VCS integration を有効にすると、変更されたファイルのみを処理するようにできます。現時点では、VCS より変更されたファイルを認識するため、Biome は変更内容について何も把握はしていません。

この機能によって、`lint-staged` のようなツールを使う必要がなくなるかもしれません。この機能を利用するには、設定ファイルでデフォルトのブランチを指定し、CLI で `--changed` オプションを渡す必要があります。

```diff json:biome.json
{
  "vcs": {
     "enabled": true,
     "clientKind": "git",
+    "defaultBranch": "main"
  }
}
```

いくつかのファイルを変更したら、必要なコマンドに新しいオプションを使用します。例えば、format コマンドの場合は次のようになります。

```shell
biome format --changed --write
```

### `biome ci` の結果を GitHub の PR で表示させる

コマンドの `check` と `ci` は、動作が非常に似ていることから、その違いがユーザーに伝わりにくくなっていました。今回のバージョンからコマンド `ci`を GitHub の CI 上で実行すると、その実行結果が PR 上に表示されるようになりました。

![](/images/biome-roadmap-2024/github-annotation.png)
_`biome ci` の結果が GitHub の PR で表示されている様子_

もし実行結果が PR 上に表示されない場合は、GitHub Actions の workflow ファイルにおいて次のパーミッションの変更が必要になる場合があります。

```yaml:.github/workflows/action.yml
permissions:
  pull-requests: write
```

### `biome explain` コマンドを提供する

このコマンドは、「オフライン」のドキュメントツールとして機能します。 このリリースでは、コマンドはすべての lint ルールの説明をサポートしています。

例えば、`noAccumulatedSpread` のドキュメントを表示するには次のコマンドを実行します。

```shell
biome explain noAccumulatingSpread
```

実行すると、次の markdown 形式のドキュメントが表示されます。

````markdown
# noAccumulatingSpread

No fix available.

This rule is recommended.

# Description

Disallow the use of spread (`...`) syntax on accumulators.

Spread syntax allows an iterable to be expanded into its individual elements.

Spread syntax should be avoided on accumulators (like those in `.reduce`)
because it causes a time complexity of `O(n^2)` instead of `O(n)`.

Source: https://prateeksurana.me/blog/why-using-object-spread-with-reduce-bad-idea/

## Examples

### Invalid

```js,expect_diagnostic
var a = ['a', 'b', 'c'];
a.reduce((acc, val) => [...acc, val], []);
```

```js,expect_diagnostic
var a = ['a', 'b', 'c'];
a.reduce((acc, val) => {return [...acc, val];}, []);
```

```js,expect_diagnostic
var a = ['a', 'b', 'c'];
a.reduce((acc, val) => ({...acc, [val]: val}), {});
```

## Valid

```js
var a = ["a", "b", "c"];
a.reduce((acc, val) => {
  acc.push(val);
  return acc;
}, []);
```
````

### `biome migrate` コマンドが `$schema` の値を更新する

コマンド `biome migrate` は、設定ファイル `biome.json` 内の `$schema` 値を更新するようになりました。今回のバージョンに更新したら、すぐにコマンドを実行するのが良いでしょう。次のような結果が得られるはずです。

```diff json:biome.json
{
-  "$schema": "https://biomejs.dev/schemas/1.4.1/schema.json"
+  "$schema": "https://biomejs.dev/schemas/1.5.0/schema.json"
}
```

### 新しい lint ルールを提供する

次のルールが新しく nursery グループに追加されました。各ルールの詳細は、ドキュメントを参考にしてください。

- [useExportType](https://biomejs.dev/linter/rules/use-export-type)
- [useFilenamingConvention](https://biomejs.dev/linter/rules/use-filenaming-convention)
- [useNodeImportProtocol](https://biomejs.dev/linter/rules/use-node-import-protocol)
- [noNodejsModules](https://biomejs.dev/linter/rules/no-nodejs-modules)
- [noInvalidUseBeforeDeclaration](https://biomejs.dev/linter/rules/no-invalid-use-before-declaration)

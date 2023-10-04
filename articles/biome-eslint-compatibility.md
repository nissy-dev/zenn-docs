---
title: "Biome と ESLint の lint ルールの互換性"
emoji: "⚙️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["JavaScript", "TypeScript", "lint", "formatter", "Biome", "ESLint"]
published: false
publication_name: "cybozu_frontend"
---

Biome では、現在約 150 個程度の JavaScript・TypeScript に関する lint ルールを実装しています。

https://biomejs.dev/linter/rules/

これらのルール多くは既存の ESLint 関連で実装されているルールと同じものになっているのですが、ルールの名前が異なることもあり、その対応関係がわかりにくくなっています。

そこでこの記事では、フロントエンド開発をする場合によく設定していそうな次の lint ルールについて、Biome で実装されているかどうかの対応表を紹介します。

- [eslint](https://eslint.org/) (`eslint:recommended`)
- [typescript-eslint](https://typescript-eslint.io/) (`plugin:@typescript-eslint/recommended`)
- [eslint-plugin-jsx-a11y](https://github.com/jsx-eslint/eslint-plugin-jsx-a11y) (`plugin:jsx-a11y/recommended`)
- [eslint-plugin-react](https://github.com/jsx-eslint/eslint-plugin-react) (`plugin:react/recommended`)
- [eslint-plugin-react-hooks](https://github.com/facebook/react/tree/main/packages/eslint-plugin-react-hooks) (`plugin:react-hooks/recommended`)

ESLint から Biome への移行を考える際の参考にしてもらえればと思います。

また、この記事よりさらに細かい対応表については次のディスカッションにもまとまっているので、興味ある人はこちらも参考にしてもらえればと思います。

https://github.com/biomejs/biome/discussions/3

## ESLint recommended

## TypeScript ESLint recommended

## eslint-plugin-jsx-a11y recommended

## eslint-plugin-react recommended

## eslint-plugin-react-hooks recommended

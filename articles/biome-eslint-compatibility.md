---
title: "Biome と ESLint の lint ルールの互換性"
emoji: "⚙️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["JavaScript", "TypeScript", "lint", "Biome", "ESLint"]
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

## まとめ

対応表がずらっと並ぶので、まとめを先に書いておきます！

- ESLint でよく利用されている recommended ルールの 8 割以上は Biome でも実装されています
  - [exhaustive-deps] などの hooks 関連のルールも実装されています
- Biome では linter と一緒に [import 文をソートする機能](https://biomejs.dev/analyzer/#imports-sorting)も含む formatter も利用できます
  - `prettier` や `eslint-plugin-import` などをインストールする必要はありません

ESLint との互換性を意識した設定は次のようになります。

```json
{
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true,
      // recommended を true にした上で、つぎの設定が必要になる
      "a11y": {
        "noAccessKey": "error",
        "useHeadingContent": "error"
      },
      "correctness": {
        "noUndeclaredVariables": "error",
        "noUnusedVariables": "error"
      },
      "style": {
        "noNamespace": "error"
      },
      "nursery": {
        "noEmptyCharacterClassInRegex": "error",
        "noFallthroughSwitchClause": "error",
        "noMisleadingInstantiator": "error",
        "useAsConstAssertion": "error",
        "useExhaustiveDependencies": "error",
        "useHookAtTopLevel": "error"
      }
    }
  }
}
```

## ESLint recommended

[no-mixed-spaces-and-tabs] などのスタイルに関するルールを除いた対応表は次のようになります。🚧 になっているルールは、今後実装される予定があります。

|              ESlint recommended |                                         Biome | recommended |
| ------------------------------: | --------------------------------------------: | ----------- |
|             [constructor-super] |                   [noInvalidConstructorSuper] | ✅          |
|                 [for-direction] |                        [useValidForDirection] | ✅          |
|                 [getter-return] |                             [useGetterReturn] | ✅          |
|     [no-async-promise-executor] |                      [noAsyncPromiseExecutor] | ✅          |
|          [no-case-declarations] |                        [noSwitchDeclarations] | ✅          |
|               [no-class-assign] |                               [noClassAssign] | ✅          |
|           [no-compare-neg-zero] |                            [noCompareNegZero] | ✅          |
|                [no-cond-assign] |                       [noAssignInExpressions] | ✅          |
|               [no-const-assign] |                               [noConstAssign] | ✅          |
|         [no-constant-condition] |                         [noConstantCondition] | ✅          |
|              [no-control-regex] |                  [noControlCharactersInRegex] | ✅          |
|                   [no-debugger] |                                  [noDebugger] | ✅          |
|                 [no-delete-var] |                                    [noDelete] | ✅          |
|                  [no-dupe-args] |                       [noDuplicateParameters] | ✅          |
|         [no-dupe-class-members] |                     [noDuplicateClassMembers] | ✅          |
|               [no-dupe-else-if] |                                            🚧 |             |
|                  [no-dupe-keys] |                       [noDuplicateObjectKeys] | ✅          |
|             [no-duplicate-case] |                             [noDuplicateCase] | ✅          |
|      [no-empty-character-class] |                [noEmptyCharacterClassInRegex] |             |
|                      [no-empty] |                                            🚧 |             |
|              [no-empty-pattern] |                              [noEmptyPattern] | ✅          |
|                  [no-ex-assign] |                               [noCatchAssign] | ✅          |
|         [no-extra-boolean-cast] |                          [noExtraBooleanCast] | ✅          |
|                [no-fallthrough] |                   [noFallthroughSwitchClause] |             |
|                [no-func-assign] |                            [noFunctionAssign] | ✅          |
|              [no-global-assign] |                                               |             |
|              [no-import-assign] |                              [noImportAssign] | ✅          |
|         [no-inner-declarations] |                         [noInnerDeclarations] | ✅          |
|             [no-invalid-regexp] |                                               |             |
|       [no-irregular-whitespace] |                                               |             |
|          [no-loss-of-precision] |                             [noPrecisionLoss] | ✅          |
| [no-misleading-character-class] |                                            🚧 |             |
|                 [no-new-symbol] |                                 [noNewSymbol] | ✅          |
|    [no-nonoctal-decimal-escape] |                     [noNonoctalDecimalEscape] | ✅          |
|                  [no-obj-calls] |                         [noGlobalObjectCalls] | ✅          |
|                      [no-octal] |                                               |             |
|         [no-prototype-builtins] |                         [noPrototypeBuiltins] | ✅          |
|                  [no-redeclare] |                                 [noRedeclare] | ✅          |
|               [no-regex-spaces] | [noMultipleSpacesInRegularExpressionLiterals] | ✅          |
|                [no-self-assign] |                                [noSelfAssign] | ✅          |
|              [no-setter-return] |                              [noSetterReturn] | ✅          |
|    [no-shadow-restricted-names] |                     [noShadowRestrictedNames] | ✅          |
|              [no-sparse-arrays] |                               [noSparseArray] | ✅          |
|          [no-this-before-super] |                          [noUnreachableSuper] | ✅          |
|                      [no-undef] |                       [noUndeclaredVariables] | ✅          |
|                [no-unreachable] |                               [noUnreachable] | ✅          |
|             [no-unsafe-finally] |                             [noUnsafeFinally] | ✅          |
|            [no-unsafe-negation] |                            [noUnsafeNegation] | ✅          |
|   [no-unsafe-optional-chaining] |                    [noUnsafeOptionalChaining] | ✅          |
|              [no-unused-labels] |                              [noUnusedLabels] | ✅          |
|                [no-unused-vars] |                           [noUnusedVariables] |             |
|      [no-useless-backreference] |                                            🚧 |             |
|              [no-useless-catch] |                              [noUselessCatch] | ✅          |
|                       [no-with] |                                      [noWith] | ✅          |
|                 [require-yield] |                                    [useYield] | ✅          |
|                     [use-isnan] |                                    [useIsNan] | ✅          |
|                  [valid-typeof] |                              [useValidTypeof] | ✅          |

## TypeScript ESLint recommended

型情報を必要しないルールとの対応表は次のようになります。**Biome では TypeScript の型情報を扱うことができないので、型情報が必要なルールについては基本的にサポートしていません。**

|         TypeScript ESlint recommended |                        Biome | recommended |
| ------------------------------------: | ---------------------------: | ----------- |
|                      [ban-ts-comment] |                              |             |
|                           [ban-types] |              [noBannedTypes] | ✅          |
|            [no-duplicate-enum-values] |                              |             |
|                     [no-explicit-any] |              [noExplicitAny] | ✅          |
|         [no-extra-non-null-assertion] |    [noExtraNonNullAssertion] | ✅          |
|                      [no-misused-new] |   [noMisleadingInstantiator] |             |
|                        [no-namespace] |                [noNamespace] |             |
| [no-non-null-asserted-optional-chain] |                              |             |
|                       [no-this-alias] |         [noUselessThisAlias] | ✅          |
|      [no-unnecessary-type-constraint] |                              |             |
|       [no-unsafe-declaration-merging] | [noUnsafeDeclarationMerging] | ✅          |
|                     [no-var-requires] |                              |             |
|                     [prefer-as-const] |        [useAsConstAssertion] |             |
|              [triple-slash-reference] |                              |             |

## eslint-plugin-jsx-a11y recommended

対応表は次のようになります。eslint-plugin-jsx-a11y は、Next.js や Remix が提供している ESLint ルールにも利用されています。

|                            jsx-a11y recommended |                                      Biome | recommended |
| ----------------------------------------------: | -----------------------------------------: | ----------- |
|                                      [alt-text] |                               [useAltText] | ✅          |
|                            [anchor-has-content] |                         [useAnchorContent] | ✅          |
|                               [anchor-is-valid] |                           [useValidAnchor] | ✅          |
|            [aria-activedescendant-has-tabindex] |                                            |             |
|                                    [aria-props] |                        [useValidAriaProps] | ✅          |
|                                [aria-proptypes] |                                            |             |
|                                     [aria-role] |                                            |             |
|                     [aria-unsupported-elements] |                [noAriaUnsupportedElements] | ✅          |
|                            [autocomplete-valid] |                                            |             |
|                  [click-events-have-key-events] |                    [useKeyWithClickEvents] | ✅          |
|                           [heading-has-content] |                        [useHeadingContent] |             |
|                                 [html-has-lang] |                              [useHtmlLang] | ✅          |
|                              [iframe-has-title] |                           [useIframeTitle] | ✅          |
|                             [img-redundant-alt] |                           [noRedundantAlt] | ✅          |
|                    [interactive-supports-focus] |                                            |             |
|                  [label-has-associated-control] |                                            |             |
|                             [media-has-caption] |                          [useMediaCaption] | ✅          |
|                  [mouse-events-have-key-events] |                    [useKeyWithMouseEvents] | ✅          |
|                                 [no-access-key] |                              [noAccessKey] |             |
|                                  [no-autofocus] |                              [noAutofocus] | ✅          |
|                       [no-distracting-elements] |                    [noDistractingElements] | ✅          |
| [no-interactive-element-to-noninteractive-role] |                                            |             |
|        [no-noninteractive-element-interactions] |                                            |             |
| [no-noninteractive-element-to-interactive-role] | [noNoninteractiveElementToInteractiveRole] | ✅          |
|                    [no-noninteractive-tabindex] |                 [noNoninteractiveTabindex] | ✅          |
|                            [no-redundant-roles] |                         [noRedundantRoles] | ✅          |
|                [no-static-element-interactions] |                                            |             |
|                  [role-has-required-aria-props] |                      [useAriaPropsForRole] | ✅          |
|                      [role-supports-aria-props] |                                            |             |
|                                         [scope] |                            [noHeaderScope] | ✅          |
|                          [tabindex-no-positive] |                       [noPositiveTabindex] | ✅          |

## eslint-plugin-react/react-hooks recommended

eslint-plugin-react の recommended ルールの中には、hooks が登場する以前のクラスコンポーネントなどに関連する古いルールも多く含まれています。古いルールを除いた対応表は次のようになります。

|          react recommended |                                   Biome | recommended |
| -------------------------: | --------------------------------------: | ----------- |
|                  [jsx-key] |                                         |             |
| [jsx-no-comment-textnodes] |                         [noCommentText] | ✅          |
|   [jsx-no-duplicate-props] |                   [noDuplicateJsxProps] | ✅          |
|      [jsx-no-target-blank] |                         [noBlankTarget] | ✅          |
|             [jsx-no-undef] |                 [noUndeclaredVariables] |             |
|            [jsx-uses-vars] |                     [noUnusedVariables] |             |
|         [no-children-prop] |                        [noChildrenProp] | ✅          |
|  [no-danger-with-children] | [noDangerouslySetInnerHtmlWithChildren] | ✅          |

eslint-plugin-react-hooks の対応表は次のようになります。

| react-hooks recommended |                       Biome | recommended |
| ----------------------: | --------------------------: | ----------- |
|       [exhaustive-deps] | [useExhaustiveDependencies] |             |
|        [rules-of-hooks] |         [useHookAtTopLevel] |             |

## 最後に

Biome の discussion の投稿を元に、ESLint 関連の recommended ルールにフォーカスした対応表を作ってみました。コントリビューターとしては、思ったより a11y のルールの実装が足りてなかったことに気づけてよかったです。

ちなみに、Biome が対応してないルールの中には、parser や formatter などで部分的に対応がされているものがあります。例えば [no-octal] のケースでは、パーサーがエラーを報告し、エディタで確認することができます。

https://biomejs.dev/playground/?indentStyle=space&quoteStyle=single&trailingComma=none&code=YwBvAG4AcwB0ACAAbgB1AG0AIAA9ACAAMAA3ADEAOwA%3D

また、Biome は linter だけではなく [import 文をソートする機能](https://biomejs.dev/analyzer/#imports-sorting)も含む formatter の機能も使えるので、formatter や linter の設定で楽をしたい人は是非使ってみてもらえるとよいと思います。

Biome の Discord に日本語のチャンネルも作ってみたので、Biome について質問したい方がいれば次のリンクからでもお待ちしています。

https://discord.com/channels/1132231889290285117/1150433265308676097

<!-- ESLint rules -->

[no-mixed-spaces-and-tabs]: https://eslint.org/docs/latest/rules/no-mixed-spaces-and-tabs
[constructor-super]: https://eslint.org/docs/latest/rules/constructor-super/
[for-direction]: https://eslint.org/docs/latest/rules/for-direction/
[getter-return]: https://eslint.org/docs/latest/rules/getter-return/
[no-async-promise-executor]: https://eslint.org/docs/latest/rules/no-async-promise-executor/
[no-case-declarations]: https://eslint.org/docs/latest/rules/no-case-declarations/
[no-class-assign]: https://eslint.org/docs/latest/rules/no-class-assign/
[no-compare-neg-zero]: https://eslint.org/docs/latest/rules/no-compare-neg-zero/
[no-cond-assign]: https://eslint.org/docs/latest/rules/no-cond-assign/
[no-const-assign]: https://eslint.org/docs/latest/rules/no-const-assign/
[no-constant-condition]: https://eslint.org/docs/latest/rules/no-constant-condition/
[no-control-regex]: https://eslint.org/docs/latest/rules/no-control-regex/
[no-debugger]: https://eslint.org/docs/latest/rules/no-debugger/
[no-delete-var]: https://eslint.org/docs/latest/rules/no-delete-var/
[no-dupe-args]: https://eslint.org/docs/latest/rules/no-dupe-args/
[no-dupe-class-members]: https://eslint.org/docs/latest/rules/no-dupe-class-members/
[no-dupe-else-if]: https://eslint.org/docs/latest/rules/no-dupe-else-if/
[no-dupe-keys]: https://eslint.org/docs/latest/rules/no-dupe-keys/
[no-duplicate-case]: https://eslint.org/docs/latest/rules/no-duplicate-case/
[no-empty-character-class]: https://eslint.org/docs/latest/rules/no-empty-character-class/
[no-empty-pattern]: https://eslint.org/docs/latest/rules/no-empty-pattern/
[no-empty]: https://eslint.org/docs/latest/rules/no-empty/
[no-ex-assign]: https://eslint.org/docs/latest/rules/no-ex-assign/
[no-extra-boolean-cast]: https://eslint.org/docs/latest/rules/no-extra-boolean-cast/
[no-fallthrough]: https://eslint.org/docs/latest/rules/no-fallthrough/
[no-func-assign]: https://eslint.org/docs/latest/rules/no-func-assign/
[no-global-assign]: https://eslint.org/docs/latest/rules/no-global-assign/
[no-import-assign]: https://eslint.org/docs/latest/rules/no-import-assign/
[no-inner-declarations]: https://eslint.org/docs/latest/rules/no-inner-declarations/
[no-irregular-whitespace]: https://eslint.org/docs/latest/rules/no-irregular-whitespace/
[no-loss-of-precision]: https://eslint.org/docs/latest/rules/no-loss-of-precision/
[no-misleading-character-class]: https://eslint.org/docs/latest/rules/no-misleading-character-class/
[no-new-symbol]: https://eslint.org/docs/latest/rules/no-new-symbol/
[no-nonoctal-decimal-escape]: https://eslint.org/docs/latest/rules/no-nonoctal-decimal-escape/
[no-obj-calls]: https://eslint.org/docs/latest/rules/no-obj-calls/
[no-prototype-builtins]: https://eslint.org/docs/latest/rules/no-prototype-builtins/
[no-redeclare]: https://eslint.org/docs/latest/rules/no-redeclare/
[no-regex-spaces]: https://eslint.org/docs/latest/rules/no-regex-spaces/
[no-self-assign]: https://eslint.org/docs/latest/rules/no-self-assign/
[no-setter-return]: https://eslint.org/docs/latest/rules/no-setter-return/
[no-shadow-restricted-names]: https://eslint.org/docs/latest/rules/no-shadow-restricted-names/
[no-sparse-arrays]: https://eslint.org/docs/latest/rules/no-sparse-arrays/
[no-this-before-super]: https://eslint.org/docs/latest/rules/no-this-before-super/
[no-undef]: https://eslint.org/docs/latest/rules/no-undef/
[no-unreachable]: https://eslint.org/docs/latest/rules/no-unreachable/
[no-unsafe-finally]: https://eslint.org/docs/latest/rules/no-unsafe-finally/
[no-unsafe-negation]: https://eslint.org/docs/latest/rules/no-unsafe-negation/
[no-unsafe-optional-chaining]: https://eslint.org/docs/latest/rules/no-unsafe-optional-chaining/
[no-unused-labels]: https://eslint.org/docs/latest/rules/no-unused-labels/
[no-unused-vars]: https://eslint.org/docs/latest/rules/no-unused-vars/
[no-useless-backreference]: https://eslint.org/docs/latest/rules/no-useless-backreference/
[no-useless-catch]: https://eslint.org/docs/latest/rules/no-useless-catch/
[no-useless-escape]: https://eslint.org/docs/latest/rules/no-useless-escape/
[no-with]: https://eslint.org/docs/latest/rules/no-with/
[require-yield]: https://eslint.org/docs/latest/rules/require-yield/
[use-isnan]: https://eslint.org/docs/latest/rules/use-isnan/
[valid-typeof]: https://eslint.org/docs/latest/rules/valid-typeof/
[no-invalid-regexp]: https://eslint.org/docs/latest/rules/no-invalid-regexp
[no-octal]: https://eslint.org/docs/latest/rules/no-octal
[no-unexpected-multiline]: https://eslint.org/docs/latest/rules/no-unexpected-multiline

<!-- TypeScript ESLint rules -->

[ban-ts-comment]: https://typescript-eslint.io/rules/no-var-requires/
[ban-types]: https://typescript-eslint.io/rules/ban-types/
[no-duplicate-enum-values]: https://typescript-eslint.io/rules/no-duplicate-enum-values/
[no-explicit-any]: https://typescript-eslint.io/rules/no-explicit-any/
[no-extra-non-null-assertion]: https://typescript-eslint.io/rules/no-extra-non-null-assertion/
[no-misused-new]: https://typescript-eslint.io/rules/no-misused-new/
[no-namespace]: https://typescript-eslint.io/rules/no-namespace/
[no-non-null-asserted-optional-chain]: https://typescript-eslint.io/rules/no-non-null-asserted-optional-chain/
[no-this-alias]: https://typescript-eslint.io/rules/no-this-alias/
[no-unnecessary-type-constraint]: https://typescript-eslint.io/rules/no-unnecessary-type-constraint/
[no-unsafe-declaration-merging]: https://typescript-eslint.io/rules/no-unsafe-declaration-merging/
[no-var-requires]: https://typescript-eslint.io/rules/no-var-requires/
[prefer-as-const]: https://typescript-eslint.io/rules/prefer-as-const/
[triple-slash-reference]: https://typescript-eslint.io/rules/triple-slash-reference/

<!-- eslint-plugin-jsx-a11y rules -->

[alt-text]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/alt-text.md
[anchor-has-content]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/anchor-has-content.md
[anchor-is-valid]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/anchor-is-valid.md
[aria-activedescendant-has-tabindex]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/aria-activedescendant-has-tabindex.md
[aria-props]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/aria-props.md
[aria-proptypes]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/aria-proptypes.md
[aria-role]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/aria-role.md
[aria-unsupported-elements]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/aria-unsupported-elements.md
[autocomplete-valid]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/autocomplete-valid.md
[click-events-have-key-events]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/click-events-have-key-events.md
[heading-has-content]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/heading-has-content.md
[html-has-lang]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/html-has-lang.md
[iframe-has-title]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/iframe-has-title.md
[img-redundant-alt]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/img-redundant-alt.md
[interactive-supports-focus]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/interactive-supports-focus.md
[label-has-associated-control]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/label-has-associated-control.md
[media-has-caption]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/media-has-caption.md
[mouse-events-have-key-events]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/mouse-events-have-key-events.md
[no-access-key]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/no-access-key.md
[no-autofocus]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/no-autofocus.md
[no-distracting-elements]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/no-distracting-elements.md
[no-interactive-element-to-noninteractive-role]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/no-interactive-element-to-noninteractive-role.md
[no-noninteractive-element-interactions]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/no-noninteractive-element-interactions.md
[no-noninteractive-element-to-interactive-role]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/no-noninteractive-element-to-interactive-role.md
[no-noninteractive-tabindex]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/no-noninteractive-tabindex.md
[no-redundant-roles]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/no-redundant-roles.md
[no-static-element-interactions]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/no-static-element-interactions.md
[role-has-required-aria-props]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/role-has-required-aria-props.md
[role-supports-aria-props]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/role-supports-aria-props.md
[scope]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/scope.md
[tabindex-no-positive]: https://github.com/jsx-eslint/eslint-plugin-jsx-a11y/blob/main/docs/rules/tabindex-no-positive.md

<!-- eslint-plugin-react rules -->

[jsx-key]: https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-key.md
[jsx-no-comment-textnodes]: https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-no-comment-textnodes.md
[jsx-no-duplicate-props]: https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-no-duplicate-props.md
[jsx-no-target-blank]: https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-no-target-blank.md
[jsx-no-undef]: https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-no-undef.md
[jsx-uses-vars]: https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-uses-vars.md
[no-children-prop]: https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/no-children-prop.md
[no-danger-with-children]: https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/no-danger-with-children.md
[exhaustive-deps]: https://github.com/facebook/react/blob/main/packages/eslint-plugin-react-hooks/README.md
[rules-of-hooks]: https://github.com/facebook/react/blob/main/packages/eslint-plugin-react-hooks/README.md

<!-- Biome rules -->

[noAccessKey]: https://biomejs.dev/linter/rules/no-access-key/
[noAccumulatingSpread]: https://biomejs.dev/linter/rules/no-accumulating-spread/
[noArguments]: https://biomejs.dev/linter/rules/no-arguments/
[noAriaUnsupportedElements]: https://biomejs.dev/linter/rules/no-aria-unsupported-elements/
[noArrayIndexKey]: https://biomejs.dev/linter/rules/no-array-index-key/
[noAssignInExpressions]: https://biomejs.dev/linter/rules/no-assign-in-expressions/
[noAsyncPromiseExecutor]: https://biomejs.dev/linter/rules/no-async-promise-executor/
[noAutofocus]: https://biomejs.dev/linter/rules/no-autofocus/
[noBannedTypes]: https://biomejs.dev/linter/rules/no-banned-types/
[noBlankTarget]: https://biomejs.dev/linter/rules/no-blank-target/
[noCatchAssign]: https://biomejs.dev/linter/rules/no-catch-assign/
[noChildrenProp]: https://biomejs.dev/linter/rules/no-children-prop/
[noClassAssign]: https://biomejs.dev/linter/rules/no-class-assign/
[noCommaOperator]: https://biomejs.dev/linter/rules/no-comma-operator/
[noCommentText]: https://biomejs.dev/linter/rules/no-comment-text/
[noCompareNegZero]: https://biomejs.dev/linter/rules/no-compare-neg-zero/
[noConfusingLabels]: https://biomejs.dev/linter/rules/no-confusing-labels/
[noConfusingVoidType]: https://biomejs.dev/linter/rules/no-confusing-void-type/
[noConsoleLog]: https://biomejs.dev/linter/rules/no-console-log/
[noConstantCondition]: https://biomejs.dev/linter/rules/no-constant-condition/
[noConstAssign]: https://biomejs.dev/linter/rules/no-const-assign/
[noConstEnum]: https://biomejs.dev/linter/rules/no-const-enum/
[noConstructorReturn]: https://biomejs.dev/linter/rules/no-constructor-return/
[noControlCharactersInRegex]: https://biomejs.dev/linter/rules/no-control-characters-in-regex/
[noDangerouslySetInnerHtml]: https://biomejs.dev/linter/rules/no-dangerously-set-inner-html/
[noDangerouslySetInnerHtmlWithChildren]: https://biomejs.dev/linter/rules/no-dangerously-set-inner-html-with-children/
[noDebugger]: https://biomejs.dev/linter/rules/no-debugger/
[noDelete]: https://biomejs.dev/linter/rules/no-delete/
[noDistractingElements]: https://biomejs.dev/linter/rules/no-distracting-elements/
[noDoubleEquals]: https://biomejs.dev/linter/rules/no-double-equals/
[noDuplicateCase]: https://biomejs.dev/linter/rules/no-duplicate-case/
[noDuplicateClassMembers]: https://biomejs.dev/linter/rules/no-duplicate-class-members/
[noDuplicateJsonKeys]: https://biomejs.dev/linter/rules/no-duplicate-json-keys/
[noDuplicateJsxProps]: https://biomejs.dev/linter/rules/no-duplicate-jsx-props/
[noDuplicateObjectKeys]: https://biomejs.dev/linter/rules/no-duplicate-object-keys/
[noDuplicateParameters]: https://biomejs.dev/linter/rules/no-duplicate-parameters/
[noEmptyCharacterClassInRegex]: https://biomejs.dev/linter/rules/no-empty-character-class-in-regex/
[noEmptyInterface]: https://biomejs.dev/linter/rules/no-empty-interface/
[noEmptyPattern]: https://biomejs.dev/linter/rules/no-empty-pattern/
[noExcessiveComplexity]: https://biomejs.dev/linter/rules/no-excessive-complexity/
[noExplicitAny]: https://biomejs.dev/linter/rules/no-explicit-any/
[noExtraBooleanCast]: https://biomejs.dev/linter/rules/no-extra-boolean-cast/
[noExtraNonNullAssertion]: https://biomejs.dev/linter/rules/no-extra-non-null-assertion/
[noFallthroughSwitchClause]: https://biomejs.dev/linter/rules/no-fallthrough-switch-clause/
[noForEach]: https://biomejs.dev/linter/rules/no-for-each/
[noFunctionAssign]: https://biomejs.dev/linter/rules/no-function-assign/
[noGlobalIsFinite]: https://biomejs.dev/linter/rules/no-global-is-finite/
[noGlobalIsNan]: https://biomejs.dev/linter/rules/no-global-is-nan/
[noGlobalObjectCalls]: https://biomejs.dev/linter/rules/no-global-object-calls/
[noHeaderScope]: https://biomejs.dev/linter/rules/no-header-scope/
[noImplicitBoolean]: https://biomejs.dev/linter/rules/no-implicit-boolean/
[noImportAssign]: https://biomejs.dev/linter/rules/no-import-assign/
[noInferrableTypes]: https://biomejs.dev/linter/rules/no-inferrable-types/
[noInnerDeclarations]: https://biomejs.dev/linter/rules/no-inner-declarations/
[noInvalidConstructorSuper]: https://biomejs.dev/linter/rules/no-invalid-constructor-super/
[noInvalidNewBuiltin]: https://biomejs.dev/linter/rules/no-invalid-new-builtin/
[noLabelVar]: https://biomejs.dev/linter/rules/no-label-var/
[noMisleadingInstantiator]: https://biomejs.dev/linter/rules/no-misleading-instantiator/
[noMultipleSpacesInRegularExpressionLiterals]: https://biomejs.dev/linter/rules/no-multiple-spaces-in-regular-expression-literals/
[noNamespace]: https://biomejs.dev/linter/rules/no-namespace/
[noNegationElse]: https://biomejs.dev/linter/rules/no-negation-else/
[noNewSymbol]: https://biomejs.dev/linter/rules/no-new-symbol/
[noNoninteractiveElementToInteractiveRole]: https://biomejs.dev/linter/rules/no-noninteractive-element-to-interactive-role/
[noNoninteractiveTabindex]: https://biomejs.dev/linter/rules/no-noninteractive-tabindex/
[noNonNullAssertion]: https://biomejs.dev/linter/rules/no-non-null-assertion/
[noNonoctalDecimalEscape]: https://biomejs.dev/linter/rules/no-nonoctal-decimal-escape/
[noParameterAssign]: https://biomejs.dev/linter/rules/no-parameter-assign/
[noParameterProperties]: https://biomejs.dev/linter/rules/no-parameter-properties/
[noPositiveTabindex]: https://biomejs.dev/linter/rules/no-positive-tabindex/
[noPrecisionLoss]: https://biomejs.dev/linter/rules/no-precision-loss/
[noPrototypeBuiltins]: https://biomejs.dev/linter/rules/no-prototype-builtins/
[noRedeclare]: https://biomejs.dev/linter/rules/no-redeclare/
[noRedundantAlt]: https://biomejs.dev/linter/rules/no-redundant-alt/
[noRedundantRoles]: https://biomejs.dev/linter/rules/no-redundant-roles/
[noRedundantUseStrict]: https://biomejs.dev/linter/rules/no-redundant-use-strict/
[noRenderReturnValue]: https://biomejs.dev/linter/rules/no-render-return-value/
[noRestrictedGlobals]: https://biomejs.dev/linter/rules/no-restricted-globals/
[noSelfAssign]: https://biomejs.dev/linter/rules/no-self-assign/
[noSelfCompare]: https://biomejs.dev/linter/rules/no-self-compare/
[noSetterReturn]: https://biomejs.dev/linter/rules/no-setter-return/
[noShadowRestrictedNames]: https://biomejs.dev/linter/rules/no-shadow-restricted-names/
[noShoutyConstants]: https://biomejs.dev/linter/rules/no-shouty-constants/
[noSparseArray]: https://biomejs.dev/linter/rules/no-sparse-array/
[noStaticOnlyClass]: https://biomejs.dev/linter/rules/no-static-only-class/
[noStringCaseMismatch]: https://biomejs.dev/linter/rules/no-string-case-mismatch/
[noSvgWithoutTitle]: https://biomejs.dev/linter/rules/no-svg-without-title/
[noSwitchDeclarations]: https://biomejs.dev/linter/rules/no-switch-declarations/
[noUndeclaredVariables]: https://biomejs.dev/linter/rules/no-undeclared-variables/
[noUnnecessaryContinue]: https://biomejs.dev/linter/rules/no-unnecessary-continue/
[noUnreachable]: https://biomejs.dev/linter/rules/no-unreachable/
[noUnreachableSuper]: https://biomejs.dev/linter/rules/no-unreachable-super/
[noUnsafeDeclarationMerging]: https://biomejs.dev/linter/rules/no-unsafe-declaration-merging/
[noUnsafeFinally]: https://biomejs.dev/linter/rules/no-unsafe-finally/
[noUnsafeNegation]: https://biomejs.dev/linter/rules/no-unsafe-negation/
[noUnsafeOptionalChaining]: https://biomejs.dev/linter/rules/no-unsafe-optional-chaining/
[noUnusedLabels]: https://biomejs.dev/linter/rules/no-unused-labels/
[noUnusedTemplateLiteral]: https://biomejs.dev/linter/rules/no-unused-template-literal/
[noUnusedVariables]: https://biomejs.dev/linter/rules/no-unused-variables/
[noUselessCatch]: https://biomejs.dev/linter/rules/no-useless-catch/
[noUselessConstructor]: https://biomejs.dev/linter/rules/no-useless-constructor/
[noUselessElse]: https://biomejs.dev/linter/rules/no-useless-else/
[noUselessEmptyExport]: https://biomejs.dev/linter/rules/no-useless-empty-export/
[noUselessFragments]: https://biomejs.dev/linter/rules/no-useless-fragments/
[noUselessLabel]: https://biomejs.dev/linter/rules/no-useless-label/
[noUselessRename]: https://biomejs.dev/linter/rules/no-useless-rename/
[noUselessSwitchCase]: https://biomejs.dev/linter/rules/no-useless-switch-case/
[noUselessThisAlias]: https://biomejs.dev/linter/rules/no-useless-this-alias/
[noUselessTypeConstraint]: https://biomejs.dev/linter/rules/no-useless-type-constraint/
[noVar]: https://biomejs.dev/linter/rules/no-var/
[noVoid]: https://biomejs.dev/linter/rules/no-void/
[noVoidElementsWithChildren]: https://biomejs.dev/linter/rules/no-void-elements-with-children/
[noVoidTypeReturn]: https://biomejs.dev/linter/rules/no-void-type-return/
[noWith]: https://biomejs.dev/linter/rules/no-with/
[useAltText]: https://biomejs.dev/linter/rules/use-alt-text/
[useAnchorContent]: https://biomejs.dev/linter/rules/use-anchor-content/
[useAriaPropsForRole]: https://biomejs.dev/linter/rules/use-aria-props-for-role/
[useAriaPropTypes]: https://biomejs.dev/linter/rules/use-aria-prop-types/
[useArrowFunction]: https://biomejs.dev/linter/rules/use-arrow-function/
[useAsConstAssertion]: https://biomejs.dev/linter/rules/use-as-const-assertion/
[useBlockStatements]: https://biomejs.dev/linter/rules/use-block-statements/
[useButtonType]: https://biomejs.dev/linter/rules/use-button-type/
[useCollapsedElseIf]: https://biomejs.dev/linter/rules/use-collapsed-else-if/
[useConst]: https://biomejs.dev/linter/rules/use-const/
[useDefaultParameterLast]: https://biomejs.dev/linter/rules/use-default-parameter-last/
[useDefaultSwitchClauseLast]: https://biomejs.dev/linter/rules/use-default-switch-clause-last/
[useEnumInitializers]: https://biomejs.dev/linter/rules/use-enum-initializers/
[useExhaustiveDependencies]: https://biomejs.dev/linter/rules/use-exhaustive-dependencies/
[useExponentiationOperator]: https://biomejs.dev/linter/rules/use-exponentiation-operator/
[useFlatMap]: https://biomejs.dev/linter/rules/use-flat-map/
[useFragmentSyntax]: https://biomejs.dev/linter/rules/use-fragment-syntax/
[useGetterReturn]: https://biomejs.dev/linter/rules/use-getter-return/
[useGroupedTypeImport]: https://biomejs.dev/linter/rules/use-grouped-type-import/
[useHeadingContent]: https://biomejs.dev/linter/rules/use-heading-content/
[useHookAtTopLevel]: https://biomejs.dev/linter/rules/use-hook-at-top-level/
[useHtmlLang]: https://biomejs.dev/linter/rules/use-html-lang/
[useIframeTitle]: https://biomejs.dev/linter/rules/use-iframe-title/
[useImportRestrictions]: https://biomejs.dev/linter/rules/use-import-restrictions/
[useIsArray]: https://biomejs.dev/linter/rules/use-is-array/
[useIsNan]: https://biomejs.dev/linter/rules/use-is-nan/
[useKeyWithClickEvents]: https://biomejs.dev/linter/rules/use-key-with-click-events/
[useKeyWithMouseEvents]: https://biomejs.dev/linter/rules/use-key-with-mouse-events/
[useLiteralEnumMembers]: https://biomejs.dev/linter/rules/use-literal-enum-members/
[useLiteralKeys]: https://biomejs.dev/linter/rules/use-literal-keys/
[useMediaCaption]: https://biomejs.dev/linter/rules/use-media-caption/
[useNamespaceKeyword]: https://biomejs.dev/linter/rules/use-namespace-keyword/
[useNamingConvention]: https://biomejs.dev/linter/rules/use-naming-convention/
[useNumericLiterals]: https://biomejs.dev/linter/rules/use-numeric-literals/
[useOptionalChain]: https://biomejs.dev/linter/rules/use-optional-chain/
[useSelfClosingElements]: https://biomejs.dev/linter/rules/use-self-closing-elements/
[useShorthandArrayType]: https://biomejs.dev/linter/rules/use-shorthand-array-type/
[useShorthandAssign]: https://biomejs.dev/linter/rules/use-shorthand-assign/
[useSimpleNumberKeys]: https://biomejs.dev/linter/rules/use-simple-number-keys/
[useSimplifiedLogicExpression]: https://biomejs.dev/linter/rules/use-simplified-logic-expression/
[useSingleCaseStatement]: https://biomejs.dev/linter/rules/use-single-case-statement/
[useSingleVarDeclarator]: https://biomejs.dev/linter/rules/use-single-var-declarator/
[useTemplate]: https://biomejs.dev/linter/rules/use-template/
[useValidAnchor]: https://biomejs.dev/linter/rules/use-valid-anchor/
[useValidAriaProps]: https://biomejs.dev/linter/rules/use-valid-aria-props/
[useValidForDirection]: https://biomejs.dev/linter/rules/use-valid-for-direction/
[useValidLang]: https://biomejs.dev/linter/rules/use-valid-lang/
[useValidTypeof]: https://biomejs.dev/linter/rules/use-valid-typeof/
[useWhile]: https://biomejs.dev/linter/rules/use-while/
[useYield]: https://biomejs.dev/linter/rules/use-yield/

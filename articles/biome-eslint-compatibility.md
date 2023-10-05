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

|              ESlint recommended |                       Biome |
| ------------------------------: | --------------------------: |
|             [constructor-super] | [noInvalidConstructorSuper] |
|                 [for-direction] |      [useValidForDirection] |
|                 [getter-return] |           [useGetterReturn] |
|     [no-async-promise-executor] | [noInvalidConstructorSuper] |
|               [no-class-assign] | [noInvalidConstructorSuper] |
|           [no-compare-neg-zero] | [noInvalidConstructorSuper] |
|                [no-cond-assign] | [noInvalidConstructorSuper] |
|               [no-const-assign] | [noInvalidConstructorSuper] |
|         [no-constant-condition] | [noInvalidConstructorSuper] |
|              [no-control-regex] | [noInvalidConstructorSuper] |
|                   [no-debugger] | [noInvalidConstructorSuper] |
|                  [no-dupe-keys] | [noInvalidConstructorSuper] |
|             [no-duplicate-case] | [noInvalidConstructorSuper] |
|      [no-empty-character-class] | [noInvalidConstructorSuper] |
|                  [no-ex-assign] | [noInvalidConstructorSuper] |
|                [no-fallthrough] | [noInvalidConstructorSuper] |
|                [no-func-assign] | [noInvalidConstructorSuper] |
|              [no-import-assign] | [noInvalidConstructorSuper] |
|         [no-inner-declarations] | [noInvalidConstructorSuper] |
|             [no-invalid-regexp] | [noInvalidConstructorSuper] |
|       [no-irregular-whitespace] | [noInvalidConstructorSuper] |
|          [no-loss-of-precision] | [noInvalidConstructorSuper] |
| [no-misleading-character-class] | [noInvalidConstructorSuper] |
|                 [no-new-symbol] | [noInvalidConstructorSuper] |
|                  [no-obj-calls] | [noInvalidConstructorSuper] |
|         [no-prototype-builtins] | [noInvalidConstructorSuper] |
|                [no-self-assign] | [noInvalidConstructorSuper] |
|              [no-setter-return] | [noInvalidConstructorSuper] |
|              [no-sparse-arrays] | [noInvalidConstructorSuper] |
|          [no-this-before-super] | [noInvalidConstructorSuper] |
|                      [no-undef] | [noInvalidConstructorSuper] |
|       [no-unexpected-multiline] | [noInvalidConstructorSuper] |
|                [no-unreachable] | [noInvalidConstructorSuper] |
|             [no-unsafe-finally] | [noInvalidConstructorSuper] |
|            [no-unsafe-negation] | [noInvalidConstructorSuper] |
|   [no-unsafe-optional-chaining] | [noInvalidConstructorSuper] |
|                [no-unused-vars] | [noInvalidConstructorSuper] |
|      [no-useless-backreference] | [noInvalidConstructorSuper] |
|                     [use-isnan] | [noInvalidConstructorSuper] |
|                  [valid-typeof] | [noInvalidConstructorSuper] |
|          [no-case-declarations] | [noInvalidConstructorSuper] |
|                 [no-delete-var] | [noInvalidConstructorSuper] |
|                      [no-empty] | [noInvalidConstructorSuper] |
|         [no-extra-boolean-cast] | [noInvalidConstructorSuper] |
|                 [no-extra-semi] | [noInvalidConstructorSuper] |
|              [no-global-assign] | [noInvalidConstructorSuper] |
|    [no-nonoctal-decimal-escape] | [noInvalidConstructorSuper] |
|                      [no-octal] | [noInvalidConstructorSuper] |
|                  [no-redeclare] | [noInvalidConstructorSuper] |
|               [no-regex-spaces] | [noInvalidConstructorSuper] |
|              [no-unused-labels] | [noInvalidConstructorSuper] |
|              [no-useless-catch] | [noInvalidConstructorSuper] |
|             [no-useless-escape] | [noInvalidConstructorSuper] |
|                       [no-with] | [noInvalidConstructorSuper] |
|                 [require-yield] | [noInvalidConstructorSuper] |
|       [no-mixed-space-and-tabs] | [noInvalidConstructorSuper] |

## TypeScript ESLint recommended

## eslint-plugin-jsx-a11y recommended

## eslint-plugin-react recommended

## eslint-plugin-react-hooks recommended

<!-- ESLint rules -->

[constructor-super]: https://eslint.org/docs/latest/rules/constructor-super/
[default-case-last]: https://eslint.org/docs/latest/rules/default-case-last/
[default-param-last]: https://eslint.org/docs/latest/rules/default-param-last/
[dot-notation]: https://eslint.org/docs/latest/rules/dot-notation/
[eqeqeq]: https://eslint.org/docs/latest/rules/eqeqeq/
[for-direction]: https://eslint.org/docs/latest/rules/for-direction/
[getter-return]: https://eslint.org/docs/latest/rules/getter-return/
[no-async-promise-executor]: https://eslint.org/docs/latest/rules/no-async-promise-executor/
[no-case-declarations]: https://eslint.org/docs/latest/rules/no-case-declarations/
[no-class-assign]: https://eslint.org/docs/latest/rules/no-class-assign/
[no-compare-neg-zero]: https://eslint.org/docs/latest/rules/no-compare-neg-zero/
[no-cond-assign]: https://eslint.org/docs/latest/rules/no-cond-assign/
[no-console]: https://eslint.org/docs/latest/rules/no-console/
[no-const-assign]: https://eslint.org/docs/latest/rules/no-const-assign/
[no-constant-condition]: https://eslint.org/docs/latest/rules/no-constant-condition/
[no-constructor-return]: https://eslint.org/docs/latest/rules/no-constructor-return/
[no-control-regex]: https://eslint.org/docs/latest/rules/no-control-regex/
[no-debugger]: https://eslint.org/docs/latest/rules/no-debugger/
[no-delete-var]: https://eslint.org/docs/latest/rules/no-delete-var/
[no-dupe-args]: https://eslint.org/docs/latest/rules/no-dupe-args/
[no-dupe-class-members]: https://eslint.org/docs/latest/rules/no-dupe-class-members/
[no-dupe-else-if]: https://eslint.org/docs/latest/rules/no-dupe-else-if/
[no-dupe-keys]: https://eslint.org/docs/latest/rules/no-dupe-keys/
[no-duplicate-case]: https://eslint.org/docs/latest/rules/no-duplicate-case/
[no-else-return]: https://eslint.org/docs/latest/rules/no-else-return/
[no-empty-character-class]: https://eslint.org/docs/latest/rules/no-empty-character-class/
[no-empty-pattern]: https://eslint.org/docs/latest/rules/no-empty-pattern/
[no-empty-static-block]: https://eslint.org/docs/latest/rules/no-empty-static-block/
[no-empty]: https://eslint.org/docs/latest/rules/no-empty/
[no-eval]: https://eslint.org/docs/latest/rules/no-eval/
[no-ex-assign]: https://eslint.org/docs/latest/rules/no-ex-assign/
[no-extra-boolean-cast]: https://eslint.org/docs/latest/rules/no-extra-boolean-cast/
[no-extra-label]: https://eslint.org/docs/latest/rules/no-extra-label/
[no-fallthrough]: https://eslint.org/docs/latest/rules/no-fallthrough/
[no-func-assign]: https://eslint.org/docs/latest/rules/no-func-assign/
[no-global-assign]: https://eslint.org/docs/latest/rules/no-global-assign/
[no-implicit-coercion]: https://eslint.org/docs/latest/rules/no-implicit-coercion/
[no-import-assign]: https://eslint.org/docs/latest/rules/no-import-assign/
[no-inner-declarations]: https://eslint.org/docs/latest/rules/no-inner-declarations/
[no-irregular-whitespace]: https://eslint.org/docs/latest/rules/no-irregular-whitespace/
[no-label-var]: https://eslint.org/docs/latest/rules/no-label-var/
[no-labels]: https://eslint.org/docs/latest/rules/no-labels/
[no-lone-blocks]: https://eslint.org/docs/latest/rules/no-lone-blocks/
[no-lonely-if]: https://eslint.org/docs/latest/rules/no-lonely-if/
[no-loss-of-precision]: https://eslint.org/docs/latest/rules/no-loss-of-precision/
[no-misleading-character-class]: https://eslint.org/docs/latest/rules/no-misleading-character-class/
[no-negated-condition]: https://eslint.org/docs/latest/rules/no-negated-condition/
[no-new-native-nonconstructor]: https://eslint.org/docs/latest/rules/no-new-native-nonconstructor/
[no-new-symbol]: https://eslint.org/docs/latest/rules/no-new-symbol/
[no-new-wrappers]: https://eslint.org/docs/latest/rules/no-new-wrappers/
[no-nonoctal-decimal-escape]: https://eslint.org/docs/latest/rules/no-nonoctal-decimal-escape/
[no-obj-calls]: https://eslint.org/docs/latest/rules/no-obj-calls/
[no-param-reassign]: https://eslint.org/docs/latest/rules/no-param-reassign/
[no-prototype-builtins]: https://eslint.org/docs/latest/rules/no-prototype-builtins/
[no-restricted-globals]: https://eslint.org/docs/latest/rules/no-restricted-globals/
[no-redeclare]: https://eslint.org/docs/latest/rules/no-redeclare/
[no-regex-spaces]: https://eslint.org/docs/latest/rules/no-regex-spaces/
[no-self-assign]: https://eslint.org/docs/latest/rules/no-self-assign/
[no-self-compare]: https://eslint.org/docs/latest/rules/no-self-compare/
[no-sequences]: https://eslint.org/docs/latest/rules/no-sequences/
[no-setter-return]: https://eslint.org/docs/latest/rules/no-setter-return/
[no-shadow-restricted-names]: https://eslint.org/docs/latest/rules/no-shadow-restricted-names/
[no-sparse-array]: https://eslint.org/docs/latest/rules/no-sparse-array/
[no-this-before-super]: https://eslint.org/docs/latest/rules/no-this-before-super/
[no-undef-init]: https://eslint.org/docs/latest/rules/no-undef-init/
[no-undef]: https://eslint.org/docs/latest/rules/no-undef/
[no-unneeded-ternary]: https://eslint.org/docs/latest/rules/no-unneeded-ternary/
[no-unreachable]: https://eslint.org/docs/latest/rules/no-unreachable/
[no-unsafe-finally]: https://eslint.org/docs/latest/rules/no-unsafe-finally/
[no-unsafe-negation]: https://eslint.org/docs/latest/rules/no-unsafe-negation/
[no-unsafe-optional-chaining]: https://eslint.org/docs/latest/rules/no-unsafe-optional-chaining/
[no-unused-labels]: https://eslint.org/docs/latest/rules/no-unused-labels/
[no-unused-private-class-members]: https://eslint.org/docs/latest/rules/no-unused-private-class-members/
[no-unused-vars]: https://eslint.org/docs/latest/rules/no-unused-vars/
[no-useless-backreference]: https://eslint.org/docs/latest/rules/no-useless-backreference/
[no-useless-call]: https://eslint.org/docs/latest/rules/no-useless-call/
[no-useless-catch]: https://eslint.org/docs/latest/rules/no-useless-catch/
[no-useless-computed-key]: https://eslint.org/docs/latest/rules/no-useless-computed-key/
[no-useless-constructor]: https://eslint.org/docs/latest/rules/no-useless-constructor/
[no-useless-escape]: https://eslint.org/docs/latest/rules/no-useless-escape/
[no-useless-rename]: https://eslint.org/docs/latest/rules/no-useless-rename/
[no-useless-return]: https://eslint.org/docs/latest/rules/no-useless-return/
[no-var]: https://eslint.org/docs/latest/rules/no-var/
[no-void]: https://eslint.org/docs/latest/rules/no-void/
[no-with]: https://eslint.org/docs/latest/rules/no-with/
[object-shorthand]: https://eslint.org/docs/latest/rules/object-shorthand/
[one-var]: https://eslint.org/docs/latest/rules/one-var/
[operator-assignment]: https://eslint.org/docs/latest/rules/operator-assignment/
[prefer-arrow-callback]: https://eslint.org/docs/latest/rules/prefer-arrow-callback/
[prefer-const]: https://eslint.org/docs/latest/rules/prefer-const/
[prefer-destructuring]: https://eslint.org/docs/latest/rules/prefer-destructuring/
[prefer-exponentiation-operator]: https://eslint.org/docs/latest/rules/prefer-exponentiation-operator/
[prefer-numeric-literals]: https://eslint.org/docs/latest/rules/prefer-numeric-literals/
[prefer-object-has-own]: https://eslint.org/docs/latest/rules/prefer-object-has-own/
[prefer-object-spread]: https://eslint.org/docs/latest/rules/prefer-object-spread/
[prefer-regex-literals]: https://eslint.org/docs/latest/rules/prefer-regex-literals/
[prefer-rest-params]: https://eslint.org/docs/latest/rules/prefer-rest-params/
[prefer-template]: https://eslint.org/docs/latest/rules/prefer-template/
[require-unicode-regexp]: https://eslint.org/docs/latest/rules/require-unicode-regexp
[require-yield]: https://eslint.org/docs/latest/rules/require-yield/
[use-isnan]: https://eslint.org/docs/latest/rules/use-isnan/
[valid-typeof]: https://eslint.org/docs/latest/rules/valid-typeof/
[yoda]: https://eslint.org/docs/latest/rules/yoda/

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

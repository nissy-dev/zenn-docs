---
title: "JavaのクラスからTypeScriptの型定義を生成するtypescript-generatorを使ってみた"
emoji: "🐧"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Java", "TypeScript"]
published: false
---

バックエンドを Java、フロントエンドを TypeScript で実装している場合、バックエンドとフロントエンド間のデータのやり取りを型で安全に行いたい場合があります。

データのやり取りの方法には、API や HTML に JSON データを埋め込むなどの方法がありますが、**typescript-generator は どちらのケースにおいても対象のデータのクラスから TypeScript のインターフェイスを生成することができます。** 生成されたインターフェイスを利用することで、フロントエンドにおいても型を利用した安全な実装が可能になります。

この記事では、typescript-generator の使い方や独自で拡張を実装する方法について紹介します。

## typescript-generator とは

typescript-generator は、以下のような Java の JSON クラス[^1]があったときに、

[^1]: JSON にシリアライズできるクラスのことを指します。typescript-generator の README に "Java JSON classes" という表現があるので、そのままの訳を利用しています。

```java
public class Person {
    public String name;
    public int age;
    public boolean hasChildren;
    public List<String> tags;
    public Map<String, String> emails;
}
```

対応する TypeScript のインターフェイスを生成するツールです。

```ts
interface Person {
  name: string;
  age: number;
  hasChildren: boolean;
  tags: string[];
  emails: { [index: string]: string };
}
```

https://github.com/vojtechhabarta/typescript-generator

Java から TypeScript への型の変換は以下の表のように行われ、Java のプリミティブ型だけでなく、Collection や Enum など多くの型に対応しています。

![](/images/typescript-generator/default-type-mapping.png =500x)
_Java から TypeScript への型変換の対応表、https://github.com/vojtechhabarta/typescript-generator/wiki/Type-Mapping#default-type-mapping からの引用_

## インストール方法

Gradle を使っている場合は、`build.gradle` に以下の設定を追加することで導入できます。

```java
apply plugin: 'cz.habarta.typescript-generator'

buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath group: 'cz.habarta.typescript-generator', name: 'typescript-generator-gradle-plugin', version: 'x.y.z'
    }
}

generateTypeScript {
    jsonLibrary = 'jackson2'
    outputKind = 'global'
    outputFile = 'build/sample.d.ts'
}
```

設定を追加した後、 `gradle generateTypeScript` を実行すると、`build/sample.d.ts` に型定義が生成されます。Maven 向けのプラグインも配布されており、Maven や Gradle Kotlin などの場合のインストール方法についても README で紹介されています。

## 各種パラメーターの設定

typescript-generator のパラメーターは、以下のドキュメントを参考にしながら設定していきます。非常に多くのパラメーターが用意されているので、柔軟な型変換が可能な点もこのツールの良いところだと思います。ここでは、必須パラメーターの解説と自分が利用したパラメーターを紹介します。

http://www.habarta.cz/typescript-generator/maven/typescript-generator-maven-plugin/generate-mojo.html

### 必須パラメーター

型変換を行う際の必須パラメーターは、`jsonLibrary` と `outputKind` の 2 つのみです。このため、手軽に試せるところもこのツールの良いところです。

#### `jsonLibrary`

- JSON のクラスを定義する際に利用しているライブラリを指定する
- 指定できる値は `jackson`, `jaxb`, `gson` など

#### `outputKind`

- 出力する TypeScript ファイルの形式を制定する
- `global`、`module`、`ambientModule` のいづれかが指定できる
  - `global` : グローバルスコープで出力する
  - `module` : トップレベルの `export` 宣言で出力する
  - `ambientModule` : `declare module "mod" { }` 形式で出力する

### そのほかの便利なパラメータ

#### `outputFile`

- 生成される型定義ファイルのパスを設定する
- `outputFileType` に対応した拡張子をつける必要がある

#### `outputFileType`

- 出力するファイルのフォーマットを指定する
- `declarationFile` と `implementationFile` の２つが指定できる
  - `declarationFile` : `*.d.ts` の形式で生成される (デフォルト)
  - `implementationFile` : `*.ts` の形式で生成される

#### `excludeClasses`

- 型定義を生成したくないクラスを指定する
- `java.lang.Comparable` などのプロパティを持たないインターフェイスの出力を無効にできる

#### `declarePropertiesAsReadOnly`

- プロパティに `readonly` を付与する
- デフォルトでは `false`

#### `additionalDataLibraries`

- Java でよく利用されるライブラリのサポートをオプトインする
- `guava`, `joda`, `vavr` のサポートを追加できる

#### `mapDate`

- Java の Date に関する型をどのように変換するかを設定する
- 指定できる値は `asDate`, `asString`, `asNumber` で、デフォルトは `asDate`
- 日付データを JSON 化すると string などに変換することが多い

#### `optionalPropertiesDeclaration` と `requiredAnnotations`

- `optionalPropertiesDeclaration` は、`requiredAnnotations` で指定されたアノテーションが付与されていないすべてのプロパティを optional とする
- optional の方法としては、以下の５つ方法が選択できる
  - `questionMark`: `?` をプロパティに付与する
  - `questionMarkAndNullableType`: `?` をプロパティに付与し、null とのユニオン型をアノテーションする
  - `nullableType`: null とのユニオン型をアノテーションする
  - `nullableAndUndefinableType`: null と undefined とのユニオン型をアノテーションする
  - `undefinableType`: undefined とのユニオン型をアノテーションする
- Java が null でないことを考慮しつつ、導入するプロジェクトに合わせて柔軟に設定できるとよい

## 独自で拡張を作成する方法

typescript-generator は今まで紹介してきたとおり多様なパラメーターを設定できるのですが、拡張を利用することでさらに柔軟な型変換が可能になります。typescript-generator が提供している拡張は、[こちらのディレクトリに存在し](https://github.com/vojtechhabarta/typescript-generator/tree/main/typescript-generator-core/src/main/java/cz/habarta/typescript/generator/ext)、以下のように利用することが可能です。

```java
generateTypeScript {
    ...
    extensions = [
      'cz.habarta.typescript.generator.ext.AxiosClientExtension',
      ....
    ]
}
```

また、ディレクトリのコードを参考にすることで、プロジェクト特有の変換も自分で実装することが可能です。ここでは、デフォルト値が代入されているプロパティを non-nullable にする拡張の実装方法を簡単に説明します。実装は以下のリポジトリにも公開しているので、興味がある人はリポジトリのコードも参考にしてもらえればと思います。

https://github.com/nissy-dev/typescript-generator-sample-extension

### 拡張の雛形の作成

まず `cz.habarta.typescript.generator.Extension` を継承し、`getFeatures` メソッドと `getTransformers` メソッドをオーバーライドします。

`getFeatures` は、作成する拡張にパラメーターを渡したいときに利用します。今回は特にパラメーターを利用する予定がないので、`EmitterExtensionFeatures` のインスタンスをそのまま返します。`getTransformers` は、指定した TransformationPhase で各 Java Bean に対する TypeScript の型情報を変換します。

```java
public class DefaultValueNonNullableExtension extends Extension {

    @Override
    public EmitterExtensionFeatures getFeatures() {
        final EmitterExtensionFeatures features = new EmitterExtensionFeatures();
        return features;
    }

    @Override
    public List<TransformerDefinition> getTransformers() {
        return Arrays
                .asList(new TransformerDefinition(ModelCompiler.TransformationPhase.BeforeSymbolResolution,
                        this::transformModel));
    }

    protected TsModel transformModel(TsModelTransformer.Context context, TsModel model) {
        final List<TsBeanModel> beans = model.getBeans().stream()
                .map(bean -> transformBean(context, bean))
                .collect(Collectors.toList());
        return model.withBeans(beans);
    }

    protected TsBeanModel transformBean(TsModelTransformer.Context context, TsBeanModel tsBean) {
      // TODO：独自の型変換の実装のメイン
    }
}
```

TransformationPhase の各 Phase で行っている型変換を把握するには、以下の処理が参考になります。

https://github.com/vojtechhabarta/typescript-generator/blob/c19d407fea52612e7b7cd20620059736aec9a6a9/typescript-generator-core/src/main/java/cz/habarta/typescript/generator/compiler/ModelCompiler.java#L118-L195

今回の場合は、`optionalPropertiesDeclaration` の変換の後に、 デフォルト値が代入されているプロパティを non-nullable にするという処理を実装するので、`BeforeSymbolResolution` を指定しています。

### 拡張のメインロジックの記述

各プロパティに対する変換処理は、`transformBean` に実装していきます。各プロパティの型情報は `TsBeanModel` の `properties` が保持しており、Java クラスの情報についても `context` 変数から Reflection API を利用してアクセスできます。

以下のロジックでは、Reflection API を使ってプロパティの初期値を取得し、初期値が null でなければユニオン型から `null` を省くという処理を行っています。

```java
  protected TsBeanModel transformBean(TsModelTransformer.Context context, TsBeanModel tsBean) {
        try {
            final BeanModel bean = context.getBeanModelOrigin(tsBean);
            List<TsPropertyModel> properties = tsBean.getProperties().stream().map((TsPropertyModel tsProperty) -> {
                try {
                    final Class<?> originClass = bean.getOrigin();
                    final Object instance = originClass.getConstructor().newInstance();
                    final Field field = originClass.getDeclaredField(tsProperty.getName());
                    field.setAccessible(true);
                    // プロパティの初期値を取得し、nullではないか確認する
                    if (field.get(instance) != null && tsProperty.tsType instanceof TsType.UnionType) {
                        final TsType.UnionType unionType = (TsType.UnionType) tsProperty.tsType;
                        // null を省いたユニオン型に修正する
                        return tsProperty.withTsType(unionType.remove(Arrays.asList(TsType.Null)));
                    }

                    return tsProperty;
                } catch (Exception e) {
                    return tsProperty;
                }
            }).collect(Collectors.toList());
            return tsBean.withProperties(properties);
        } catch (Exception e) {
            TypeScriptGenerator.getLogger()
                    .verbose(String.format("DefaultValueNonNullableExtension raised error: ", e.getMessage()));
            return tsBean;
        }
    }
```

### テスト

最後に、拡張が正しく実装されているかテストを書きます。テストに関しても、typescript-generator のリポジトリに実装されているので、それらを参考にして実装します。

```java
class ExtensionTest {

    // 型変換のパラメーターを設定する
    public Settings settings() {
      final Settings settings = new Settings();
      settings.outputKind = TypeScriptOutputKind.module;
      settings.jsonLibrary = JsonLibrary.jackson2;
      settings.noFileComment = true;
      settings.noTslintDisable = true;
      settings.noEslintDisable = true;
      settings.newline = "\n";
      settings.optionalPropertiesDeclaration = OptionalPropertiesDeclaration.nullableType;
      return settings;
    }

    @Test
    public void test() {
        final Settings settings = settings();
        settings.extensions.add(new DefaultValueNonNullableExtension());
        final String output = new TypeScriptGenerator(settings).generateTypeScript(Input.from(SampleClass.class));
        assertTrue(output.contains("text0: string | null;"));
        assertTrue(output.contains("text1: string;"));
    }

    @Data
    public static class SampleClass {
        public String text0;
        public String text1 = "hello"
    }
}
```

## まとめ

この記事では、typescript-generator の使い方について、基本的なパラメーターの扱い方から独自で拡張を実装する方法まで幅広く紹介しました。バックエンドの Java のコードを読みながら手動で TypeScript の型定義を用意したり、バックエンドから受け取る値に TypeScript の any 型をアノテーションしている場合などには、typescript-generator を使ってみる価値があると思います。

また、typescript-generator の各パラメーターについてもう少し詳しく知りたい方は、公式ドキュメントの他にも以下の記事も非常に参考になります。

https://hepokon365.hatenablog.com/entry/2020/06/28/184615

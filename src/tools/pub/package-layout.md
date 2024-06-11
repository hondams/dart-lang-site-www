---
title: Package layout conventions
description: Learn more about the directory structure used by Dart's package management tool, pub.
---

[pub package](/guides/packages) をビルドする際には、このページで説明する規約に従うことを推奨します。
パッケージ内のファイルやディレクトリをどのように整理し、どのように名前をつけるかについて説明しています。
When you build a [pub package](/guides/packages),
we encourage you to follow the conventions that this page describes.
They describe how you organize the files and directories within your
package, and how to name things.

{{site.alert.flutter-note}}
  Flutterアプリはアセットにカスタムディレクトリを使用できます。
  詳しくは [Flutter website.]({{site.flutter-docs}}) の 
  [アセットとイメージの追加]({{site.flutter-docs}}/development/ui/assets-and-images) を参照してください。
  Flutter apps can use custom directories for their assets.
  For details, see
  [Adding assets and images]({{site.flutter-docs}}/development/ui/assets-and-images)
  on the [Flutter website.]({{site.flutter-docs}})
{{site.alert.end}}

このガイドラインを隅々まで使った完全なパッケージ（名前は`enchilada`）はこんな感じだ：
Here's what a complete package (named `enchilada`)
that uses every corner of these guidelines
might look like:

```nocode
enchilada/
  .dart_tool/ *
  pubspec.yaml
  pubspec.lock **
  LICENSE
  README.md
  CHANGELOG.md
  benchmark/
    make_lunch.dart
  bin/
    enchilada
  doc/
    api/ ***
    getting_started.md
  example/
    main.dart
  lib/
    enchilada.dart
    tortilla.dart
    guacamole.css
    src/
      beans.dart
      queso.dart
  test/
    enchilada_test.dart
    tortilla_test.dart
  tool/
    generate_docs.dart
  web/
    index.html
    main.dart
    style.css
```    

\* `dart pub get` を実行すると、`.dart_tool/` ディレクトリが存在する。
   ソース管理にはチェックしないでください。
   詳細については、[ツール用のプロジェクト別キャッシュ](#project-specific-caching-for-tools)
   The `.dart_tool/` directory exists after you've run `dart pub get`.
   Don't check it into source control.
   To learn more, see 
   [Project specific caching for tools](#project-specific-caching-for-tools).

\** `pubspec.lock` ファイルは、`dart pub get` を実行した後に存在する。
    あなたのパッケージが [application package](/tools/pub/glossary#application-package) でない限り、
    このファイルはソース管理から外しておいてください。
    The `pubspec.lock` file exists after you've run `dart pub get`.
    Leave it out of source control unless your package is an
    [application package](/tools/pub/glossary#application-package).

\*** `doc/api` ディレクトリは [`dart doc`](/tools/dart-doc) を実行した後にローカルに存在する。
     `api`ディレクトリをソース管理にチェックしてはいけない。
     The `doc/api` directory exists locally after you've run
     [`dart doc`](/tools/dart-doc).
     Don't check the `api` directory into source control.


## pubspec The pubspec

```nocode
enchilada/
  pubspec.yaml
  pubspec.lock
```

すべてのパッケージは、パッケージのルートディレクトリに `pubspec.yaml` という名前のファイルで
 [_pubspec_](/tools/pub/pubspec) を持つ。
それがパッケージに *なる* のです。
Every package has a [_pubspec_](/tools/pub/pubspec), a file named
`pubspec.yaml`, in the root directory of the package. That's what *makes* it a
package.

パッケージで、
[`dart pub get`](/tools/pub/cmd/pub-get),
[`dart pub upgrade`](/tools/pub/cmd/pub-upgrade), または
[`dart pub downgrade`](/tools/pub/cmd/pub-downgrade)
を実行すると、`pubspec.lock` という名前の **lockfile** を作成する。
あなたのパッケージが
[application package](/tools/pub/glossary#application-package)
である場合、ロックファイルをソース管理にチェックする。
さもなければ、そうしない。
Running [`dart pub get`](/tools/pub/cmd/pub-get),
[`dart pub upgrade`](/tools/pub/cmd/pub-upgrade), or
[`dart pub downgrade`](/tools/pub/cmd/pub-downgrade) on the package
creates a **lockfile**, named `pubspec.lock`. 
If your package is an 
[application package](/tools/pub/glossary#application-package), 
check the lockfile into source control. Otherwise, don't.

さらなる情報のために、[pubspec page](/tools/pub/pubspec) を参照する。
For more information, see the [pubspec page](/tools/pub/pubspec).

## ライセンス LICENSE

```nocode
enchilada/
  LICENSE
```

あなたのパッケージを公開するなら、`LICENSE` という名前のライセンスファイルを含む。
[BSD-3-Clause,](https://opensource.org/licenses/BSD-3-Clause) のような
[OSI-approved license](https://opensource.org/licenses) を使用することを推奨する。
そうすれば、他の人があなたの作品を再利用できる。
If you're publishing your package, include a license file named `LICENSE`.
We recommend using an [OSI-approved license](https://opensource.org/licenses)
such as [BSD-3-Clause,](https://opensource.org/licenses/BSD-3-Clause)
so that others can reuse your work.

## README.md

```nocode
enchilada/
  README.md
```

オープンソースでよく見られるファイルのひとつに、プロジェクトについて説明する _README_ ファイルがある。
これは pub では特に重要だ。
[pub.dev site,]({{site.pub}}) にアップロードすると、
`README.md`ファイルは、パッケージのページに、[Markdown][] としてレンダリングされて、表示されます。
これは、あなたのコードを人々に紹介するのに最適な場所です。
One file that's very common in open source is a _README_ file that
describes the project. This is especially important in pub. When you upload
to the [pub.dev site,]({{site.pub}}) your `README.md` file
is shown—rendered as [Markdown][]—on the page for your package. 
This is the perfect place to introduce people to your code.

すばらしい README の書き方のガイダンスのために、
[Writing package pages](/guides/libraries/writing-package-pages) を参照する。
For guidance on how to write a great README, see
[Writing package pages](/guides/libraries/writing-package-pages).

## 変更履歴 CHANGELOG.md

```nocode
enchilada/
  CHANGELOG.md
```

ユーザがパッケージをアップグレードする際に役立つ注釈を記載した、
あなたのパッケージのリリースごとのセクションがある `CHANGELOG.md` ファイルを含む。
あなたのパッケージのユーザは、バグフィックスや新機能を発見するため、
あるいは、あなたのパッケージの最新バージョンにアップグレードするのに 
どの程度の労力がかかるかを判断するために、よく変更履歴を見ます。
Include a `CHANGELOG.md` file that has a section for
each release of your package,
with notes to help users of your package upgrade.
Users of your package often review the changelog
to discover bug fixes and new features,
or to determine how much effort it will take to upgrade
to the latest version of your package.

`CHANGELOG.md` を解析するツールに対応するには、以下のフォーマットを使用する：
To support tools that parse `CHANGELOG.md`,
use the following format:

* 各バージョンには、見出しのついたセクションがあります。
  Each version has its own section with a heading.
* バージョン見出しはすべてレベル1か、すべてレベル2である。
  The version headings are either all level 1 or all level 2.
* バージョン見出しのテキストにはパッケージのバージョン番号が含まれ、 オプションで先頭に "v "が付きます。
  The version heading text contains a package version number,
  optionally prefixed with "v".

パッケージを [pub.dev site,]({{site.pub}}) にアップロードすると、
パッケージの `CHANGELOG.md` ファイル (もしあれば) が **Changelog** タブに表示されます。 
[Markdown.][Markdown]として表示されます。
When you upload your package to the [pub.dev site,]({{site.pub}})
your package's `CHANGELOG.md` file (if any)
appears in the **Changelog** tab, rendered as [Markdown.][Markdown]

以下に`CHANGELOG.md`ファイルの例を示す。
この例が示すように、サブセクションを追加することができます。
Here's an example of a `CHANGELOG.md` file.
As the example shows, you can add subsections.

```markdown
# 1.0.1

* Fixed missing exclamation mark in `sayHi()` method.

# 1.0.0

* **Breaking change:** Removed deprecated `sayHello()` method.
* Initial stable release.

## Upgrading from 0.1.x

Change all calls to `sayHello()` to instead be to `sayHi()`.

# 0.1.1

* Deprecated the `sayHello()` method; use `sayHi()` instead.

# 0.1.0

* Initial development release.
```


## 公開ディレクトリ Public directories

パッケージ内の2つのディレクトリは他のパッケージに公開される： `lib` と `bin` である。
`lib` に [public libraries](#public-libraries)
`bin` に [public tools](#public-tools)  を配置する。
Two directories in your package are public to other packages: `lib` and
`bin`. You place [public libraries](#public-libraries) in `lib` and
[public tools](#public-tools) in `bin`.

### 公開ライブラリ Public libraries {#public-libraries}

以下のディレクトリ構造は、enchiladaの `lib` 部分を示している：
The following directory structure shows the `lib` portion of enchilada:

```nocode
enchilada/
  lib/
    enchilada.dart
    tortilla.dart
```

多くの [packages](/tools/pub/glossary#package) は、
他のパッケージがインポートして使用できる Dart ライブラリを定義しています。
これらの公開 Dart ライブラリファイルは `lib` というディレクトリの中にあります。
Many [packages](/tools/pub/glossary#package)
define Dart libraries that other packages can import and use.
These public Dart library files go inside a directory called `lib`.

ほとんどのパッケージは、ユーザーがインポートできる単一のライブラリを定義しています。
その場合、ライブラリの名前は通常パッケージの名前と同じにします。
例えば、この例では `enchilada.dart` とします。
しかし、パッケージに適した名前で他のライブラリを定義することもできます。
Most packages define a single library that users can import. In that case,
its name should usually be the same as the name of the package, like
`enchilada.dart` in the example here. But you can also define other
libraries with whatever names make sense for your package.

そうすれば、パッケージ名とライブラリファイル名を使って、
ユーザーは 以下のようにライブラリをインポートできる：
When you do, users can import these libraries using the name of the
package and the library file, like so:

```dart
import 'package:enchilada/enchilada.dart';
import 'package:enchilada/tortilla.dart';
```

公開ライブラリを整理したい場合は、`lib`の中にサブディレクトリを作ることもできる。
そうすれば、ユーザーがインポートするときにそのパスを指定することになる。
例えば、以下のようなファイル階層があるとします：
If you want to organize your public libraries, you can also create
subdirectories inside `lib`. If you do that, users will specify that path
when they import it. Say you have the following file hierarchy:

```nocode
enchilada/
  lib/
    some/
      path/
        olives.dart
```

ユーザは `olives.dart` を次のようにインポートします：
Users import `olives.dart` as follows:

```dart
import 'package:enchilada/some/path/olives.dart';
```

*ライブラリ* だけが `lib` の中にあるべきであることに注意してください。
*エントリポイント* （`main()` 関数を持つ Dart スクリプト） は `lib` には置けない。
Dart スクリプトを `lib` 内に置くと、
そのスクリプトに含まれる `package:` インポートが解決されないことがわかります。
代わりに、エントリポイントは適切なエントリポイントディレクトリに
置く必要があります。
Note that only *libraries* should be in `lib`.
*Entrypoints*—Dart scripts with a `main()` function—cannot
go in `lib`. If you place a Dart script inside `lib`,
you will discover that any `package:` imports it contains don't
resolve. Instead, your entrypoints should go in the appropriate
[entrypoint directory](/tools/pub/glossary#entrypoint-directory).

{{site.alert.info}}
  **Webアプリのための Tips**
  **Tip for web apps:**
  Webアプリを開発する際に最高のパフォーマンスのためには、
  [実装ファイル](#implementation-files) を `/lib` 配下に代わって、
  `/lib/src` 配下に置くこと。
  また、<code>package:<em>package_name</em>/src/...</code> の
  インポートを避ける。
  For the best performance when developing web apps,
  put [implementation files](#implementation-files) under `/lib/src`,
  instead of elsewhere under `/lib`.
  Also, avoid imports of <code>package:<em>package_name</em>/src/...</code>.
{{site.alert.end}}

パッケージのさらなる情報のために、
[Creating packages](/guides/libraries/create-packages) を参照。
For more information on packages, see
[Creating packages](/guides/libraries/create-packages).

### 公開ツール Public tools {#public-tools}

`bin` ディレクトリに置かれた Dart スクリプトは公開される。
あるパッケージのディレクトリ内にいる場合、
[`dart run`](/tools/dart-run) を使用することで、
そのパッケージが依存している他のパッケージの `bin` ディレクトリから
スクリプトを実行することができます。
_どの_ ディレクトリからでも、[`dart pub global activate`][activate] を
使って有効化したパッケージの [スクリプトを実行する][] ことができます。
Dart scripts placed inside of the `bin` directory are public. If you're
inside the directory of a package, you can use
[`dart run`](/tools/dart-run) to run scripts from the `bin`
directories of any other package the package depends on. From _any_
directory, you can [run scripts][]
from packages that you have activated using
[`dart pub global activate`][activate].

[run scripts]: /tools/pub/cmd/pub-global#running-a-script
[activate]: /tools/pub/cmd/pub-global#activating-a-package

パッケージが依存することを想定していて、
スクリプトをパッケージのプライベートなものにしたい場合は、
スクリプトをトップレベルの `tool` ディレクトリに置いてください。
パッケージに依存するつもりがない場合は、
スクリプトを `bin` に残しておくことができます。
If you intend for your package to be depended on,
and you want your scripts to be private to your package, place them
in the top-level `tool` directory.
If you don't intend for your package to be depended on, you can leave your
scripts in `bin`.


## 公開アセット Public assets

```nocode
enchilada/
  lib/
    guacamole.css
```
ほとんどのパッケージはDartコードを再利用するために存在しますが、
他の種類のコンテンツを再利用することもできます。
例えば、[Bootstrap](https://getbootstrap.com/) 用のパッケージには、
パッケージの利用者が使用するための多くの CSS ファイルが
含まれているかもしれません。
While most packages exist to let you reuse Dart code, you can also
reuse other kinds of content. For example, a package for
[Bootstrap](https://getbootstrap.com/) might include a number of CSS files
for consumers of the package to use.

これらはトップレベルの `lib` ディレクトリに置く。
そこにどんな種類のファイルを入れてもいいし、
好きなようにサブディレクトリで整理してもいい。
These go in the top-level `lib` directory. You can put any kind of file
in there and organize it with subdirectories however you like.


## 実装ファイル Implementation files

```nocode
enchilada/
  lib/
    src/
      beans.dart
      queso.dart
```

`lib` 内のライブラリは一般に公開されており、
他のパッケージが自由にインポートすることができる。
しかし、パッケージのコードの多くは内部実装ライブラリであり、
パッケージ自身によってのみインポートされ、使用されるべきものです。
それらは `lib` のサブディレクトリ `src` の中にあります。
その中にサブディレクトリを作ることもできます。
The libraries inside `lib` are publicly visible: other packages are free to
import them. But much of a package's code is internal implementation libraries
that should only be imported and used by the package itself. Those go inside a
subdirectory of `lib` called `src`. You can create subdirectories in there if
it helps you organize things.

`lib/src` にあるライブラリを、
*同じ* パッケージ 内の他の Dart コードからインポートするのは自由ですが、
(同じパッケージとは、`lib` にある他のライブラリや `bin` にあるスクリプト、テストなど)
他のパッケージの `lib/src` ディレクトリからインポートしてはいけません。
これらのファイルはそのパッケージの公開 API の一部ではないので、
あなたのコードを壊してしまうような方法での変更を加える可能性があります。
You are free to import libraries that live in `lib/src` from within other Dart
code in the *same* package (like other libraries in `lib`, scripts in `bin`, and
tests) but you should never import from another package's `lib/src` directory.
Those files are not part of the package's public API, and they might change in
ways that could break your code.

ライブラリを自分のパッケージ内からどのようにインポートするかは、
ライブラリの場所に依存する：
How you import libraries from within your own package
depends on the locations of the libraries:

 * [reaching inside or outside `lib/`][] のとき
   (lint: [_avoid_relative_lib_imports_][])
  `package:` を利用する。
  When [reaching inside or outside `lib/`][]
   (lint: [_avoid_relative_lib_imports_][]),
   use `package:`.
 * さもなければ、[prefer relative imports][]
   Otherwise, [prefer relative imports][].
 
 [reaching inside or outside `lib/`]: /effective-dart/usage#dont-allow-an-import-path-to-reach-into-or-out-of-lib
 [_avoid_relative_lib_imports_]: /tools/linter-rules/avoid_relative_lib_imports
 [prefer relative imports]: /effective-dart/usage#prefer-relative-import-paths

例えば、
For example:

```dart
// When importing from lib/beans.dart
import 'src/beans.dart';

// When importing from test/beans_test.dart
import 'package:enchilada/src/beans.dart';
```

ここ （この場合は `enchilada`） で使用する名前は、
[pubspec](/tools/pub/pubspec) でパッケージに指定する名前である。
The name you use here (in this case `enchilada`) is the name you specify for
your package in its [pubspec](/tools/pub/pubspec).

## Web ファイル Web files

```nocode
enchilada/
  web/
    index.html
    main.dart
    style.css
```

ウェブパッケージでは、エントリポイントのコード
（Dart スクリプトを含む `main()` や CSS や HTML などのサポートファイル）
を `web` の下に置きます。
`web` ディレクトリを、好きなようにサブディレクトリに整理できる。
For web packages, place entrypoint code—Dart scripts that include
`main()` and supporting files, such as CSS or HTML—under `web`.
You can organize the `web` directory into subdirectories if you like.

[library code](#public-libraries) を `lib` の下に置く。
ライブラリが `web` 配下のコードや他のパッケージから直接インポートされていない場合は、 
`lib/src` 配下に置く。
[web-based examples](#examples) は `example` の下に置く。
画像などのアセットの置くTipsについては [Public assets](#public-assets) を参照のこと。
Put [library code](#public-libraries) under `lib`.
If the library isn't imported directly by code under `web`, or by
another package, put it under `lib/src`.
Put [web-based examples](#examples) under `example`. See
[Public assets](#public-assets) for tips on where to put assets,
such as images.

## コマンドライン アプリケーション Command-line apps

```nocode
enchilada/
  bin/
    enchilada
```

パッケージの中には、コマンドラインから直接的に実行できるプログラムを定義しているものもある。
これらはシェルスクリプトであったり、Dartを含む他のスクリプト言語であったりします。
Some packages define programs that can be run directly from the command
line. These can be shell scripts or any other scripting language,
including Dart.

パッケージがこのようなコードを定義している場合、
それを `bin` という名前のディレクトリに置いてください。
[`dart pub global`](/tools/pub/cmd/pub-global#running-a-script-from-your-path) を使って設定すれば、
コマンドラインのどこからでもこのスクリプトを実行できる。
If your package defines code like this, put it in a directory named `bin`.
You can run that script from anywhere on the command line, if you set it up
using
[`dart pub global`](/tools/pub/cmd/pub-global#running-a-script-from-your-path).

## テストとベンチマーク Tests and benchmarks

```nocode
enchilada/
  test/
    enchilada_test.dart
    tortilla_test.dart
```

すべてのパッケージにはテストが必要です。
pub では、これらのテストは `test` ディレクトリ
 (あるいはその中のディレクトリでもかまいません) に置き、
ファイル名の最後に `_test` をつけるのが慣例となっています。
Every package should have tests. With pub, the convention is
that these go in a `test` directory (or some directory inside it if you like)
and have `_test` at the end of their file names.

典型的に、[test]({{site.pub-pkg}}/test) パッケージを利用する。
Typically, these use the [test]({{site.pub-pkg}}/test)
package.

```nocode
enchilada/
  benchmark/
    make_lunch.dart
```

パフォーマンスが重要なコードを含むパッケージには、
*ベンチマーク* も含まれているかもしれない。
これらはAPIの正しいさでなく、速度
（またはメモリ使用量、あるいはその他の経験的メトリクス）をテストする。
Packages that have performance critical code may also include *benchmarks*.
These test the API not for correctness but for speed (or memory use, or maybe
other empirical metrics).

## ドキュメンテーション Documentation

```nocode
enchilada/
  doc/
    api/
    getting_started.md
```

If you have code and tests, the next piece you might want
is good documentation. That goes inside a directory named `doc`.

When you run the [`dart doc`](/tools/dart-doc)
tool, it places the API documentation, by default, under `doc/api`.
Since the API documentation is generated from the source code,
you should not place it under source control.

Other than the generated `api`, we don't
have any guidelines about format or organization of the documentation
that you author.  Use whatever markup format that you prefer.

## 実例 Examples

```nocode
enchilada/
  example/
    main.dart
```

コード、テスト、ドキュメント、それ以外にユーザーが望むものはあるでしょうか？
もちろん、あなたのパッケージを使うスタンドアロンのサンプルプログラムです！
これらは `example` ディレクトリの中にあります。
もしサンプルが複雑で複数のファイルを使用するのであれば、
サンプルごとにディレクトリを作ることを検討してください。
そうでなければ、それぞれを `example` ディレクトリの中に置くことができます。
Code, tests, docs, what else
could your users want? Standalone example programs that use your package, of
course! Those go inside the `example` directory. If the examples are complex
and use multiple files, consider making a directory for each example. Otherwise,
you can place each one right inside `example`.

サンプルでは、自分のパッケージからファイルをインポートするために、
`package:`を使ってください。
そうすることで、あなたのパッケージ内のサンプルコードが、
あなたのパッケージ外のコードと全く同じように見えるようになります。
In your examples, use `package:` to import files from your own package.
That ensures that the example code in your package looks exactly
like code outside of your package would look.

パッケージを公開する可能性がある場合は、
以下のいずれかの名前でサンプルファイルを作成することを検討してください：
If you might publish your package,
consider creating an example file with one of the following names:

* <code>example/example[.md]</code>
* <code>example[/lib]/main.dart</code>
* <code>example[/lib]/<em>package_name</em>.dart</code>
* <code>example[/lib]/<em>package_name</em>_example.dart</code>
* <code>example[/lib]/example.dart</code>
* <code>example/README[.md]</code>

上記のファイルを1つ以上を含むパッケージを公開すると、
pub.dev サイトは最初に見つけたファイル（上記のリストに示された順序で検索します）を表示するために、 
**Example**  タブを作成します。
例えば、パッケージの `example` ディレクトリの下に `README.md` というファイルを含み、多くのファイルがある場合、
パッケージの Example タブは `example/README.md` の内容を表示します（[Markdown.][Markdown] としてパースされます）。
When you publish a package that contains one or more of the above files,
the pub.dev site creates an **Example** tab to display the first file it finds
(searching in the order shown in the list above).
For example, if your package has many files under its `example` directory,
including a file named `README.md`,
then your package's Example tab displays the contents of `example/README.md`
(parsed as [Markdown.)][Markdown]

{% comment %}
exampleファイルがどのように選ばれるかは、
dart-langのリポジトリでexampleFileCandidatesを検索してください：
To see how the example file is chosen,
search the dart-lang repos for exampleFileCandidates:
https://github.com/search?q=org%3Adart-lang+exampleFileCandidates&type=Code
{% endcomment %}

## 内部的なツールとスクリプト Internal tools and scripts

```nocode
enchilada/
  tool/
    generate_docs.dart
```

成熟したパッケージには、パッケージ自体の開発中に実行される小さなヘルパースクリプトやプログラムがよくあります。
テストランナーやドキュメンテーションジェネレータ、その他の自動化の断片のようなものを考えてみてください。
Mature packages often have little helper scripts and programs that people
run while developing the package itself. Think things like test runners,
documentation generators, or other bits of automation.

`bin` 内のスクリプトとは異なり、これらはパッケージの外部ユーザー用では *ありません*。
これらのスクリプトがある場合は、 `tool` というディレクトリに置いてください。
Unlike the scripts in `bin`, these are *not* for external users of the package.
If you have any of these, place them in a directory called `tool`.

## Project-specific caching for tools

{{site.alert.info}}
  Do not check the `.dart_tool/` directory into source control.
  Instead, keep `.dart_tool/` in `.gitignore`.
{{site.alert.end}}

The `.dart_tool/` directory is created when you run `dart pub get`
and might be deleted at any time. Various tools use this directory
for caching files specific to your project and/or local machine.
The `.dart_tool/` directory should never be checked into
source control, or copied between machines.

It is also generally safe to delete the `.dart_tool/` directory,
though some tools might need recompute the cached information. 

**Example:** The [`dart pub get`](/tools/pub/cmd/pub-get) tool
will download and extract dependencies to a global `$PUB_CACHE` directory,
and then write a `.dart_tool/package_config.json` file mapping _package names_
to directories in the global `$PUB_CACHE` directory.
The `.dart_tool/package_config.json` file is used by other tools,
such as the analyzer and compilers when they need to resolve statements
such as `import 'package:foo/foo.dart'`.

When developing a tool that needs project-specific caching, 
you might consider using the `.dart_tool/` directory 
because most users already ignore it with `.gitignore`.
When caching files for your tool in a user's `.dart_tool/` directory,
you should use a unique subdirectory. To avoid collisions,
subdirectories of the form `.dart_tool/<tool_package_name>/`
are reserved for the package named `<tool_package_name>`.
If your tool isn't distributed through the [pub.dev site,]({{site.pub}})
you might consider publishing a placeholder package in order to 
reserve the unique name.

**Example:** [`package:build`](https://pub.dev/packages/build) provides a
framework for writing code generation steps.
When running these build steps, files are cached in `.dart_tool/build/`.
This helps speed-up future re-runs of the build steps.

{{site.alert.warning}}
  When developing a tool that wants to cache files in `.dart_tool/`,
  ensure the following:
  
  * You are using a subdirectory named after a package you own
    (`.dart_tool/<my_tool_package_name>/`)
  * Your files don't belong under source control, 
    as `.dart_tool/` is generally listed in `.gitignore`
{{site.alert.end}}


[Markdown]: {{site.pub-pkg}}/markdown

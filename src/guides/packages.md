---
title: How to use packages
short-title: Packages
description: Learn more about pub, Dart's tool for managing packages.
---

Dartエコシステムでは、ライブラリやツールのような共有ソフトウェアを管理するために
_packages_ を使用します。
Dartパッケージを入手するには、**pub package manager** を使用します。
[**pub.dev site**,]({{site.pub}}) で一般公開されているパッケージを見つけることもできるし、
Gitリポジトリのような、ローカルのファイルシステムやそのほかの場所などからパッケージを読み込むこともできる。
パッケージの出所がどこであろうと、pubはバージョンの依存関係を管理し、
互いのパッケージと、あなたのSDKバージョンと動作するパッケージバージョンを入手するのを手助けします。
The Dart ecosystem uses _packages_ to manage shared software
such as libraries and tools.
To get Dart packages, you use the **pub package manager**.
You can find publicly available packages on the
[**pub.dev site**,]({{site.pub}})
or you can load packages from the local file system or elsewhere,
such as Git repositories.
Wherever your packages come from, pub manages version dependencies,
helping you get package versions that work with each other and
with your SDK version.

ほとんどの [Dart-savvy IDEs][] は、パッケージの作成、ダウンロード、更新、公開を含む 
pub の使用をサポートしています。
あるいは、[`dart pub` on the command line](/tools/pub/cmd) を利用します。
Most [Dart-savvy IDEs][] offer support for using pub that
includes creating, downloading, updating, and publishing packages.
Or you can use [`dart pub` on the command line](/tools/pub/cmd).

最低限、Dart パッケージは [pubspec file](/tools/pub/pubspec) を含むディレクトリです。
pubspec にはパッケージに関するメタデータが含まれます。
さらに、パッケージには依存関係（pubspec にリストされている）、
Dart ライブラリ、アプリ、リソース、テスト、イメージ、サンプルを含めることができます。
At a minimum,
a Dart package is a directory containing a [pubspec file](/tools/pub/pubspec).
The pubspec contains some metadata about the package. 
Additionally, a package can contain dependencies (listed in the pubspec),
Dart libraries, apps, resources, tests, images, and examples.

パッケージを利用するために、次を実行する。
To use a package, do the following:

* pubspec （パッケージの依存関係をリストアップし、バージョン番号のようなほかのメタデータを含める `pubspec.yaml` というファイル） を作成する。
  Create a pubspec (a file named `pubspec.yaml` that
  lists package dependencies and includes
  other metadata, such as a version number).
* パッケージの依存関係を取得するにはコマンドを使用します。
  Use [`dart pub get`][get] to retrieve your package's dependencies.
* Dart コードがパッケージ内のライブラリに依存している場合、そのライブラリをインポートします。
  If your Dart code depends on a library in the package, import the library.

## pubspec を作成する。 Creating a pubspec

pubspec は、アプリケーションのトップディレクトリにある `pubspec.yaml` という名前のファイルです。
最も単純で可能な pubspec はパッケージ名のみを列挙します：
The pubspec is a file named `pubspec.yaml`
that's in the top directory of your application.
The simplest possible pubspec lists only the package name:

```yaml
name: my_app
```

Here is an example of a pubspec that declares dependencies on
two packages (`js` and `intl`) that are hosted on the pub.dev site:

```yaml
name: my_app

dependencies:
  js: ^0.6.0
  intl: ^0.17.0
```

手動で編集せずに `pubspec.yaml` ファイルを更新するには、`dart pub add` コマンドを実行する。
以下の例では、`vector_math` への依存関係を追加している。
To update the `pubspec.yaml` file, without manual editing, 
you can run `dart pub add` command.
The following example adds a dependency on `vector_math`.

```terminal
$ dart pub add vector_math
Resolving dependencies... 
+ vector_math 2.1.3
Downloading vector_math 2.1.3...
Changed 1 dependency!
```

pubspecの作成に関する詳細は、[pubspec documentation](/tools/pub/pubspec) や 
使用するパッケージのドキュメントを参照してください。
For details on creating a pubspec,
see the [pubspec documentation](/tools/pub/pubspec)
and the documentation for the packages that you want to use.

## パッケージを取得する。 Getting packages

一度、pubspecを取得したら、アプリケーションのトップ・ディレクトリから [`dart pub get`][get] を実行できる：
Once you have a pubspec, you can run [`dart pub get`][get] from the top 
directory of your application:

```terminal
$ cd <path-to-my_app>
$ dart pub get
```

この処理は、_依存関係の取得_ と呼ばれる。
This process is called _getting the dependencies_.

この `dart pub get`  コマンドは、あなたのアプリがどのパッケージに依存しているかを判断し、
それらを主要な [system cache](/tools/pub/glossary#system-cache) に置く。
公開されているパッケージにアプリが依存している場合、
そのパッケージを [pub.dev site.]({{site.pub}}) から pub がダウンロードします。
[Git dependency](/tools/pub/dependencies#git-packages) の場合は、pub が Git リポジトリをクローンします。
推移的依存関係も含まれます。
例えば、 `js` パッケージが `test` パッケージに依存している場合、 
`pub` は `js` パッケージと `test` パッケージの両方を取得します。
The `dart pub get` command
determines which packages your app depends on,
and puts them in a central [system cache](/tools/pub/glossary#system-cache).
If your app depends on a published package, pub downloads that package from the
[pub.dev site.]({{site.pub}})
For a [Git dependency](/tools/pub/dependencies#git-packages),
pub clones the Git repository.
Transitive dependencies are included, too.
For example, if the `js` package depends on the `test` package, `pub`
grabs both the `js` package and the `test` package.

Pubは、`package_config.json` ファイル (`.dart_tool/` ディレクトリの下に) を作成します。
`package_config.json` ファイルは、システムキャッシュ内の対応するパッケージにアプリが依存する、
各パッケージ名をマップします。
Pub creates a
`package_config.json` file (under the `.dart_tool/` directory)
that maps each package name that your app depends on
to the corresponding package in the system cache.


## パッケージからライブラリをインポートする。 Importing libraries from packages

パッケージに含まれているライブラリをインポートするには、
接頭辞に `package:` を付けてください：
To import libraries found in packages, 
use the `package:` prefix:

```dart
import 'package:js/js.dart' as js;
import 'package:intl/intl.dart';
```

Dartのランタイムは、`package:`以降をすべて受け取り、
アプリの `package_config.json` ファイル内でそれを検索する。
The Dart runtime takes everything after `package:`
and looks it up within the `package_config.json` file for
your app.

自分自身のパッケージの中からライブラリをインポートするために、このスタイルを使うこともできます。
`transmogrify`パッケージが以下のようにレイアウトされているとしよう：
You can also use this style to import libraries from within your own package.
Let's say that the `transmogrify` package is laid out as follows:

```nocode
transmogrify/
  lib/
    transmogrify.dart
    parser.dart
  test/
    parser/
      parser_test.dart
```

`parser_test.dart` ファイルは、次のように`parser.dart`をインポートできる：
The `parser_test.dart` file can import `parser.dart` like this:

```dart
import 'package:transmogrify/parser.dart';
```


## 依存関係をアップグレードする。 Upgrading a dependency

あなたのパッケージに新しい依存関係が初めてできたとき、
pub はあなたの他の依存関係と 互換性のある最新バージョンをダウンロードします。
そして、**lockfile** を作成することで、そのバージョンを *常に* 使用するために、
あなたのパッケージをロックします。
これは `pubspec.lock` という名前のファイルで、
pub が作成し、あなたの pubspec の隣に保存します。
このファイルには、あなたのパッケージが使用する、
それぞれの依存関係(即時的および推移的)の特定のバージョンがリストアップされています。
The first time you get a new dependency for your package,
pub downloads the latest version of it that's compatible with
your other dependencies.
It then locks your package to *always* use that version by
creating a **lockfile**.
This is a file named `pubspec.lock` that pub creates
and stores next to your pubspec. 
It lists the specific versions of each dependency (immediate and transitive) 
that your package uses.

あなたのパッケージが [アプリケーション パッケージ](/tools/pub/glossary#application-package) である場合、
このファイルを [ソースコードバージョン管理システム](/guides/libraries/private-files) にチェックする必要がある。
そうすることで、あなたのアプリで作業する全員が、すべての依存関係の同じバージョンを使うことになる。
lockfileをチェックインすることで、デプロイされたアプリがコードの同じバージョンを使うことも保証する。
If your package is an [application package](/tools/pub/glossary#application-package)
you should check this file into
[source control](/guides/libraries/private-files).
That way, everyone working on your app uses the same versions
of all of its dependencies.
Checking in the lockfile also ensures that your deployed app
uses the same versions of code.

依存関係を最新バージョンにアップグレードする準備ができたら、
[`dart pub upgrade`][upgrade] コマンドを使う：
When you're ready to upgrade your dependencies to the latest versions,
use the [`dart pub upgrade`][upgrade] command:

```terminal
$ dart pub upgrade
```

`dart pub upgrade` コマンドは、あなたのパッケージの依存関係の最新で利用可能なバージョンを使用して、
ロックファイルを再生成するように pub に指示します。
依存関係を 1 つだけアップグレードしたい場合は、アップグレードする パッケージを指定できます：
The `dart pub upgrade` command tells pub to regenerate the lockfile,
using the newest available versions of your package's dependencies.
If you want to upgrade only one dependency,
you can specify the package to upgrade:

```terminal
$ dart pub upgrade transmogrify
```

このコマンドは `transmogrify` を最新バージョンにアップグレードするが、
それ以外は同じままである。
That command upgrades `transmogrify` to the latest version
but leaves everything else the same.

pubspec のバージョン制約が競合するため、`dart pub upgrade` コマンドは、 
すべてのパッケージを最新バージョンに、いつでもアップグレードできるとは 限りません。
pubspec の編集が必要な古いパッケージを特定するには、[`dart pub outdated`][outdated] を使用します。
The `dart pub upgrade` command can't always upgrade every package
to its latest version,
due to conflicting version constraints in the pubspec.
To identify out-of-date packages that require editing the pubspec,
use [`dart pub outdated`][outdated].

## さらなる情報 More information

パッケージと pub・パッケージ・マネージャについてのさらなる情報は、
以下のページを参照してください。
The following pages have more information about packages and
the pub package manager.


### How to

* [Creating packages](/guides/libraries/create-packages)
* [Publishing packages](/tools/pub/publishing)

### Reference

* [Pub dependencies](/tools/pub/dependencies)
* [Pub environment variables](/tools/pub/environment-variables)
* [Pub glossary](/tools/pub/glossary)
* [Pub package layout conventions](/tools/pub/package-layout)
* [Pub versioning philosophy](/tools/pub/versioning)
* [Pubspec format](/tools/pub/pubspec)

### Pub サブコマンド Pub subcommands

`dart pub`ツールは以下のサブコマンドを提供する：
The `dart pub` tool provides the following subcommands:

{% include pub-subcommands.md %}

すべての `dart pub` サブコマンドの概要については、
[pub ツールのドキュメント](/tools/pub/cmd)を参照のこと。
For an overview of all the `dart pub` subcommands,
see the [pub tool documentation](/tools/pub/cmd).

### トラブルシューティング Troubleshooting

[pubのトラブルシューティング](/tools/pub/troubleshoot) は、
pubを使用する際に遭遇するかもしれない問題の解決策を提供します。
[Troubleshooting pub](/tools/pub/troubleshoot) gives solutions to problems that
you might encounter when using pub.

[Dart-savvy IDEs]: /tools#ides-and-editors
[get]: /tools/pub/cmd/pub-get
[upgrade]: /tools/pub/cmd/pub-upgrade
[outdated]: /tools/pub/cmd/pub-outdated

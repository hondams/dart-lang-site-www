---
title: "Effective Dart: Style"
description: Formatting and naming rules for consistent, readable code.
nextpage:
  url: /effective-dart/documentation
  title: Documentation
prevpage:
  url: /effective-dart
  title: Overview
---
<?code-excerpt plaster="none"?>
<?code-excerpt path-base="misc/lib/effective_dart"?>

優れたコードで意外に重要なのは、優れたスタイルだ。
一貫した命名、順序、書式は、同じコードが同じように見えるのを助ける。
これは、ほんとんどの私たちが視覚システムとして持っている、強力なパターンマッチングのハードウェアをうまく活用する。
Dartのエコシステム全体で一貫したスタイルを使用すれば、私たち全員が互いのコードから学び、貢献することが容易になります。
A surprisingly important part of good code is good style. Consistent naming,
ordering, and formatting helps code that *is* the same *look* the same. It takes
advantage of the powerful pattern-matching hardware most of us have in our
ocular systems.  If we use a consistent style across the entire Dart ecosystem,
it makes it easier for all of us to learn from and contribute to each others'
code.

## 識別子 Identifiers

Dartには3種類の識別子がある。
Identifiers come in three flavors in Dart.

*   `UpperCamelCase` の名前は、最初も含み、各単語の最初の文字を大文字にする。
    `UpperCamelCase` names capitalize the first letter of each word, including
    the first.

*   `lowerCamelCase` の名前は、頭字語（単語の先頭文字を連結した略語）であっても、常に小文字にする最初の単語を *除き* 、各単語の最初の文字を大文字にする。
    `lowerCamelCase` names capitalize the first letter of each word, *except*
    the first which is always lowercase, even if it's an acronym.

*   `lowercase_with_underscores`の名前は、頭字語（単語の先頭文字を連結した略語）であっても小文字のみを使用し、単語は`_`で区切る。
    `lowercase_with_underscores` names use only lowercase letters,
    even for acronyms,
    and separate words with `_`.


### DO 型は`UpperCamelCase`を使用して名前を付ける DO name types using `UpperCamelCase`

{% include linter-rule-mention.md rule="camel_case_types" %}

クラス、enum型、typedef、型パラメータは、各単語の最初の文字を大文字にし（最初の単語を含む）、区切り文字を使用しない。
Classes, enum types, typedefs, and type parameters should capitalize the first
letter of each word (including the first word), and use no separators.

{:.good}
<?code-excerpt "style_good.dart (type-names)"?>
```dart
class SliderMenu { ... }

class HttpRequest { ... }

typedef Predicate<T> = bool Function(T value);
```

これには、メタデータ アノテーションで使われることを意図したクラスも含まれる。
This even includes classes intended to be used in metadata annotations.

{:.good}
<?code-excerpt "style_good.dart (annotation-type-names)"?>
```dart
class Foo {
  const Foo([Object? arg]);
}

@Foo(anArg)
class A { ... }

@Foo()
class B { ... }
```

アノテーション・クラスのコンストラクタがパラメータを取らない場合、そのコンストラクタのために、別の`lowerCamelCase`定数を作成することをお勧めします。
If the annotation class's constructor takes no parameters, you might want to
create a separate `lowerCamelCase` constant for it.

{:.good}
<?code-excerpt "style_good.dart (annotation-const)"?>
```dart
const foo = Foo();

@foo
class C { ... }
```

### DO extension は、`UpperCamelCase`を使用して名前を付ける DO name extensions using `UpperCamelCase`

{% include linter-rule-mention.md rule="camel_case_extensions" %}

型のように、[extensions][]は、各単語の最初の文字を大文字にし（最初の単語を含む）、区切り文字を使用しない。
Like types, [extensions][] should capitalize the first letter of each word
(including the first word),
and use no separators.

{:.good}
<?code-excerpt "style_good.dart (extension-names)"?>
```dart
extension MyFancyList<T> on List<T> { ... }

extension SmartIterable<T> on Iterable<T> { ... }
```

[extensions]: /language/extension-methods

<a id="do-name-libraries-and-source-files-using-lowercase_with_underscores"></a>

### DO パッケージ、ディレクトリ、ソースファイルは、`lowercase_with_underscores` を使用して名前を付ける DO name packages, directories, and source files using `lowercase_with_underscores` {#do-name-packages-and-file-system-entities-using-lowercase-with-underscores}

{% include linter-rule-mention.md rule1="file_names" rule2="package_names" %}
<!-- source for rules (update these if you update the guideline):
https://github.com/dart-lang/linter/blob/master/lib/src/rules/file_names.dart -->

ファイルシステムによっては大文字と小文字を区別しないものもあるので、多くのプロジェクトではファイル名をすべて小文字にする必要がある。
区切り文字を使用することで、その形式でも名前が読めるようになります。
区切り文字としてアンダースコアを使用することで、名前が有効な Dart 識別子であることが保証されます。
その言語が、後に、シンボリック・インポートをサポートするようになった場合に役立つだろう。
Some file systems are not case-sensitive, so many projects require filenames to
be all lowercase. Using a separating character allows names to still be readable
in that form. Using underscores as the separator ensures that the name is still
a valid Dart identifier, which may be helpful if the language later supports
symbolic imports.

{:.good}
```text
my_package
└─ lib
   └─ file_system.dart
   └─ slider_menu.dart
```

{:.bad}
```text
mypackage
└─ lib
   └─ file-system.dart
   └─ SliderMenu.dart
```


### DO import プレフィックスは、`lowercase_with_underscores`を使用して名前をつける。 DO name import prefixes using `lowercase_with_underscores`

{% include linter-rule-mention.md rule="library_prefixes" %}

{:.good}
<?code-excerpt "style_lib_good.dart (import-as)" replace="/(package):examples\/effective_dart\/foo.dart[^']*/$1:angular_components\/angular_components.dart/g; /(package):examples\/effective_dart\/bar.dart[^']*/$1:js\/js.dart/g"?>
```dart
import 'dart:math' as math;
import 'package:angular_components/angular_components.dart' as angular_components;
import 'package:js/js.dart' as js;
```

{:.bad}
<?code-excerpt "style_lib_good.dart (import-as)" replace="/(package):examples\/effective_dart\/foo.dart[^']*/$1:angular_components\/angular_components.dart/g; /as angular_components/as angularComponents/g; /(package):examples\/effective_dart\/bar.dart[^']*/$1:js\/js.dart/g; / math/ Math/g;/as js/as JS/g"?>
```dart
import 'dart:math' as Math;
import 'package:angular_components/angular_components.dart' as angularComponents;
import 'package:js/js.dart' as JS;
```


### DO 他の識別子は、`lowerCamelCase` を使用して名前を付ける DO name other identifiers using `lowerCamelCase`

{% include linter-rule-mention.md rule="non_constant_identifier_names" %}

クラス・メンバー、トップレベル定義、変数、パラメータ、名前付きパラメータは、最初の単語を除く各単語の最初の文字を大文字にし、区切り文字を使用しない。
Class members, top-level definitions, variables, parameters, and named
parameters should capitalize the first letter of each word *except* the first
word, and use no separators.

{:.good}
<?code-excerpt "style_good.dart (misc-names)"?>
```dart
var count = 3;

HttpRequest httpRequest;

void align(bool clearItems) {
  // ...
}
```


### PREFER 定数名には、`lowerCamelCase`利用したほうがよい PREFER using `lowerCamelCase` for constant names

{% include linter-rule-mention.md rule="constant_identifier_names" %}

新しいコードでは、列挙値を含む定数変数には `lowerCamelCase` を使用する。
In new code, use `lowerCamelCase` for constant variables, including enum values.

{:.good}
<?code-excerpt "style_good.dart (const-names)"?>
```dart
const pi = 3.14;
const defaultTimeout = 1000;
final urlScheme = RegExp('^([a-z]+):');

class Dice {
  static final numberGenerator = Random();
}
```

{:.bad}
<?code-excerpt "style_bad.dart (const-names)"?>
```dart
const PI = 3.14;
const DefaultTimeout = 1000;
final URL_SCHEME = RegExp('^([a-z]+):');

class Dice {
  static final NUMBER_GENERATOR = Random();
}
```

次のような場合に、既存のコードとの一貫性を保つために、`SCREAMING_CAPS`（大文字の'_'区切り）を使用してもよい。
You may use `SCREAMING_CAPS` for consistency with existing code,
as in the following cases:

* 既に `SCREAMING_CAPS` を使用しているファイルやライブラリにコードを追加する場合。
  When adding code to a file or library that already uses `SCREAMING_CAPS`.
* Javaコードと並行するDartコードを生成する場合 - たとえば、[protobufs.][] から生成される列挙型の中
  When generating Dart code that's parallel to Java code—for example, 
  in enumerated types generated from [protobufs.][]

{{site.alert.note}}
  当初、定数にはJavaの`SCREAMING_CAPS`スタイルを使用していました。変更した理由はいくつかある：
  We initially used Java's `SCREAMING_CAPS` style for constants. We
  changed for a few reasons:

  *   `SCREAMING_CAPS`は、多くの場合、特にCSSの色の列挙値に対して、悪く見える。
      `SCREAMING_CAPS` looks bad for many cases, particularly enum values for
      things like CSS colors.
  *   定数はしばしば最終的な非定数変数に変更されるが、その場合は名前の変更が必要になる。
      Constants are often changed to final non-const variables, which would
      necessitate a name change.
  *   列挙型に自動的に定義される `values` プロパティは const で小文字である。
      The `values` property automatically defined on an enum type is const and
      lowercase.
{{site.alert.end}}

[protobufs.]: {{site.pub-pkg}}/protobuf


### DO 2文字以上の頭字語や略語は、単語と同様に大文字にする DO capitalize acronyms and abbreviations longer than two letters like words

大文字の頭字語は読みにくく、複数の頭字語が隣接していると曖昧な名前になる可能性がある。
たとえば、` HTTPSFTP` で始まる名前があった場合、それが HTTPS FTP を指しているのか、HTTP SFTP を指しているのかを見分ける方法はない。
Capitalized acronyms can be hard to read, and
multiple adjacent acronyms can lead to ambiguous names.
For example, given a name that starts with `HTTPSFTP`, there's no way
to tell if it's referring to HTTPS FTP or HTTP SFTP.

これを避けるため、頭字語や略語は通常の単語と同じように大文字で表記する。
To avoid this, acronyms and abbreviations are capitalized like regular words.

**例外:** IO (input/output) のような2文字の頭字語は完全に大文字になります：`IO`。
一方、ID（identification）のような2文字の略語は、通常の単語と同じように大文字で表記される： Id`。
**Exception:** Two-letter *acronyms* like IO (input/output) are fully
capitalized: `IO`. On the other hand, two-letter *abbreviations* like
ID (identification) are still capitalized like regular words: `Id`.

{:.good}
```dart
class HttpConnection {}
class DBIOPort {}
class TVVcr {}
class MrRogers {}

var httpRequest = ...
var uiHandler = ...
var userId = ...
Id id;
```

{:.bad}
```dart
class HTTPConnection {}
class DbIoPort {}
class TvVcr {}
class MRRogers {}

var hTTPRequest = ...
var uIHandler = ...
var userID = ...
ID iD;
```


### PREFER 使用しないコールバックパラメータには、 `_`, `__`,などを利用したほうがよい。 PREFER using `_`, `__`, etc. for unused callback parameters


コールバック関数の型シグネチャがパラメータを要求しているにもかかわらず、コールバック関数の実装がそのパラメータを使用しないことがあります。
この場合、未使用のパラメータに `_` という名前をつけるのが一般的です。
関数に複数の未使用パラメータがある場合は、アンダースコアを追加して名前の衝突を避けます： `__`, `___`, など
Sometimes the type signature of a callback function requires a parameter,
but the callback implementation doesn't _use_ the parameter.
In this case, it's idiomatic to name the unused parameter `_`.
If the function has multiple unused parameters, use additional
underscores to avoid name collisions: `__`, `___`, etc.

{:.good}
<?code-excerpt "style_good.dart (unused-callback-params)"?>
```dart
futureOfVoid.then((_) {
  print('Operation complete.');
});
```

このガイドラインは、*匿名かつローカル*な関数のみを対象としています。
これらの関数は、通常、使用されていないパラメータが何を表しているかが明らかなコンテキストで、すぐに使用されます。
対照的に、トップレベル関数やメソッド宣言には、そのようなコンテキストがないため、たとえ使用されなくても、各パラメータが何のためにあるのかが明確になるように、パラメータ名を付けなければなりません。
This guideline is only for functions that are both *anonymous and local*.
These functions are usually used immediately in a context where it's
clear what the unused parameter represents.
In contrast, top-level functions and method declarations don't have that context,
so their parameters must be named so that it's clear what each parameter is for,
even if it isn't used.


### DON'T プライベートでない識別子には先頭のアンダースコアを使わない DON'T use a leading underscore for identifiers that aren't private

Dartでは、メンバやトップレベルの宣言をプライベートとしてマークするために、識別子の先頭のアンダースコアを使用します。
これにより、ユーザーは先頭のアンダースコアからこれらの宣言のいずれかを連想するようになります。
ユーザは"_"を見て "private "と思うのです。
Dart uses a leading underscore in an identifier to mark members and top-level
declarations as private. This trains users to associate a leading underscore
with one of those kinds of declarations. They see "_" and think "private".

ローカル変数、パラメータ、ローカル関数、ライブラリ接頭辞には "private" という概念はない。
これらの名前にアンダースコアで始まるものがあると、読み手に混乱したシグナルを送ることになる。
それを避けるには、これらの名前に先頭のアンダースコアを使わないことだ。
There is no concept of "private" for local variables, parameters, local
functions, or library prefixes. When one of those has a name that starts with an
underscore, it sends a confusing signal to the reader. To avoid that, don't use
leading underscores in those names.


### DON'T use prefix letters

[ハンガリー記法](https://en.wikipedia.org/wiki/Hungarian_notation)やその他のスキームはBCPL（Basic Combined Programming Language、Basic-CPL、手続き型で命令型の構造化プログラミング言語）の時代に生まれたもので、コンパイラがコードを理解するのに役立つことはあまりありませんでした。
Dartは、宣言について、型、スコープ、変更可否、その他のプロパティを教えてくれるので、それらのプロパティを識別子名に記号化する理由はありません。
[Hungarian notation](https://en.wikipedia.org/wiki/Hungarian_notation) and
other schemes arose in the time of BCPL, when the compiler didn't do much to
help you understand your code. Because Dart can tell you the type, scope,
mutability, and other properties of your declarations, there's no reason to
encode those properties in identifier names.

{:.good}
```dart
defaultTimeout
```

{:.bad}
```dart
kDefaultTimeout
```

### DON'T 明示的にライブラリ名を指定しない DON'T explicitly name libraries

`library`ディレクティブに名前を追加することは技術的には可能ですが、レガシーな機能なので推奨されません。
Appending a name to the `library` directive is technically possible,
but is a legacy feature and discouraged. 

Dartは、各ライブラリのパスとファイル名に基づいて一意のタグを生成します。
ライブラリに名前を付けると、この生成されたURIが上書きされます。
URIがないと、ツールが問題のメイン・ライブラリ・ファイルを見つけるのが難しくなります。
Dart generates a unique tag for each library
based on its path and filename.
Naming libraries overrides this generated URI.
Without the URI, it can be harder for tools to find
the main library file in question. 

{:.bad}
<?code-excerpt "usage_bad.dart (library-dir)"?>
```dart

library my_library;
```

{:.good}
<?code-excerpt "docs_good.dart (library-doc)"?>
```dart
/// A really great test library.
@TestOn('browser')
library;
```

## 順序 Ordering

ファイルの前文を整理整頓するために、ディレクティブを記述する順番を定めています。
各 "セクション" は空白行で区切られるべきです。
To keep the preamble of your file tidy, we have a prescribed order that
directives should appear in. Each "section" should be separated by a blank line.

1つの linter ルールがすべての順序ガイドラインを処理する：
A single linter rule handles all the ordering guidelines:
[directives_ordering.](/tools/linter-rules/directives_ordering)


### DO 他の importの前に、`dart:` import を配置する。  DO place `dart:` imports before other imports

{% include linter-rule-mention.md rule="directives_ordering" %}

{:.good}
<?code-excerpt "style_lib_good.dart (dart-import-first)" replace="/\w+\/effective_dart\///g"?>
```dart
import 'dart:async';
import 'dart:html';

import 'package:bar/bar.dart';
import 'package:foo/foo.dart';
```


### DO 相対 importの前に、`package:` import を配置する。 DO place `package:` imports before relative imports

{% include linter-rule-mention.md rule="directives_ordering" %}

{:.good}
<?code-excerpt "style_lib_good.dart (pkg-import-before-local)" replace="/\w+\/effective_dart\///g;/'foo/'util/g"?>
```dart
import 'package:bar/bar.dart';
import 'package:foo/foo.dart';

import 'util.dart';
```


### DO すべての import の後に、別のセクションで export を指定する。 DO specify exports in a separate section after all imports

{% include linter-rule-mention.md rule="directives_ordering" %}

{:.good}
<?code-excerpt "style_lib_good.dart (export)"?>
```dart
import 'src/error.dart';
import 'src/foo_bar.dart';

export 'src/error.dart';
```

{:.bad}
<?code-excerpt "style_lib_bad.dart (export)"?>
```dart
import 'src/error.dart';
export 'src/error.dart';
import 'src/foo_bar.dart';
```


### DO アルファベット順で、セクションをソートする。 DO sort sections alphabetically

{% include linter-rule-mention.md rule="directives_ordering" %}

{:.good}
<?code-excerpt "style_lib_good.dart (sorted)" replace="/\w+\/effective_dart\///g"?>
```dart
import 'package:bar/bar.dart';
import 'package:foo/foo.dart';

import 'foo.dart';
import 'foo/foo.dart';
```

{:.bad}
<?code-excerpt "style_lib_bad.dart (sorted)" replace="/\w+\/effective_dart\///g"?>
```dart
import 'package:foo/foo.dart';
import 'package:bar/bar.dart';

import 'foo/foo.dart';
import 'foo.dart';
```


## フォーマット Formatting

多くの言語と同様、Dartは空白を無視する。
しかし、*人間*はそうではありません。
一貫した空白のスタイルを持つことで、人間の読者がコンパイラと同じようにコードを見ることができるようになります。
Like many languages, Dart ignores whitespace. However, *humans* don't. Having a
consistent whitespace style helps ensure that human readers see code the same
way the compiler does.


### DO `dart format` を利用してコードをフォーマットする。 DO format your code using `dart format`

フォーマットは面倒な作業で、リファクタリング中は特に時間がかかる。
幸い、その心配はありません。
私たちは [`dart format`][] という洗練された自動コード整形ツールを提供しています。
適用されるルールについては[いくつかのドキュメント][dart format docs]がありますが、Dartにとっての公式な空白処理ルールは *`dart format` が生成するもの* です。
Formatting is tedious work and is particularly time-consuming during
refactoring. Fortunately, you don't have to worry about it. We provide a
sophisticated automated code formatter called [`dart format`][] that does it for
you. We have [some documentation][dart format docs] on the rules it applies, but the
official whitespace-handling rules for Dart are *whatever `dart format` produces*.

残りのフォーマットガイドラインは、`dart format`では修正できないいくつかの事柄のためのものです。
The remaining formatting guidelines are for the few things `dart format` cannot fix
for you.

[`dart format`]: /tools/dart-format
[dart format docs]: https://github.com/dart-lang/dart_style/wiki/Formatting-Rules

### CONSIDER フォーマッタが扱いやすいコードに変更することを考えよう。 CONSIDER changing your code to make it more formatter-friendly

フォーマッターは、あなたがどんなコードを投げつけても、できる限りのことはしますが、奇跡を起こすことはできません。
コードが特に長い識別子を持っていたり、式（expression）が深くネストしていたり、さまざまな種類の演算子が混在していたりすると、フォーマットされた出力は読みにくいままになってしまうかもしれない。
The formatter does the best it can with whatever code you throw at it, but it
can't work miracles. If your code has particularly long identifiers, deeply
nested expressions, a mixture of different kinds of operators, etc. the
formatted output may still be hard to read.

そんなときは、コードを再編成するか単純化しよう。
ローカル変数名を短くしたり、式（expression）を新しいローカル変数に格納したりすることを検討しよう。
言い換えれば、手作業でコードをフォーマットし、より読みやすくしようとするときと同じような修正を加えるのです。
`dart format`は、美しいコードを作成するために、時には反復的に協力し合うパートナーシップのようなものだと考えてください。
When that happens, reorganize or simplify your code. Consider shortening a local
variable name or hoisting out an expression into a new local variable. In other
words, make the same kinds of modifications that you'd make if you were
formatting the code by hand and trying to make it more readable. Think of
`dart format` as a partnership where you work together, sometimes iteratively, 
to produce beautiful code.


### AVOID 80文字より長い行は避けよう。 AVOID lines longer than 80 characters

{% include linter-rule-mention.md rule="lines_longer_than_80_chars" %}

読みやすさの研究によると、長い行のテキストは読みにくい。
というのも、次の行の先頭に移動するとき、視線はより遠くまで移動しなければならないからだ。
これが、新聞や雑誌が複数の段組を採用している理由である。
Readability studies show that long lines of text are harder to read because your
eye has to travel farther when moving to the beginning of the next line. This is
why newspapers and magazines use multiple columns of text.

もし本当に80文字より長い行が必要だと感じたら、私たちの経験では、あなたのコードが冗長すぎる可能性があり、もう少しコンパクトにできるかもしれません。
その主な原因は、たいてい`VeryLongCamelCaseClassNames`である。
"その型名の各単語は、私に何か重要なことを伝えたり、名前の衝突を防いだりしているだろうか？" と自問してみてほしい。もしそうでなければ、省略することを検討しよう。
If you really find yourself wanting lines longer than 80 characters, our
experience is that your code is likely too verbose and could be a little more
compact. The main offender is usually `VeryLongCamelCaseClassNames`. Ask
yourself, "Does each word in that type name tell me something critical or
prevent a name collision?" If not, consider omitting it.

`dart format`はこの99%はやってくれるが、最後の1%は自分でやるということに注意してほしい。
長い文字列リテラルは80カラムに収まるように分割してくれないので、手動で行う必要がある。
Note that `dart format` does 99% of this for you, but the last 1% is you. 
It does not split long string literals to fit in 80 columns, 
so you have to do that manually.

**例外:**URIやファイルパスがコメントや文字列（通常はインポートやエクスポートの中）に含まれる場合、その行が80文字を超えることがあっても、そっくりそのままを残すことができる。
これにより、ソース・ファイルからパスを検索しやすくなります。
**Exception:** When a URI or file path occurs in a comment or string (usually in
an import or export), it may remain whole even if it causes the line to go over
80 characters. This makes it easier to search source files for a path.

**例外:**複数行の文字列には80文字より長い行が含まれることがあるが、これは文字列内部で改行が重要であり、行を短いものに分割するとプログラムが変わってしまうためである。
**Exception:** Multi-line strings can contain lines longer than 80 characters
because newlines are significant inside the string and splitting the lines into
shorter ones can alter the program.

### DO すべてのフロー制御文に中括弧を使用する。 DO use curly braces for all flow control statements {#do-use-curly-braces-for-all-flow-control-structures}

{% include linter-rule-mention.md rule="curly_braces_in_flow_control_structures" %}

そうすることで[未解決のelse][]問題は回避される。
Doing so avoids the [dangling else][] problem.

[未解決のelse]: https://en.wikipedia.org/wiki/Dangling_else
[dangling else]: https://en.wikipedia.org/wiki/Dangling_else

{:.good}
<?code-excerpt "style_good.dart (curly-braces)"?>
```dart
if (isWeekDay) {
  print('Bike to work!');
} else {
  print('Go dancing or read a book!');
}
```

**例外:** `else`句のない`if`文（statement）で、`if`文（statement）全体が1行に収まる場合は、よろしければ、中括弧を省略できる：
**Exception:** When you have an `if` statement with no `else` clause and the
whole `if` statement fits on one line, you can omit the braces if you prefer:

{:.good}
<?code-excerpt "style_good.dart (one-line-if)"?>
```dart
if (arg == null) return defaultValue;
```

本文が次の行に折り返されている場合は、中括弧を使う：
If the body wraps to the next line, though, use braces:

{:.good}
<?code-excerpt "style_good.dart (one-line-if-wrap)"?>
```dart
if (overflowChars != other.overflowChars) {
  return overflowChars < other.overflowChars;
}
```

{:.bad}
<?code-excerpt "style_bad.dart (one-line-if-wrap)"?>
```dart
if (overflowChars != other.overflowChars)
  return overflowChars < other.overflowChars;
```

---
title: "Effective Dart: Usage"
description: Guidelines for using language features to write maintainable code.
nextpage:
  url: /effective-dart/design
  title: Design
prevpage:
  url: /effective-dart/documentation
  title: Documentation
---
<?code-excerpt replace="/([A-Z]\w*)\d\b/$1/g"?>
<?code-excerpt path-base="misc/lib/effective_dart"?>

これらのガイドラインは、あなたのDartコードの本文で毎日使うことができます。
あなたのライブラリの *利用者* は、あなたがここに書かれていることを理解しているかどうかわからないかもしれませんが、ライブラリの *メンテナンス担当者* は、あなたがここに書かれていることを理解しているかどうかわかるでしょう。
You can use these guidelines every day in the bodies of your Dart code. *Users*
of your library may not be able to tell that you've internalized the ideas here,
but *maintainers* of it sure will.

## ライブラリ Libraries

これらのガイドラインは、複数のファイルから一貫性のある保守可能な方法でプログラムを構成するのに役立ちます。
このガイドラインを簡潔なものにするために、`import`ディレクティブと `export`ディレクティブをカバーするために "import "を使用しています。
ガイドラインはどちらにも同じように適用されます。
These guidelines help you compose your program out of multiple files in a
consistent, maintainable way. To keep these guidelines brief, they use "import"
to cover `import` and `export` directives. The guidelines apply equally to both. 

### DO `part of`ディレクティブに文字列を利用する。 DO use strings in `part of` directives

{% include linter-rule-mention.md rule="use_string_in_part_of_directives" %}

多くのDart開発者は、`part`の使用を完全に避けている。
彼らは、各ライブラリが単一のファイルである方が、コードの推論がしやすいと考えるからです。
`part` を使用してライブラリの一部を別のファイルに分割する場合、Dart は そのファイルがどのライブラリの一部であるかを示す必要があります。
Many Dart developers avoid using `part` entirely. They find it easier to reason
about their code when each library is a single file. If you do choose to use
`part` to split part of a library out into another file, Dart requires the other
file to in turn indicate which library it's a part of. 

Dart では `part of` ディレクティブでライブラリの *名前* を使うことができる。
ライブラリの名前を指定することはレガシーな機能であり、現在では[推奨されていません][]。
ライブラリ名は、パートがどのライブラリに属するかを決定する際に曖昧さをもたらす可能性があります。
Dart allows the `part of` directive to use the *name* of a library.
Naming libraries is a legacy feature that is now [discouraged][]. 
Library names can introduce ambiguity
when determining which library a part belongs to.

[推奨されていません]: /effective-dart/style#dont-explicitly-name-libraries
[discouraged]: /effective-dart/style#dont-explicitly-name-libraries

望ましい構文は、ライブラリファイルを直接指すURI文字列を使用することである。
例えば、`my_library.dart` というライブラリがあるとします：
The preferred syntax is to use a URI string that points
directly to the library file. 
If you have some library, `my_library.dart`, that contains:

<?code-excerpt "my_library.dart"?>
```dart
library my_library;

part 'some/other/file.dart';
```

その場合、part ファイルはライブラリファイルのURI文字列を使用しなければならない：
Then the part file should use the library file's URI string:

{:.good}
<?code-excerpt "some/other/file.dart"?>
```dart
part of '../../my_library.dart';
```
ライブラリの名前ではない：
Not the library name:

{:.bad}
<?code-excerpt "some/other/file_2.dart"?>
```dart
part of my_library;
```

### DON'T 他のパッケージの `src` ディレクトリにあるライブラリを import しない。 DON'T import libraries that are inside the `src` directory of another package

{% include linter-rule-mention.md rule="implementation_imports" %}

`lib` の下にある `src` ディレクトリは、そのパッケージの実装にプライベートな ライブラリが含まれるように[指定されます][package guide]。
パッケージのメンテナンス担当者がパッケージをバージョン管理する際には、この慣習を考慮に入れています。
彼らは、パッケージを壊すことなく、`src` 配下のコードに自由に変更を加えることができる。
The `src` directory under `lib` [is specified][package guide] to contain
libraries private to the package's own implementation. The way package
maintainers version their package takes this convention into account. They are
free to make sweeping changes to code under `src` without it being a breaking
change to the package.

[パッケージガイド]: /tools/pub/package-layout
[package guide]: /tools/pub/package-layout

つまり、他のパッケージのプライベート・ライブラリをインポートした場合、そのパッケージのマイナーな、理論的にはブレークポイントではないリリースが、あなたのコードを壊してしまう可能性があるということだ。
That means that if you import some other package's private library, a minor,
theoretically non-breaking point release of that package could break your code.


###  DON'T import パスが `lib` の中や外に出ないようにする。 DON'T allow an import path to reach into or out of `lib`

{% include linter-rule-mention.md rule="avoid_relative_lib_imports" %}

パッケージがコンピュータのどこに格納されているかを気にすることなく、`package:` インポートによって、パッケージの `lib` ディレクトリにある ライブラリにアクセスできる。
これが機能するためには、`lib`が、ディスク上の他のファイルと相対的な場所にあることを必要とする、インポートはできません。
言い換えると、`lib` ディレクトリの中のファイルの相対 import パスは、`lib` ディレクトリの外のファイルにアクセスできません。また、`lib` の外側にあるライブラリは、相対パスを使って `lib` ディレクトリに到達できない。
どちらをやっても、混乱したエラーや壊れたプログラムにつながる。
A `package:` import lets you access
a library inside a package's `lib` directory
without having to worry about where the package is stored on your computer.
For this to work, you cannot have imports that require the `lib`
to be in some location on disk relative to other files.
In other words, a relative import path in a file inside `lib`
can't reach out and access a file outside of the `lib` directory,
and a library outside of `lib` can't use a relative path
to reach into the `lib` directory.
Doing either leads to confusing errors and broken programs.

例えば、あなたのディレクトリ構造が次のようなものだとする：
For example, say your directory structure looks like this:

```text
my_package
└─ lib
   └─ api.dart
   test
   └─ api_test.dart
```

そして、`api_test.dart` が `api.dart` を2つの方法でインポートするとする：
And say `api_test.dart` imports `api.dart` in two ways:

{:.bad}
```dart
import 'package:my_package/api.dart';
import '../lib/api.dart';
```

Dartはこれらを、まったく関係のない2つのライブラリのインポートだと考えてしまいます。
Dartとあなた自身を混乱させないためには、以下の2つのルールに従ってください：
Dart thinks those are imports of two completely unrelated libraries.
To avoid confusing Dart and yourself, follow these two rules:

* import パスに `/lib/` を使用しない。
  Don't use `/lib/` in import paths.
* `lib` ディレクトリを免れるために、`../` を使用しない。
  Don't use `../` to escape the `lib` directory.

その代わりに、パッケージの `lib` ディレクトリにアクセスする必要があるときは（同じパッケージの `test` ディレクトリや他のトップレベルディレクトリからでも）、`package:` インポートを使う。
Instead, when you need to reach into a package's `lib` directory
(even from the same package's `test` directory
or any other top-level directory),
use a `package:` import.

{:.good}
```dart
import 'package:my_package/api.dart';
```

パッケージは、`lib` ディレクトリの *外* に出て、パッケージ内の他の場所からライブラリをインポートしてはならない。
A package should never reach *out* of its `lib` directory and
import libraries from other places in the package.


### PREFER 相対的な importパスを優先する。  PREFER relative import paths

{% include linter-rule-mention.md rule="prefer_relative_imports" %}

前のルールが適用されないときは、いつでも、このルールに従うこと。
インポートが `lib` を越え *ない* 場合、相対インポートを使うことを好む。
その方が短いからだ。
例えば、あなたのディレクトリ構造が以下のようになっているとする：
Whenever the previous rule doesn't come into play, follow this one.
When an import does *not* reach across `lib`, prefer using relative imports.
They're shorter.
For example, say your directory structure looks like this:

```text
my_package
└─ lib
   ├─ src
   │  └─ stuff.dart
   │  └─ utils.dart
   └─ api.dart
   test
   │─ api_test.dart
   └─ test_utils.dart
```

以下に、さまざまなライブラリーがお互いをインポートする方法を示す：
Here is how the various libraries should import each other:

**lib/api.dart:**

{:.good}
```dart
import 'src/stuff.dart';
import 'src/utils.dart';
```

**lib/src/utils.dart:**

{:.good}
```dart
import '../api.dart';
import 'stuff.dart';
```

**test/api_test.dart:**

{:.good}
```dart
import 'package:my_package/api.dart'; // Don't reach into 'lib'.

import 'test_utils.dart'; // Relative within 'test' is fine.
```


## Null


### DON'T 変数を `null` に明示的に初期化しない。 DON'T explicitly initialize variables to `null`

{% include linter-rule-mention.md rule="avoid_init_to_null" %}

変数がnullableでない型の場合、確実に初期化される前にその変数を使用しようとすると、Dartはコンパイルエラーを報告します。
変数がnullableな場合は、暗黙のうちに `null` に初期化されます。
Dartには「初期化されていないメモリ」という概念はなく、「安全」であるために変数を明示的に `null` に初期化する必要はありません。
If a variable has a non-nullable type, Dart reports a compile error if you try
to use it before it has been definitely initialized. If the variable is
nullable, then it is implicitly initialized to `null` for you. There's no
concept of "uninitialized memory" in Dart and no need to explicitly initialize a
variable to `null` to be "safe".

{:.good}
<?code-excerpt "usage_good.dart (no-null-init)"?>
```dart
Item? bestDeal(List<Item> cart) {
  Item? bestItem;

  for (final item in cart) {
    if (bestItem == null || item.price < bestItem.price) {
      bestItem = item;
    }
  }

  return bestItem;
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (no-null-init)" replace="/ = null/[!$&!]/g"?>
```dart
Item? bestDeal(List<Item> cart) {
  Item? bestItem[! = null!];

  for (final item in cart) {
    if (bestItem == null || item.price < bestItem.price) {
      bestItem = item;
    }
  }

  return bestItem;
}
```


### DON'T 明示的なデフォルト値 `null` を使用しない。 DON'T use an explicit default value of `null`

{% include linter-rule-mention.md rule="avoid_init_to_null" %}

nullableなパラメータをオプションにして、デフォルト値を与えなければ、言語は暗黙のうちに `null` をデフォルトとして使用するので、それを記述する必要はない。
If you make a nullable parameter optional but don't give it a default value, the
language implicitly uses `null` as the default, so there's no need to write it.

{:.good}
<?code-excerpt "usage_good.dart (default-value-null)"?>
```dart
void error([String? message]) {
  stderr.write(message ?? '\n');
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (default-value-null)"?>
```dart
void error([String? message = null]) {
  stderr.write(message ?? '\n');
}
```

<a id="prefer-using--to-convert-null-to-a-boolean-value"></a>

### DON'T 等値演算子（==）に、`true` または `false` を利用しない。  DON'T use `true` or `false` in equality operations

nullableでない boolean 式（expression）を boolean リテラルに対して評価するために、等価演算子を使うのは冗長である。
等価演算子を使わず、必要であれば、単項否定演算子 `!` を使う方が常にシンプルです：
Using the equality operator to evaluate a *non-nullable* boolean expression 
against a boolean literal is redundant. 
It's always simpler to eliminate the equality operator, 
and use the unary negation operator `!` if necessary:

{:.good}
<?code-excerpt "usage_good.dart (non-null-boolean-expression)"?>
```dart
if (nonNullableBool) { ... }

if (!nonNullableBool) { ... }
```

{:.bad}
<?code-excerpt "usage_bad.dart (non-null-boolean-expression)"?>
```dart
if (nonNullableBool == true) { ... }

if (nonNullableBool == false) { ... }
```

*nullableな* boolean 式（expression）を評価するには、`??` または、明示的な `!= null` チェックを使うべきである。
To evaluate a boolean expression that *is nullable*, you should use `??`
or an explicit `!= null` check.

{:.good}
<?code-excerpt "usage_good.dart (nullable-boolean-expression)"?>
```dart
// If you want null to result in false:
if (nullableBool ?? false) { ... }

// If you want null to result in false
// and you want the variable to type promote:
if (nullableBool != null && nullableBool) { ... }
```

{:.bad}
<?code-excerpt "usage_bad.dart (nullable-boolean-expression)"?>
```dart
// Static error if null:
if (nullableBool) { ... }

// If you want null to be false:
if (nullableBool == true) { ... }
```

`nullableBool == true` は有効な表現だが、
いくつかの理由から使うべきではない：
`nullableBool == true` is a viable expression, 
but shouldn't be used for several reasons:

* そのコードが `null` と関係があることを示すものではない。
  It doesn't indicate the code has anything to do with `null`.

* 明らかに`null` 関連ではないので、等価演算子が冗長で削除できるnon-nullableのケースと間違われやすい。
これは、左辺の boolean 式（expression）が、nullを生成する可能性がない場合にのみ当てはまることで、nullを生成する可能性がある場合には当てはまらない。
  Because it's not evidently `null` related, 
  it can easily be mistaken for the non-nullable case,
  where the equality operator is redundant and can be removed.
  That's only true when the boolean expression on the left
  has no chance of producing null, but not when it can.

* boolean ロジックが紛らわしい。
`nullableBool` が null の場合、`nullableBool==true` は条件が `false` と評価されることを意味する。
  The boolean logic is confusing. If `nullableBool` is null, 
  then `nullableBool == true` means the condition evaluates to `false`.

`??` 演算子は、nullに関係することが起こっていることを明確にするので、冗長な操作と間違われることはない。
そのロジックもより明確になる； 
式（expression）が `null` となる結果は、 boolean リテラルと同じです。
The `??` operator makes it clear that something to do with null is happening,
so it won't be mistaken for a redundant operation. 
The logic is much clearer too; 
the result of the expression being `null` is the same as the boolean literal.

`if` 文（statement）の条件内の変数に `??` のような null-aware演算子を使っても、その変数をnullableでない型に昇格しない。
`if` 文（statement）の本文の中で、変数を昇格させたい場合は、`??` の代わりに、明示的に `!= null` チェックを使用したほうがよい。
Using a null-aware operator such as `??` on a variable inside a condition
doesn't promote the variable to a non-nullable type. 
If you want the variable to be promoted inside the body of the `if` statement,
it's better to use an explicit `!= null` check instead of `??`. 

### AVOID 変数が初期化されているかどうかをチェックする必要がある場合は、`late` 変数を避ける。 AVOID `late` variables if you need to check whether they are initialized

Dart は `late` 変数が初期化されたか、代入されたかを知る方法を提供しません。
もしアクセスすると、すぐにイニシャライザが実行されるか（イニシャライザがある場合）、例外がスローされます。
時には、`late`が適しているような、遅延的に初期化される状態があるかもしれませんが、初期化がもう行われているかどうかを*知る*ことができることも必要です。
Dart offers no way to tell if a `late` variable
has been initialized or assigned to.
If you access it, it either immediately runs the initializer
(if it has one) or throws an exception.
Sometimes you have some state that's lazily initialized
where `late` might be a good fit,
but you also need to be able to *tell* if the initialization has happened yet.

`late` 変数に初期化状態を格納し、変数が設定されたかどうかを追跡する別の boolean フィールドを持つことで、初期化を検知できますが、
Dart は *内部的に* `late` 変数の初期化状態を保持するため、それは冗長です。
その代わりに、通常は、変数を非`late`でnullableにする方が明確です。
そうすれば、`null` をチェックすることで、変数が初期化されたかどうかを確認できます。
Although you could detect initialization by storing the state in a `late` variable
and having a separate boolean field
that tracks whether the variable has been set,
that's redundant because Dart *internally*
maintains the initialized status of the `late` variable.
Instead, it's usually clearer to make the variable non-`late` and nullable.
Then you can see if the variable has been initialized
by checking for `null`.

もちろん、`null` が変数にとって有効な初期化された値であれば、十中八九は、別の boolean フィールドをもつことが理にかなっているだろう。
Of course, if `null` is a valid initialized value for the variable,
then it probably does make sense to have a separate boolean field.


### CONSIDER 型昇格を有効にするために、nullable なフィールドをローカル変数に割り当てることを検討する。 CONSIDER assigning a nullable field to a local variable to enable type promotion

nullableな変数が `null` と等しくないことをチェックすると、その変数はnullableでない型に昇格する。
これにより、その変数のメンバにアクセスしたり、nullableでない型を期待する関数に渡したりできるようになる。
残念ながら、昇格はローカル変数とパラメータに対してのみ有効なので、フィールドやトップレベル変数は昇格しません。
Checking that a nullable variable is not equal to `null` promotes the variable
to a non-nullable type. That lets you access members on the variable and pass it
to functions expecting a non-nullable type. Unfortunately, promotion is only
sound for local variables and parameters, so fields and top-level variables
aren't promoted.

これを回避する1つのパターンは、フィールドの値をローカル変数に代入することだ。
その変数のnullチェックは昇格するので、安全にnullableでないとして扱うことができる。
One pattern to work around this is to assign the field's value to a local
variable. Null checks on that variable do promote, so you can safely treat
it as non-nullable.

{:.good}
<?code-excerpt "usage_good.dart (shadow-nullable-field)"?>
```dart
class UploadException {
  final Response? response;

  UploadException([this.response]);

  @override
  String toString() {
    final response = this.response;
    if (response != null) {
      return 'Could not complete upload to ${response.url} '
          '(error code ${response.errorCode}): ${response.reason}.';
    }

    return 'Could not upload (no response).';
  }
}
```

ローカル変数への代入は、フィールドやトップレベル変数が使われるたびに `!` を使うよりもすっきりしていて安全です：
Assigning to a local variable can be cleaner and safer than using `!` every
place the field or top-level variable is used:

{:.bad}
<?code-excerpt "usage_bad.dart (shadow-nullable-field)" replace="/!\./[!!!]./g"?>
```dart
class UploadException {
  final Response? response;

  UploadException([this.response]);

  @override
  String toString() {
    if (response != null) {
      return 'Could not complete upload to ${response[!!!].url} '
          '(error code ${response[!!!].errorCode}): ${response[!!!].reason}.';
    }

    return 'Could not upload (no response).';
  }
}
```

ローカル変数を使用するときは注意してください。
フィールドに書き戻す必要がある場合は、代わりにローカル変数に書き戻さないように注意してください。
(ローカル変数を `final` にすることで、このようなミスを防ぐことができます）。
また、ローカル変数がまだスコープ内にある間に、フィールドが変更される可能性がある場合、ローカル変数が古い値を持つ可能性があります。
単純にフィールドに `!` を使うのがベストな場合もあります。
Be careful when using a local variable. If you need to write back to the field,
make sure that you don't write back to the local variable instead. (Making the
local variable `final` can prevent such mistakes.) Also, if the field might
change while the local is still in scope, then the local might have a stale
value. Sometimes it's best to simply use `!` on the field.


## 文字列 Strings

Dartで文字列を構成する際に留意すべきベストプラクティスをいくつか紹介しよう。
Here are some best practices to keep in mind when composing strings in Dart.

### DO 文字列リテラルを連結するには、隣接する文字列を使用する。 DO use adjacent strings to concatenate string literals

{% include linter-rule-mention.md rule="prefer_adjacent_string_concatenation" %}

文字列リテラル -値ではなく、実際のクォーテーションで囲まれたリテラル形式- が2つある場合、`+`を使って連結する必要はありません。
CやC++と同じように、単純に隣り合わせにするだけでいいのです。
これは、1行に収まらないような長い文字列を1つにする良い方法です。
If you have two string literals—not values, but the actual quoted literal
form—you do not need to use `+` to concatenate them. Just like in C and
C++, simply placing them next to each other does it. This is a good way to make
a single long string that doesn't fit on one line.

{:.good}
<?code-excerpt "usage_good.dart (adjacent-strings-literals)"?>
```dart
raiseAlarm('ERROR: Parts of the spaceship are on fire. Other '
    'parts are overrun by martians. Unclear which are which.');
```

{:.bad}
<?code-excerpt "usage_bad.dart (adjacent-strings-literals)"?>
```dart
raiseAlarm('ERROR: Parts of the spaceship are on fire. Other ' +
    'parts are overrun by martians. Unclear which are which.');
```

### PREFER 文字列と値の合成に補間を使う方を優先する PREFER using interpolation to compose strings and values

{% include linter-rule-mention.md rule="prefer_interpolation_to_compose_strings" %}

他の言語から来た人は、リテラルや他の値から文字列を作るために長い `+` チェーンを使うことに慣れているでしょう。
Dartでもそれは使えますが、ほとんどの場合、補間を使った方がすっきりしていて短いです：
If you're coming from other languages, you're used to using long chains of `+`
to build a string out of literals and other values. That does work in Dart, but
it's almost always cleaner and shorter to use interpolation:

{:.good}
<?code-excerpt "usage_good.dart (string-interpolation)"?>
```dart
'Hello, $name! You are ${year - birth} years old.';
```

{:.bad}
<?code-excerpt "usage_bad.dart (string-interpolation)"?>
```dart
'Hello, ' + name + '! You are ' + (year - birth).toString() + ' y...';
```

このガイドラインは、*複数の*リテラルと値を組み合わせる場合に適用されることに注意してください。
単一のオブジェクトだけを文字列に変換する場合は、`.toString()` を使ってもよい。
Note that this guideline applies to combining *multiple* literals and values.
It's fine to use `.toString()` when converting only a single object to a string.

### AVOID 中括弧が不要な場合は、補間で中括弧を使用を避ける。 AVOID using curly braces in interpolation when not needed

{% include linter-rule-mention.md rule="unnecessary_brace_in_string_interps" %}

直後に英数字が続かない単純な識別子を補間する場合は、`{}`は省略する。
If you're interpolating a simple identifier not immediately followed by more
alphanumeric text, the `{}` should be omitted.

{:.good}
<?code-excerpt "usage_good.dart (string-interpolation-avoid-curly)"?>
```dart
var greeting = 'Hi, $name! I love your ${decade}s costume.';
```

{:.bad}
<?code-excerpt "usage_bad.dart (string-interpolation-avoid-curly)"?>
```dart
var greeting = 'Hi, ${name}! I love your ${decade}s costume.';
```

## コレクション Collections

Dartは、リスト、マップ、キュー、セットの4種類のコレクションをサポートしています。
コレクションには以下のベストプラクティスが適用されます。
Out of the box, Dart supports four collection types: lists, maps, queues, and sets.
The following best practices apply to collections.

### DO 可能な限りコレクション・リテラルを使用する。 DO use collection literals when possible

{% include linter-rule-mention.md rule="prefer_collection_literals" %}

Dartには3つのコア・コレクション型がある： List, Map, Setだ。
MapクラスとSetクラスには、ほとんどのクラスと同じように無名のコンストラクタがあります。
しかし、これらのコレクションは非常に頻繁に使用されるため、Dartにはそれらを作成するためのより良い組み込み構文があります：
Dart has three core collection types: List, Map, and Set. The Map and Set
classes have unnamed constructors like most classes do. But because these
collections are used so frequently, Dart has nicer built-in syntax for creating
them:

{:.good}
<?code-excerpt "usage_good.dart (collection-literals)"?>
```dart
var points = <Point>[];
var addresses = <String, Address>{};
var counts = <int>{};
```

{:.bad}
<?code-excerpt "usage_bad.dart (collection-literals)"?>
```dart
var addresses = Map<String, Address>();
var counts = Set<int>();
```

このガイドラインは、これらのクラスの *名前付き* コンストラクタには適用されないことに注意してください。
`List.from()`、`Map.fromIterable()`、と その仲間のすべてはそれらの用途があります。
(Listクラスにも無名コンストラクタがありますが、nullセーフなDartでは禁止されています)。
Note that this guideline doesn't apply to the *named* constructors for those
classes. `List.from()`, `Map.fromIterable()`, and friends all have their uses.
(The List class also has an unnamed constructor, but it is prohibited in null
safe Dart.)

コレクションリテラルはDartで特に強力で、
他のコレクションの内容を含めるための[spread演算子][spread]や、
内容を構築する際に制御フローを実行するための[`if`と`for`][control]にアクセスできるからです：
Collection literals are particularly powerful in Dart
because they give you access to the [spread operator][spread]
for including the contents of other collections,
and [`if` and `for`][control] for performing control flow while
building the contents:

[spread]: /language/collections#spread-operators
[control]: /language/collections#control-flow-operators

{:.good}
<?code-excerpt "usage_good.dart (spread-etc)"?>
```dart
var arguments = [
  ...options,
  command,
  ...?modeFlags,
  for (var path in filePaths)
    if (path.endsWith('.dart')) path.replaceAll('.dart', '.js')
];
```

{:.bad}
<?code-excerpt "usage_bad.dart (spread-etc)"?>
```dart
var arguments = <String>[];
arguments.addAll(options);
arguments.add(command);
if (modeFlags != null) arguments.addAll(modeFlags);
arguments.addAll(filePaths
    .where((path) => path.endsWith('.dart'))
    .map((path) => path.replaceAll('.dart', '.js')));
```


### DON'T コレクションが空かどうかを調べるのに `.length` を使わない。 DON'T use `.length` to see if a collection is empty

{% include linter-rule-mention.md rule1="prefer_is_empty" rule2="prefer_is_not_empty" %}

[Iterable][]コントラクトは、コレクションがその長さを知っていたり、一定の時間でそれを提供できることを要求されていません。
コレクションに *何か* が含まれているかどうかを確認するためだけに `.length` を呼び出すと、非常に時間がかかることがあります。
The [Iterable][] contract does not require that a collection know its length or
be able to provide it in constant time. Calling `.length` just to see if the
collection contains *anything* can be painfully slow.

[iterable]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable-class.html

代わりに、より高速で読みやすいゲッターがある： .isEmpty`と`.isNotEmpty`である。
結果を否定する必要がない方を使いましょう。
Instead, there are faster and more readable getters: `.isEmpty` and
`.isNotEmpty`. Use the one that doesn't require you to negate the result.

{:.good}
<?code-excerpt "usage_good.dart (dont-use-length)"?>
```dart
if (lunchBox.isEmpty) return 'so hungry...';
if (words.isNotEmpty) return words.join(' ');
```

{:.bad}
<?code-excerpt "usage_bad.dart (dont-use-length)"?>
```dart
if (lunchBox.length == 0) return 'so hungry...';
if (!words.isEmpty) return words.join(' ');
```


### AVOID 関数リテラルで `Iterable.forEach()` を使うのを避ける。 AVOID using `Iterable.forEach()` with a function literal

{% include linter-rule-mention.md rule="avoid_function_literals_in_foreach_calls" %}

forEach()`関数はJavaScriptで広く使われている。なぜなら、組み込まれた`for-in`ループは、通常あなたが望むことをしないからだ。
Dartでは、シーケンスを繰り返し処理したい場合、これを行うには、ループを使うのが一般的だ。
`forEach()` functions are widely used in JavaScript because the built in
`for-in` loop doesn't do what you usually want. In Dart, if you want to iterate
over a sequence, the idiomatic way to do that is using a loop.

{:.good}
<?code-excerpt "usage_good.dart (avoid-forEach)"?>
```dart
for (final person in people) {
  ...
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (avoid-forEach)"?>
```dart
people.forEach((person) {
  ...
});
```

このガイドラインでは、特に"関数 *リテラル*"と書かれていることに注意してください。
各要素に対して *既に存在する* 関数を呼び出したいのであれば、`forEach()`で構わない。
Note that this guideline specifically says "function *literal*". If you want to
invoke some *already existing* function on each element, `forEach()` is fine.

{:.good}
<?code-excerpt "usage_good.dart (forEach-over-func)"?>
```dart
people.forEach(print);
```

また、`Map.forEach()`を使っても常にOKであることにも注意してください。
マップは反復可能ではないので、このガイドラインは適用されない。
Also note that it's always OK to use `Map.forEach()`. Maps aren't iterable, so
this guideline doesn't apply.

### DON'T 結果の型を変更するつもりがない限り、`List.from()`は使わない。 DON'T use `List.from()` unless you intend to change the type of the result

同じ要素を含む新しいリストを作るには、2つの方法がある：
Given an Iterable, there are two obvious ways to produce a new List that
contains the same elements:

<?code-excerpt "../../test/effective_dart_test.dart (list-from-1)"?>
```dart
var copy1 = iterable.toList();
var copy2 = List.from(iterable);
```

明らかな違いは、最初の方が短いということである。
*重要な*違いは、最初のものが元のオブジェクトの型引数を保持することである：
The obvious difference is that the first one is shorter. The *important*
difference is that the first one preserves the type argument of the original
object:

{:.good}
<?code-excerpt "../../test/effective_dart_test.dart (list-from-good)"?>
```dart
// Creates a List<int>:
var iterable = [1, 2, 3];

// Prints "List<int>":
print(iterable.toList().runtimeType);
```

{:.bad}
<?code-excerpt "../../test/effective_dart_test.dart (list-from-bad)"?>
```dart
// Creates a List<int>:
var iterable = [1, 2, 3];

// Prints "List<dynamic>":
print(List.from(iterable).runtimeType);
```

型を変更*したい*場合は、`List.from()`を呼び出すと便利である：
If you *want* to change the type, then calling `List.from()` is useful:

{:.good}
<?code-excerpt "../../test/effective_dart_test.dart (list-from-3)"?>
```dart
var numbers = [1, 2.3, 4]; // List<num>.
numbers.removeAt(1); // Now it only contains integers.
var ints = List<int>.from(numbers);
```

しかし、iterableをコピーして元の型を保持することだけが目的であったり、型を気にしないのであれば、`toList()`を使ってください。
But if your goal is just to copy the iterable and preserve its original type, or
you don't care about the type, then use `toList()`.


### DO コレクションを型によってフィルタリングするには、`whereType()` を使用する。 DO use `whereType()` to filter a collection by type

{% include linter-rule-mention.md rule="prefer_iterable_whereType" %}

オブジェクトが混在したリストがあり、その中から整数だけを取り出したいとしよう。
このように `where()` を使うことができる：
Let's say you have a list containing a mixture of objects, and you want to get
just the integers out of it. You could use `where()` like this:

{:.bad}
<?code-excerpt "usage_bad.dart (where-type)"?>
```dart
var objects = [1, 'a', 2, 'b', 3];
var ints = objects.where((e) => e is int);
```

これは冗長だが、悪いことに、十中八九、望まない型の iterable を返してしまう。
ここの例では フィルタリングしているのがこのタイプだから、`Iterable<int>` を欲していそうなのに、`Iterable<Object>` を返している。
This is verbose, but, worse, it returns an iterable whose type probably isn't
what you want. In the example here, it returns an `Iterable<Object>` even though
you likely want an `Iterable<int>` since that's the type you're filtering it to.

cast()`を追加することで、上記のエラーを "修正" しているコードを見かけることがある：
Sometimes you see code that "corrects" the above error by adding `cast()`:

{:.bad}
<?code-excerpt "usage_bad.dart (where-type-2)"?>
```dart
var objects = [1, 'a', 2, 'b', 3];
var ints = objects.where((e) => e is int).cast<int>();
```

これは冗長で、2つのラッパーが作成され、2つのレイヤーのインダイレクトと冗長な実行時チェックが発生する。
幸いなことに、コア・ライブラリにはこのユースケースのために [`whereType()`][where-type] メソッドが用意されている：
That's verbose and causes two wrappers to be created, with two layers of
indirection and redundant runtime checking. Fortunately, the core library has
the [`whereType()`][where-type] method for this exact use case:

[where-type]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable/whereType.html

{:.good}
<?code-excerpt "../../test/effective_dart_test.dart (whereType)"?>
```dart
var objects = [1, 'a', 2, 'b', 3];
var ints = objects.whereType<int>();
```

`whereType()`を使うと簡潔で、目的の型の [Iterable][] を生成し、ラッピングの不必要なレベルがない。
Using `whereType()` is concise, produces an [Iterable][] of the desired type,
and has no unnecessary levels of wrapping.


### DON'T 近くの操作で済む場合は、`cast() ` を使わない。DON'T use `cast()` when a nearby operation will do

iterable や stream を扱うとき、そのオブジェクトに対していくつかの変換を行うことがよくある。
最後に、ある型引数を持つオブジェクトを生成したいとします。
`cast()` を呼び出す代わりに、既存の変換の1つで型を変更できるかどうかを確認します。
Often when you're dealing with an iterable or stream, you perform several
transformations on it. At the end, you want to produce an object with a certain
type argument. Instead of tacking on a call to `cast()`, see if one of the
existing transformations can change the type.

すでに `toList()` を呼び出している場合は、`T` は結果のリストのタイプである `List<T>.from()` の呼び出しに置き換える。
If you're already calling `toList()`, replace that with a call to
[`List<T>.from()`][list-from] where `T` is the type of resulting list you want.

[list-from]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/List/List.from.html

{:.good}
<?code-excerpt "usage_good.dart (cast-list)"?>
```dart
var stuff = <dynamic>[1, 2];
var ints = List<int>.from(stuff);
```

{:.bad}
<?code-excerpt "usage_bad.dart (cast-list)"?>
```dart
var stuff = <dynamic>[1, 2];
var ints = stuff.toList().cast<int>();
```

`map()` を呼び出す場合は、明示的な型引数を与えて、目的の型の iterable を生成するようにする。
`map()` に渡した関数に基づいて、型推論は正しい型を選ぶことが多いが、明示的に指定する必要がある場合もある。
If you are calling `map()`, give it an explicit type argument so that it
produces an iterable of the desired type. Type inference often picks the correct
type for you based on the function you pass to `map()`, but sometimes you need
to be explicit.

{:.good}
<?code-excerpt "usage_good.dart (cast-map)" replace="/\(n as int\)/n/g"?>
```dart
var stuff = <dynamic>[1, 2];
var reciprocals = stuff.map<double>((n) => 1 / n);
```

{:.bad}
<?code-excerpt "usage_bad.dart (cast-map)" replace="/\(n as int\)/n/g"?>
```dart
var stuff = <dynamic>[1, 2];
var reciprocals = stuff.map((n) => 1 / n).cast<double>();
```


### AVOID `cast()` の使用を避ける。 AVOID using `cast()`

これは前のルールをよりソフトに一般化したものである。
あるオブジェクトの型を固定するために使える操作が近くにないことがあります。
そのような場合でも、可能な限り `cast()` を使ってコレクションの型を "変更 "することは避けてください。
This is the softer generalization of the previous rule. Sometimes there is no
nearby operation you can use to fix the type of some object. Even then, when
possible avoid using `cast()` to "change" a collection's type.

代わりに、これらのオプションのどれかを選ぶ：
Prefer any of these options instead:

*   **適切なタイプで作成する。** コレクションが最初に作成されるコードを変更し、正しい型を持つようにする。
    **Create it with the right type.** Change the code where the collection is
    first created so that it has the right type.

*   **アクセス時の要素をキャストする。** コレクションをすぐに反復処理する場合は、各要素を反復処理内でキャストする。
    **Cast the elements on access.** If you immediately iterate over the
    collection, cast each element inside the iteration.

*   **`List.from()` を利用して、熱心にキャストする。**
    最終的にコレクション内のほとんどの要素にアクセスすることになり、
    オブジェクトが元の生のオブジェクトに戻される必要がない場合は、`List.from()`を使って変換します。
    **Eagerly cast using `List.from()`.** If you'll eventually access most of
    the elements in the collection, and you don't need the object to be backed
    by the original live object, convert it using `List.from()`.

    The `cast()` method returns a lazy collection that checks the element type
    on *every operation*. If you perform only a few operations on only a few
    elements, that laziness can be good. But in many cases, the overhead of lazy
    validation and of wrapping outweighs the benefits.
    `cast()`メソッドは、*操作のたびに*要素の型をチェックする遅延コレクションを返す。
    数個の要素に対して数個の操作しか行わないのであれば、その遅延は良いことかもしれません。
    しかし、多くの場合、遅延検証やラップのオーバーヘッドはその利点を上回ります。


以下は、**正しいタイプで作成する** 例である。:
Here is an example of **creating it with the right type:**

{:.good}
<?code-excerpt "usage_good.dart (cast-at-create)"?>
```dart
List<int> singletonList(int value) {
  var list = <int>[];
  list.add(value);
  return list;
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (cast-at-create)"?>
```dart
List<int> singletonList(int value) {
  var list = []; // List<dynamic>.
  list.add(value);
  return list.cast<int>();
}
```

以下は、**アクセス時の各要素のキャストである。** :
Here is **casting each element on access:**

{:.good}
<?code-excerpt "usage_good.dart (cast-iterate)" replace="/\(n as int\)/[!$&!]/g"?>
```dart
void printEvens(List<Object> objects) {
  // We happen to know the list only contains ints.
  for (final n in objects) {
    if ([!(n as int)!].isEven) print(n);
  }
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (cast-iterate)"?>
```dart
void printEvens(List<Object> objects) {
  // We happen to know the list only contains ints.
  for (final n in objects.cast<int>()) {
    if (n.isEven) print(n);
  }
}
```

以下は、**`List.from()` を使った熱心なキャストである。** :
Here is **casting eagerly using `List.from()`:**

{:.good}
<?code-excerpt "usage_good.dart (cast-from)"?>
```dart
int median(List<Object> objects) {
  // We happen to know the list only contains ints.
  var ints = List<int>.from(objects);
  ints.sort();
  return ints[ints.length ~/ 2];
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (cast-from)"?>
```dart
int median(List<Object> objects) {
  // We happen to know the list only contains ints.
  var ints = objects.cast<int>();
  ints.sort();
  return ints[ints.length ~/ 2];
}
```

これらの選択肢がいつもうまくいくとは限りませんし、もちろん、時には `cast()` が正しい答えになることもあります。
しかし、このメソッドは少しリスキーで望ましくないものだと考えてください。 — それは、ゆっくり考えることができ、注意しないと実行時に失敗する可能性がある。
These alternatives don't always work, of course, and sometimes `cast()` is the
right answer. But consider that method a little risky and undesirable—it
can be slow and may fail at runtime if you aren't careful.


## 関数 Functions

Dartでは、関数もオブジェクトである。
ここでは、関数に関するベストプラクティスをいくつか紹介する。
In Dart, even functions are objects. Here are some best practices
involving functions.


### DO 関数宣言を使用して関数を名前にバインドする。 DO use a function declaration to bind a function to a name

{% include linter-rule-mention.md rule="prefer_function_declarations_over_variables" %}

現代の言語は、ローカルにネストされた関数やクロージャがいかに便利にするかを実現している。
ある関数が別の関数の内部で定義されていることはよくあることだ。
多くの場合、この関数はすぐにコールバックとして使われ、名前は必要ありません。
関数式はそのような場合に最適です。
Modern languages have realized how useful local nested functions and closures
are. It's common to have a function defined inside another one. In many cases,
this function is used as a callback immediately and doesn't need a name. A
function expression is great for that.

しかし、名前をつける必要がある場合は、ラムダを変数にバインドするのではなく、関数宣言文を使う。
But, if you do need to give it a name, use a function declaration statement
instead of binding a lambda to a variable.

{:.good}
<?code-excerpt "usage_good.dart (func-decl)"?>
```dart
void main() {
  void localFunction() {
    ...
  }
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (func-decl)"?>
```dart
void main() {
  var localFunction = () {
    ...
  };
}
```

### DON'T tear-off（関数ポインタ）で済む場合はラムダを作らない。 DON'T create a lambda when a tear-off will do

{% include linter-rule-mention.md rule="unnecessary_lambdas" %}

関数やメソッド、名前付きコンストラクタを参照するが、括弧を省略すると、Dartは_tear-off（関数ポインタ）_を作成します。
このtear-off（関数ポインタ）は、関数と同じパラメータを取り、呼び出すとき、根底となる関数を実行するクロージャである。
クロージャが受け取るのと同じパラメータを持つ名前付き関数を呼び出すクロージャが必要なだけなら、
その呼び出しをラムダに手動でラップする必要はない。
When you refer to a function, method, or named constructor but omit the
parentheses, Dart creates a _tear-off_—a closure that takes the same
parameters as the function and invokes the underlying function when you call it.
If all you need is a closure that invokes a named function with the same
parameters as the closure accepts, don't manually wrap the call in a lambda.

{:.good}
<?code-excerpt "usage_good.dart (use-tear-off)"?>
```dart
var charCodes = [68, 97, 114, 116];
var buffer = StringBuffer();

// Function:
charCodes.forEach(print);

// Method:
charCodes.forEach(buffer.write);

// Named constructor:
var strings = charCodes.map(String.fromCharCode);

// Unnamed constructor:
var buffers = charCodes.map(StringBuffer.new);
```

{:.bad}
<?code-excerpt "usage_bad.dart (use-tear-off)"?>
```dart
var charCodes = [68, 97, 114, 116];
var buffer = StringBuffer();

// Function:
charCodes.forEach((code) {
  print(code);
});

// Method:
charCodes.forEach((code) {
  buffer.write(code);
});

// Named constructor:
var strings = charCodes.map((code) => String.fromCharCode(code));

// Unnamed constructor:
var buffers = charCodes.map((code) => StringBuffer(code));
```


### DO 名前付きパラメータとデフォルト値を区切るには `=` を使う。 DO use `=` to separate a named parameter from its default value

{% include linter-rule-mention.md rule="prefer_equal_for_default_values" %}

Dart 3 より前のバージョンでは、名前付きパラメータとデフォルトの値区切り文字として `:` と `=` の両方を使用できました。
位置で指定するオプションパラメータ（[] を利用する）との一貫性を保つために、 `=` を使用してください。
名前で指定するオプションパラメータ（{} を利用する）は、 関数利用時に、`:` を使用して値を指定する。
Before Dart 3, Dart allowed both `:` and `=` 
as the default value separator for named parameters. 
For consistency with optional positional parameters, use `=`.

{:.good}
<?code-excerpt "usage_good.dart (default-separator)"?>
```dart
void insert(Object item, {int at = 0}) { ... }
```

{:.bad}
```dart
void insert(Object item, {int at: 0}) { ... }
```


## 変数 Variables

以下のベストプラクティスでは、Dartにおける変数の最適な使用方法について説明します。
The following best practices describe how to best use variables in Dart.

### DO ローカル変数の `var` と `final` の一貫したルールに従う。 DO follow a consistent rule for `var` and `final` on local variables

ほとんどのローカル変数には型アノテーションを付けるべきでなく、`var` または `final` を使って宣言すべきである。
どのような場合にどちらかを使うかについては、広く使われている2つのルールがある：
Most local variables shouldn't have type annotations and should be declared
using just `var` or `final`. There are two rules in wide use for when to use one
or the other:

*   再代入されないローカル変数には `final` を、再代入されるローカル変数には `var` を使用する。
    Use `final` for local variables that are not reassigned and `var` for those
    that are.

*   すべてのローカル変数には `var` を使う。
    ローカル変数には絶対に `final` を使わないこと。
    （もちろん、フィールドやトップレベル変数に `final` を使うことは推奨される。）
    Use `var` for all local variables, even ones that aren't reassigned. Never use
    `final` for locals. (Using `final` for fields and top-level variables is
    still encouraged, of course.)

どちらのルールでも構わないが、どれか一つを選び、コード全体で一貫して適用すること。
そうすれば、読者が `var` を見たときに、その変数が関数の後半で代入されることを意味するのかどうかがわかる。
Either rule is acceptable, but pick *one* and apply it consistently throughout
your code. That way when a reader sees `var`, they know whether it means that
the variable is assigned later in the function.


### AVOID 計算できるものは保存しない。 AVOID storing what you can calculate

クラスを設計するとき、同じ根本的な状態に複数のビューを公開したいことがよくあります。
多くの場合、コンストラクタでそれらのビューをすべて計算し、格納するコードを見かけます：
When designing a class, you often want to expose multiple views into the same
underlying state. Often you see code that calculates all of those views in the
constructor and then stores them:

{:.bad}
<?code-excerpt "usage_bad.dart (calc-vs-store1)"?>
```dart
class Circle {
  double radius;
  double area;
  double circumference;

  Circle(double radius)
      : radius = radius,
        area = pi * radius * radius,
        circumference = pi * 2.0 * radius;
}
```

このコードには2つの間違いがある。
第一に、メモリを無駄にしている可能性が高い。
面積と円周は、厳密に言えば*キャッシュ*である。
すでに持っている他のデータから再計算できるような計算が保存されているのだ。
メモリの増加とCPU使用量の削減を引き換えにしているのだ。
そのトレードオフに見合うだけのパフォーマンス上の問題があるのか？
This code has two things wrong with it. First, it's likely wasting memory. The
area and circumference, strictly speaking, are *caches*. They are stored
calculations that we could recalculate from other data we already have. They are
trading increased memory for reduced CPU usage. Do we know we have a performance
problem that merits that trade-off?

さらに悪いことに、そのコードは*間違っている*。
キャッシュの問題は*無効化*である。- キャッシュが古くなって再計算が必要になったとき、どうやってそれを知るのか？
ここでは、`radius`が変更可能であるにもかかわらず、そのようなことは決してしない。
異なる値を代入しても、`area`と`circumference`は以前の、今は正しくない値を保持する。
Worse, the code is *wrong*. The problem with caches is *invalidation*—how
do you know when the cache is out of date and needs to be recalculated? Here, we
never do, even though `radius` is mutable. You can assign a different value and
the `area` and `circumference` will retain their previous, now incorrect values.

キャッシュの無効化を正しく処理するには、このようにする必要がある：
To correctly handle cache invalidation, we would need to do this:

{:.bad}
<?code-excerpt "usage_bad.dart (calc-vs-store2)"?>
```dart
class Circle {
  double _radius;
  double get radius => _radius;
  set radius(double value) {
    _radius = value;
    _recalculate();
  }

  double _area = 0.0;
  double get area => _area;

  double _circumference = 0.0;
  double get circumference => _circumference;

  Circle(this._radius) {
    _recalculate();
  }

  void _recalculate() {
    _area = pi * _radius * _radius;
    _circumference = pi * 2.0 * _radius;
  }
}
```

これは、書いたり、保守したり、デバッグしたり、読んだりするのに膨大な量のコードになる。
その代わり、最初の実装は以下のようにすべきだ。
That's an awful lot of code to write, maintain, debug, and read. Instead, your
first implementation should be:

{:.good}
<?code-excerpt "usage_good.dart (calc-vs-store)"?>
```dart
class Circle {
  double radius;

  Circle(this.radius);

  double get area => pi * radius * radius;
  double get circumference => pi * 2.0 * radius;
}
```

このコードは短く、メモリ使用量も少なく、エラーも起こりにくい。
円を表現するのに必要な最小限のデータしか保存しない。
真実という1つソースがあるだけなので、同期が取れなくなるフィールドはない。
This code is shorter, uses less memory, and is less error-prone. It stores the
minimal amount of data needed to represent the circle. There are no fields to
get out of sync because there is only a single source of truth.

場合によっては、遅い計算の結果をキャッシュする必要があるかもしれません、 
しかし、パフォーマンス上の問題があることを理解し、慎重に実行し、最適化について説明するコメントを残した後にのみ、
それを実行してください。
In some cases, you may need to cache the result of a slow calculation, but only
do that after you know you have a performance problem, do it carefully, and
leave a comment explaining the optimization.


## メンバ Members

Dartでは、オブジェクトは関数（メソッド）またはデータ（インスタンス変数）となるメンバーを持ちます。
オブジェクトのメンバには、以下のベストプラクティスが適用されます。
In Dart, objects have members which can be functions (methods) or data (instance
variables). The following best practices apply to an object's members.

### DON'T 不必要にゲッターやセッターでフィールドをラップしない。 DON'T wrap a field in a getter and setter unnecessarily

{% include linter-rule-mention.md rule="unnecessary_getters_setters" %}

JavaやC#では、すべてのフィールドをゲッターやセッター（C#ではプロパティ）の後ろに隠すのが一般的だ、
たとえ実装がフィールドに転送するだけであっても。
そうすることで、これらのメンバにさらに手を加える必要が生じたときに、呼出元に触れる必要がなくなるからだ。
なぜなら、Javaでは、ゲッター・メソッドを呼び出すことは、フィールドにアクセスすることとは異なり、
C#では、プロパティにアクセスすることは、生のフィールドにアクセスすることとバイナリ互換性がないからです。
In Java and C#, it's common to hide all fields behind getters and setters (or
properties in C#), even if the implementation just forwards to the field. That
way, if you ever need to do more work in those members, you can without needing
to touch the call sites. This is because calling a getter method is different
than accessing a field in Java, and accessing a property isn't binary-compatible
with accessing a raw field in C#.

Dartにはこの制限はない。
フィールドとゲッター/セッターは完全に見分けがつかない。
クラスでフィールドを公開し、後で、そのフィールドを使うコードに触れることなく、
それをゲッターやセッターでラップすることができる。
Dart doesn't have this limitation. Fields and getters/setters are completely
indistinguishable. You can expose a field in a class and later wrap it in a
getter and setter without having to touch any code that uses that field.

{:.good}
<?code-excerpt "usage_good.dart (dont-wrap-field)"?>
```dart
class Box {
  Object? contents;
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (dont-wrap-field)"?>
```dart
class Box {
  Object? _contents;
  Object? get contents => _contents;
  set contents(Object? value) {
    _contents = value;
  }
}
```


### PREFER 読み取り専用にするためには、`final` フィールドを使うことを優先する。 PREFER using a `final` field to make a read-only property

外部のコードが、見ることはできるが、代入することはできないフィールドがある場合、
多くの場合うまくいく単純な解決策は、単純に`final`とマークすることである。
If you have a field that outside code should be able to see but not assign to, a
simple solution that works in many cases is to simply mark it `final`.

{:.good}
<?code-excerpt "usage_good.dart (final)"?>
```dart
class Box {
  final contents = [];
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (final)"?>
```dart
class Box {
  Object? _contents;
  Object? get contents => _contents;
}
```

もちろん、コンストラクタの外でフィールドに内部的に代入する必要がある場合は、
"private field, public getter" パターンが必要になるかもしれないが、必要になるまで手を伸ばさないでほしい。
Of course, if you need to internally assign to the field outside of the
constructor, you may need to do the "private field, public getter" pattern, but
don't reach for that until you need to.


### CONSIDER 単純なメンバーには `=>` を使用することを検討する。 CONSIDER using `=>` for simple members

{% include linter-rule-mention.md rule="prefer_expression_function_bodies" %}

関数式に `=>` を使うだけでなく、Dartではメンバを `=>` で定義することもできる。
このスタイルは、値を計算して返すだけの単純なメンバに適しています。
In addition to using `=>` for function expressions, Dart also lets you define
members with it. That style is a good fit for simple members that just calculate
and return a value.

{:.good}
<?code-excerpt "usage_good.dart (use-arrow)"?>
```dart
double get area => (right - left) * (bottom - top);

String capitalize(String name) =>
    '${name[0].toUpperCase()}${name.substring(1)}';
```

コードを*書く*人たちは`=>`が大好きなようだが、それを悪用するのはとても簡単で、*読みにくい*コードになってしまう。
宣言が2、3行を超えたり、深い入れ子になった式（カスケード演算子（同じオブジェクトに対する連続実行）や条件演算子はよくある違反者だ）を含んでいる場合は、
自分自身やコードを読まなければならないすべての人のために、ブロック本体といくつかのステートメントを使おう。
People *writing* code seem to love `=>`, but it's very easy to abuse it and end
up with code that's hard to *read*. If your declaration is more than a couple of
lines or contains deeply nested expressions—cascades and conditional
operators are common offenders—do yourself and everyone who has to read
your code a favor and use a block body and some statements.

{:.good}
<?code-excerpt "usage_good.dart (arrow-long)"?>
```dart
Treasure? openChest(Chest chest, Point where) {
  if (_opened.containsKey(chest)) return null;

  var treasure = Treasure(where);
  treasure.addAll(chest.contents);
  _opened[chest] = treasure;
  return treasure;
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (arrow-long)"?>
```dart
Treasure? openChest(Chest chest, Point where) => _opened.containsKey(chest)
    ? null
    : _opened[chest] = (Treasure(where)..addAll(chest.contents));
```

値を返さないメンバに `=>` を使うこともできる。
セッターが小さくて、それに対応するゲッターが `=>` を使っている場合に、これは、よく使われます。
You can also use `=>` on members that don't return a value. This is idiomatic
when a setter is small and has a corresponding getter that uses `=>`.

{:.good}
<?code-excerpt "usage_good.dart (arrow-setter)"?>
```dart
num get x => center.x;
set x(num value) => center = Point(value, center.y);
```


### DON'T `this.`は、名前付きコンストラクタにリダイレクトする場合やシャドーイング（外側のスコープの変数と同名の変数を定義して、そのスコープで外側のスコープの変数にアクセスできなくする機能）を避ける場合を除き、使わない。 DON'T use `this.` except to redirect to a named constructor or to avoid shadowing {#dont-use-this-when-not-needed-to-avoid-shadowing}

{% include linter-rule-mention.md rule="unnecessary_this" %}

JavaScriptでは、現在メソッドが実行されているオブジェクトのメンバを参照するために、
明示的に `this.` を指定する必要があるが、C++、Java、C#のようなDartには制限はない。
JavaScript requires an explicit `this.` to refer to members on the object whose
method is currently being executed, but Dart—like C++, Java, and
C#—doesn't have that limitation.

`this.`を使う必要があるのは2回だけだ。
一つは、同じ名前のローカル変数が、アクセスしたいメンバの影になる場合です：
There are only two times you need to use `this.`. One is when a local variable
with the same name shadows the member you want to access:

{:.bad}
<?code-excerpt "usage_bad.dart (this-dot)"?>
```dart
class Box {
  Object? value;

  void clear() {
    this.update(null);
  }

  void update(Object? value) {
    this.value = value;
  }
}
```

{:.good}
<?code-excerpt "usage_good.dart (this-dot)"?>
```dart
class Box {
  Object? value;

  void clear() {
    update(null);
  }

  void update(Object? value) {
    this.value = value;
  }
}
```

その他に`this.`を使うのは、名前付きコンストラクターにリダイレクトするときだ：
The other time to use `this.` is when redirecting to a named constructor:

{:.bad}
<?code-excerpt "usage_bad.dart (this-dot-constructor)"?>
```dart
class ShadeOfGray {
  final int brightness;

  ShadeOfGray(int val) : brightness = val;

  ShadeOfGray.black() : this(0);

  // This won't parse or compile!
  // ShadeOfGray.alsoBlack() : black();
}
```

{:.good}
<?code-excerpt "usage_good.dart (this-dot-constructor)"?>
```dart
class ShadeOfGray {
  final int brightness;

  ShadeOfGray(int val) : brightness = val;

  ShadeOfGray.black() : this(0);

  // But now it will!
  ShadeOfGray.alsoBlack() : this.black();
}
```

ここで留意すべきは、コンストラクタのパラメータが、コンストラクタのイニシャライザ・リストのフィールドをシャドウすることはない：
Note that constructor parameters never shadow fields in constructor initializer
lists:

{:.good}
<?code-excerpt "usage_good.dart (param-dont-shadow-field-ctr-init)"?>
```dart
class Box extends BaseBox {
  Object? value;

  Box(Object? value)
      : value = value,
        super(value);
}
```

これは意外に見えるが、思い通りに動く。
幸運なことに、このようなコードは、初期化フォーマルやスーパーイニシャライザーのおかげで、比較的まれである。
This looks surprising, but works like you want. Fortunately, code like this is
relatively rare thanks to initializing formals and super initializers.


### DO 可能な限り、宣言時にフィールドを初期化する。 DO initialize fields at their declaration when possible

フィールドがコンストラクタのパラメータに依存しない場合は、宣言時に初期化できますし、初期化すべきです。
その方がコードが少なくて済みますし、クラスに複数のコンストラクタがある場合に重複を避けることができます。
If a field doesn't depend on any constructor parameters, it can and should be
initialized at its declaration. It takes less code and avoids duplication when
the class has multiple constructors.

{:.bad}
<?code-excerpt "usage_bad.dart (field-init-at-decl)"?>
```dart
class ProfileMark {
  final String name;
  final DateTime start;

  ProfileMark(this.name) : start = DateTime.now();
  ProfileMark.unnamed()
      : name = '',
        start = DateTime.now();
}
```

{:.good}
<?code-excerpt "usage_good.dart (field-init-at-decl)"?>
```dart
class ProfileMark {
  final String name;
  final DateTime start = DateTime.now();

  ProfileMark(this.name);
  ProfileMark.unnamed() : name = '';
}
```

例えば、他のフィールドを使用したり、メソッドを呼び出したりするために `this` を参照する必要があるので、
フィールドの中には、宣言時に初期化できないものがあります。
しかしながら、フィールドが `late` とマークされていれば、イニシャライザは `this` にアクセスすることが*できる*。
Some fields can't be initialized at their declarations because they need to reference
`this`—to use other fields or call methods, for example. However, if the
field is marked `late`, then the initializer *can* access `this`.

もちろん、フィールドがコンストラクタのパラメータに依存したり、
異なったコンストラクタによって異なった初期化をする場合は、このガイドラインは適用されない。
Of course, if a field depends on constructor parameters, or is initialized
differently by different constructors, then this guideline does not apply.


## コンストラクタ Constructors

クラスのコンストラクタを宣言する際には、以下のベスト・プラクティスが適用されます。
The following best practices apply to declaring constructors for a class.

### DO 可能な限り initializing formal （初期化形式）を使用する。 DO use initializing formals when possible

{% include linter-rule-mention.md rule="prefer_initializing_formals" %}

多くのフィールドは、コンストラクターのパラメーターから直接初期化される。以下のように：
Many fields are initialized directly from a constructor parameter, like:

{:.bad}
<?code-excerpt "usage_bad.dart (field-init-as-param)"?>
```dart
class Point {
  double x, y;
  Point(double x, double y)
      : x = x,
        y = y;
}
```

フィールドを定義するのに _4_ 回も `x` と入力しなければならない。もっといい方法がある：
We've got to type `x` _four_ times here to define a field. We can do better:

{:.good}
<?code-excerpt "usage_good.dart (field-init-as-param)"?>
```dart
class Point {
  double x, y;
  Point(this.x, this.y);
}
```

コンストラクタのパラメーターの前の この`this.`構文は"initializing formal" （初期化形式）と呼ばれる。
常に、これを有利とできるわけではありません。
ときどき、初期化するフィールドの名前と一致しない名前つきパラメータを持ちたいこともあるでしょう。
しかし、"initializing formal" （初期化形式）を使うことが*できる*ときは、使う*べき*です。
This `this.` syntax before a constructor parameter is called an "initializing
formal". You can't always take advantage of it. Sometimes you want to have a
named parameter whose name doesn't match the name of the field you are
initializing. But when you *can* use initializing formals, you *should*.


### DON'T コンストラクタのイニシャライザー・リストを使用する場合は、`late`を使用しない。 DON'T use `late` when a constructor initializer list will do

Dartでは、null 非許容のフィールドは、読むことができる前に、初期化する必要があります。
フィールドはコンストラクタ本体の中で読み込めるので、本体が実行される前に null 非許容のフィールドを初期化しないとエラーになります。
Dart requires you to initialize non-nullable fields before they can be read.
Since fields can be read inside the constructor body, 
this means you get an error if you don't initialize a
non-nullable field before the body runs.

フィールドに`late`をマークすることで、このエラーをなくすことができる。
これにより、フィールドが初期化される前にアクセスすると、コンパイル時のエラーが*実行時*のエラーに変わります。
いくつかのケースで、これが必要な場合もありますが、多くの場合、コンストラクタのイニシャライザー・リストでフィールドを初期化するのが正しい修正です：
You can make this error go away by marking the field `late`. That turns the
compile-time error into a *runtime* error if you access the field before it is
initialized. That's what you need in some cases, but often the right fix is to
initialize the field in the constructor initializer list:

{:.good}
<?code-excerpt "usage_good.dart (late-init-list)"?>
```dart
class Point {
  double x, y;
  Point.polar(double theta, double radius)
      : x = cos(theta) * radius,
        y = sin(theta) * radius;
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (late-init-list)"?>
```dart
class Point {
  late double x, y;
  Point.polar(double theta, double radius) {
    x = cos(theta) * radius;
    y = sin(theta) * radius;
  }
}
```

イニシャライザー・リストを使うと、コンストラクターのパラメーターにアクセスできるようになり、フィールドを読み込めるようになる前に初期化できるようになります。
ですから、もしイニシャライザー・リストを使うことが可能なら、フィールドを`late`にして、いくつかの静的な安全性とパフォーマンスを失うよりはよいことです。
The initializer list gives you access to constructor parameters and lets you
initialize fields before they can be read. So, if it's possible to use an initializer list,
that's better than making the field `late` and losing some static safety and
performance.


### DO 空のコンストラクタ本体には `{}` の代わりに `;` を使う。 DO use `;` instead of `{}` for empty constructor bodies

{% include linter-rule-mention.md rule="empty_constructor_bodies" %}

Dartでは、本体が空のコンストラクタはセミコロンだけで終了させることができます。(実際、constコンストラクタでは必須です)。
In Dart, a constructor with an empty body can be terminated with just a
semicolon. (In fact, it's required for const constructors.)

{:.good}
<?code-excerpt "usage_good.dart (semicolon-for-empty-body)"?>
```dart
class Point {
  double x, y;
  Point(this.x, this.y);
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (semicolon-for-empty-body)"?>
```dart
class Point {
  double x, y;
  Point(this.x, this.y) {}
}
```

### DON'T `new` を使わない。 DON'T use `new`

{% include linter-rule-mention.md rule="unnecessary_new" %}

コンストラクタを呼び出す際に、`new` キーワードは任意である。
ファクトリーコンストラクタでは `new` を呼び出しても実際には新しいオブジェクトを返さないことがあるため、その意味は明確ではありません。
The `new` keyword is optional when calling a constructor.
Its meaning is not clear because factory constructors mean a
`new` invocation may not actually return a new object.

言語ではまだ `new` が許されているが、非推奨と考え、コードでは使わないようにしよう。
The language still permits `new`, but consider
it deprecated and avoid using it in your code.

{:.good}
<?code-excerpt "usage_good.dart (no-new)"?>
```dart
Widget build(BuildContext context) {
  return Row(
    children: [
      RaisedButton(
        child: Text('Increment'),
      ),
      Text('Click!'),
    ],
  );
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (no-new)" replace="/new/[!$&!]/g"?>
```dart
Widget build(BuildContext context) {
  return [!new!] Row(
    children: [
      [!new!] RaisedButton(
        child: [!new!] Text('Increment'),
      ),
      [!new!] Text('Click!'),
    ],
  );
}
```


### DON'T `const`を冗長に使わない。 DON'T use `const` redundantly

{% include linter-rule-mention.md rule="unnecessary_const" %}

式が定数で*なければならない*コンテキストでは、`const`キーワードは暗黙的なものであり、書く必要はないし、書くべきでもない。
そのようなコンテキストとは、その中にあるすべての式のことである：
In contexts where an expression *must* be constant, the `const` keyword is
implicit, doesn't need to be written, and shouldn't. Those contexts are any
expression inside:

* 定数コレクションリテラル。
  A const collection literal.
* 定数コンストラクタ呼び出し
  A const constructor call
* メタデータアノテーション。
  A metadata annotation.
* 定数 変数宣言ためのイニシャライザ。
  The initializer for a const variable declaration.
* switch case 式 - caseの本体ではなく、`:` の前の `case` の直後の部分。
  A switch case expression—the part right after `case` before the `:`, not
  the body of the case.

（Dartの将来のバージョンでは、const以外のデフォルト値がサポートされる可能性があるため、デフォルト値はこのリストに含まれていません。）
(Default values are not included in this list because future versions of Dart
may support non-const default values.)

基本的に、`const`の代わりに`new`と書くとエラーになるような場所では、Dartでは`const`を省略できる。
Basically, any place where it would be an error to write `new` instead of
`const`, Dart allows you to omit the `const`.

{:.good}
<?code-excerpt "usage_good.dart (no-const)"?>
```dart
const primaryColors = [
  Color('red', [255, 0, 0]),
  Color('green', [0, 255, 0]),
  Color('blue', [0, 0, 255]),
];
```

{:.bad}
<?code-excerpt "usage_bad.dart (no-const)" replace="/ (const)/ [!$1!]/g"?>
```dart
const primaryColors = [!const!] [
  [!const!] Color('red', [!const!] [255, 0, 0]),
  [!const!] Color('green', [!const!] [0, 255, 0]),
  [!const!] Color('blue', [!const!] [0, 0, 255]),
];
```

## エラーハンドリング Error handling

Dartは、プログラムでエラーが発生したときに例外を使用します。
例外のキャッチとスローには、以下のベストプラクティスが適用されます。
Dart uses exceptions when an error occurs in your program. The following
best practices apply to catching and throwing exceptions.

### AVOID `on` 節のないキャッチを避ける。 AVOID catches without `on` clauses

{% include linter-rule-mention.md rule="avoid_catches_without_on_clauses" %}

`on` 修飾子のないcatch節は、tryブロック内のコードによってスローされた *すべての* ものをキャッチします。
[Pokémon exception handling][pokemon] は、あなたが望んでいるものではない可能性が高いです。
あなたのコードは [StackOverflowError][] や [OutOfMemoryError][] を正しく処理していますか？
tryブロック内のメソッドに間違った引数を渡した場合、デバッガに間違いを指摘させたいですか、それとも役に立つ [ArgumentError][] を見えなくしたいですか？
スローされた [AssertionError][] をキャッチしているのだから、そのコード内の `assert()` 文が事実上消えてしまうことを望むか？
A catch clause with no `on` qualifier catches *anything* thrown by the code in
the try block. [Pokémon exception handling][pokemon] is very likely not what you
want. Does your code correctly handle [StackOverflowError][] or
[OutOfMemoryError][]? If you incorrectly pass the wrong argument to a method in
that try block do you want to have your debugger point you to the mistake or
would you rather that helpful [ArgumentError][] get swallowed? Do you want any
`assert()` statements inside that code to effectively vanish since you're
catching the thrown [AssertionError][]s?

おそらく答えは「ノー」だろう。その場合は、キャッチする型にフィルターをかけるべきだ。
たいていの場合、`on` 節を設けて、自分が認識していて正しく処理できる種類の実行時エラーに限定すべきである。
The answer is probably "no", in which case you should filter the types you
catch. In most cases, you should have an `on` clause that limits you to the
kinds of runtime failures you are aware of and are correctly handling.

まれに、なんらかの実行時エラーをキャッチしたい場合がある。
これは、通常、フレームワークや低レベルのコードで、原因となる問題から、任意のアプリケーション・コードを分離するようにするためだ。
このような場合でも、すべての型をキャッチするよりは、[Exception][] をキャッチした方がよいことが多い。
Exceptionはすべての *実行時* エラーの基本クラスであり、コードの *プログラム上の* バグを示すエラーは除外される。
In rare cases, you may wish to catch any runtime error. This is usually in
framework or low-level code that tries to insulate arbitrary application code
from causing problems. Even here, it is usually better to catch [Exception][]
than to catch all types. Exception is the base class for all *runtime* errors
and excludes errors that indicate *programmatic* bugs in the code.


### DON'T `on` 節のないキャッチではエラーは廃棄しない。 DON'T discard errors from catches without `on` clauses

コードの ある領域からスローされる可能性のある *すべての* ものをキャッチする必要があると本当に感じるなら、キャッチしたものを *何か* してください。
ログに残したり、ユーザーに表示したり、再スローしたりしてください。しかし、静かに、エラーを破棄しないでください。
If you really do feel you need to catch *everything* that can be thrown from a
region of code, *do something* with what you catch. Log it, display it to the
user or rethrow it, but do not silently discard it.


### DO プログラム上のエラーに対してのみ `Error` を実装したオブジェクトを投げる。 DO throw objects that implement `Error` only for programmatic errors

[Errorクラス][] は *プログラム上の* エラーの基本クラスである。
このタイプのオブジェクト、あるいは [ArgumentError][] のようなサブインターフェースの1つが投げられるとき、
それはあなたのコードに *バグ* があることを意味する。
APIが誤って使用されていることを呼び出し元に報告したい場合、Errorを投げることでそのシグナルを明確に送ることができる。
The [Error][] class is the base class for *programmatic* errors. When an object
of that type or one of its subinterfaces like [ArgumentError][] is thrown, it
means there is a *bug* in your code. When your API wants to report to a caller
that it is being used incorrectly throwing an Error sends that signal clearly.

逆に、例外がコードのバグを示さない何らかの実行時エラーである場合、Errorをスローするのは誤解を招く。
その代わりに、コアとなるExceptionクラスや他の型を投げてください。
Conversely, if the exception is some kind of runtime failure that doesn't
indicate a bug in the code, then throwing an Error is misleading. Instead, throw
one of the core Exception classes or some other type.


### DON'T `Error` やそれを実装した型を明示的にキャッチしない。 DON'T explicitly catch `Error` or types that implement it

{% include linter-rule-mention.md rule="avoid_catching_errors" %}

以上のことから、次のようになる。
Errorはコードにバグがあることを示すので、コールスタック全体を巻き戻し、プログラムを停止させ、
スタック・トレースを表示して、バグを見つけて修正できるようにすべきである。
This follows from the above. Since an Error indicates a bug in your code, it
should unwind the entire callstack, halt the program, and print a stack trace so
you can locate and fix the bug.

このようなタイプのエラーをキャッチすると、そのプロセスが壊れてバグが隠されてしまう。
事後的に、この例外を処理するためにエラー処理ハンドリングを *追加* するのではなく、
最初の場所で、スローされる例外の原因となっているコードに戻って修正するのだ。
Catching errors of these types breaks that process and masks the bug. Instead of
*adding* error-handling code to deal with this exception after the fact, go back
and fix the code that is causing it to be thrown in the first place.


### DO キャッチした例外を再スローするには `rethrow` を使う。 DO use `rethrow` to rethrow a caught exception

{% include linter-rule-mention.md rule="use_rethrow_when_possible" %}

例外を再スローすると決めるならば、 `throw` を使って同じ例外オブジェクトをスローするのに代わって、
`rethrow` ステートメントを使うことを好む。
`rethrow` は例外の元のスタックトレースを保持する。
一方、 `throw` はスタックトレースを最後にスローされた位置にリセットする。
If you decide to rethrow an exception, prefer using the `rethrow` statement
instead of throwing the same exception object using `throw`.
`rethrow` preserves the original stack trace of the exception. `throw` on the
other hand resets the stack trace to the last thrown position.

{:.bad}
<?code-excerpt "usage_bad.dart (rethrow)"?>
```dart
try {
  somethingRisky();
} catch (e) {
  if (!canHandle(e)) throw e;
  handle(e);
}
```

{:.good}
<?code-excerpt "usage_good.dart (rethrow)" replace="/rethrow/[!$&!]/g"?>
```dart
try {
  somethingRisky();
} catch (e) {
  if (!canHandle(e)) [!rethrow!];
  handle(e);
}
```


## 非同期 Asynchrony

Dartには、非同期プログラミングをサポートする言語機能がいくつかあります。
以下のベストプラクティスは、非同期コーディングに適用されます。
Dart has several language features to support asynchronous programming.
The following best practices apply to asynchronous coding.

### PREFER 生の Future を使うより async/await を優先する。 PREFER async/await over using raw futures

非同期コードは、futuresのような よい抽象化されたコードを使用している場合でも、悪名が知れわたるほどに、読みにくく、デバッグしにくいです。
`async` / `await` 構文は可読性を向上させ、非同期コード内ですべてのDart制御フロー構造を使用できる。
Asynchronous code is notoriously hard to read and debug, even when using a nice
abstraction like futures. The `async`/`await` syntax improves readability and
lets you use all of the Dart control flow structures within your async code.

{:.good}
<?code-excerpt "usage_good.dart (async-await)" replace="/async|await/[!$&!]/g"?>
```dart
Future<int> countActivePlayers(String teamName) [!async!] {
  try {
    var team = [!await!] downloadTeam(teamName);
    if (team == null) return 0;

    var players = [!await!] team.roster;
    return players.where((player) => player.isActive).length;
  } catch (e) {
    log.error(e);
    return 0;
  }
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (async-await)"?>
```dart
Future<int> countActivePlayers(String teamName) {
  return downloadTeam(teamName).then((team) {
    if (team == null) return Future.value(0);

    return team.roster.then((players) {
      return players.where((player) => player.isActive).length;
    });
  }).catchError((e) {
    log.error(e);
    return 0;
  });
}
```

### DON'T 有用な効果がないときは、`async`は 使わない。 DON'T use `async` when it has no useful effect

非同期に関連する処理を行う関数では、`async` を使う習性がつきやすい。
しかし、いくつかのケースで、本質的でない。
関数の動作を変えずに `async` を省略できる場合は、そうしてください。
It's easy to get in the habit of using `async` on any function that does
anything related to asynchrony. But in some cases, it's extraneous. If you can
omit the `async` without changing the behavior of the function, do so.

{:.good}
<?code-excerpt "usage_good.dart (unnecessary-async)"?>
```dart
Future<int> fastestBranch(Future<int> left, Future<int> right) {
  return Future.any([left, right]);
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (unnecessary-async)"?>
```dart
Future<int> fastestBranch(Future<int> left, Future<int> right) async {
  return Future.any([left, right]);
}
```

`async` が有用 *である* ケースは以下の通りである：
Cases where `async` *is* useful include:

* `await` を使っている。（これは明らかなことだ）。
  You are using `await`. (This is the obvious one.)

* 非同期にエラーを返している。
  `async` してから `throw` する方が `return Future.error(...)` よりも短いです。
  You are returning an error asynchronously. `async` and then `throw` is shorter
  than `return Future.error(...)`.

* 値を返していて、暗黙的に 値を future でラップしたい。
  `async` は `Future.value(...)` よりも短い。
  You are returning a value and you want it implicitly wrapped in a future.
  `async` is shorter than `Future.value(...)`.

{:.good}
<?code-excerpt "usage_good.dart (async)"?>
```dart
Future<void> usesAwait(Future<String> later) async {
  print(await later);
}

Future<void> asyncError() async {
  throw 'Error!';
}

Future<String> asyncValue() async => 'value';
```

### CONSIDER ストリームを変換するために高次のメソッドを使用することを検討する。 CONSIDER using higher-order methods to transform a stream

これは iterable に関する上の提案と類似している。
Stream は同じメソッドの多くをサポートし、エラーの送信やクローズなども正しくハンドリングする。
This parallels the above suggestion on iterables. Streams support many of the
same methods and also handle things like transmitting errors, closing, etc.
correctly.

### AVOID Completer を直接的に使用するのを避ける。 AVOID using Completer directly

非同期プログラミングの初心者の多くは、futureを生成するコードを書きたいと思っている。
Futureのコンストラクタは彼らのニーズに合わないようなので、最終的にはCompleterクラスを見つけて、それを使う。
Many people new to asynchronous programming want to write code that produces a
future. The constructors in Future don't seem to fit their need so they
eventually find the Completer class and use that.

{:.bad}
<?code-excerpt "usage_bad.dart (avoid-completer)"?>
```dart
Future<bool> fileContainsBear(String path) {
  var completer = Completer<bool>();

  File(path).readAsString().then((contents) {
    completer.complete(contents.contains('bear'));
  });

  return completer.future;
}
```

Completer は2種類の低レベルコードに必要である。: 新しい非同期プリミティブと、フューチャーを使わない非同期コードとのインターフェイスです。
他のほとんどのコードでは async/await か [`Future.then()`][then] を使うべきだ。なぜなら、その方が明確でエラー処理も簡単だからだ。
Completer is needed for two kinds of low-level code: new asynchronous
primitives, and interfacing with asynchronous code that doesn't use futures.
Most other code should use async/await or [`Future.then()`][then], because
they're clearer and make error handling easier.

[then]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Future/then.html

{:.good}
<?code-excerpt "usage_good.dart (avoid-completer)"?>
```dart
Future<bool> fileContainsBear(String path) {
  return File(path).readAsString().then((contents) {
    return contents.contains('bear');
  });
}
```

{:.good}
<?code-excerpt "usage_good.dart (avoid-completer-alt)"?>
```dart
Future<bool> fileContainsBear(String path) async {
  var contents = await File(path).readAsString();
  return contents.contains('bear');
}
```


### DO 型引数が `Object` である可能性のある `FutureOr<T>` を曖昧性解消する際に `Future<T>` をテストする。 DO test for `Future<T>` when disambiguating a `FutureOr<T>` whose type argument could be `Object`

FutureOr<T>`で何か便利なことをする前に、`Future<T>` か素の `T` かを確認するために、典型的には `is` チェックを必要とする。
型引数が `FutureOr<int>` のように特定の型である場合、`is int` か `is Future<int>` のどちらを使っても構わない。
この2つの型はばらばらになるので、どちらでも動作する。
Before you can do anything useful with a `FutureOr<T>`, you typically need to do
an `is` check to see if you have a `Future<T>` or a bare `T`. If the type
argument is some specific type as in `FutureOr<int>`, it doesn't matter which
test you use, `is int` or `is Future<int>`. Either works because those two types
are disjoint.

しかしながら、値の型が `Object` または `Object` でインスタンス化される可能性のある型パラメーターの場合、2つの分岐はオーバーラップする。
`Future<Object>` 自身は `Object` を実装しているので、`is Object` または `is T` (`T` は `Object` でインスタンス化できる何らかの型パラメータ) は、
オブジェクトが Future であっても true を返す。
それに代わって、`Future` を明示的にテストする場合：
However, if the value type is `Object` or a type parameter that could possibly
be instantiated with `Object`, then the two branches overlap. `Future<Object>`
itself implements `Object`, so `is Object` or `is T` where `T` is some type
parameter that could be instantiated with `Object` returns true even when the
object is a future. Instead, explicitly test for the `Future` case:

{:.good}
<?code-excerpt "usage_good.dart (test-future-or)"?>
```dart
Future<T> logValue<T>(FutureOr<T> value) async {
  if (value is Future<T>) {
    var result = await value;
    print(result);
    return result;
  } else {
    print(value);
    return value;
  }
}
```

{:.bad}
<?code-excerpt "usage_bad.dart (test-future-or)"?>
```dart
Future<T> logValue<T>(FutureOr<T> value) async {
  if (value is T) {
    print(value);
    return value;
  } else {
    var result = await value;
    print(result);
    return result;
  }
}
```

悪い例では、`Future<Object>`を渡すと、それをむきだしの同期的な値のように、間違って扱ってしまう。
In the bad example, if you pass it a `Future<Object>`, it incorrectly treats it
like a bare, synchronous value.

[pokemon]: https://blog.codinghorror.com/new-programming-jargon/
[Error]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Error-class.html
[StackOverflowError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/StackOverflowError-class.html
[OutOfMemoryError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/OutOfMemoryError-class.html
[ArgumentError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/ArgumentError-class.html
[AssertionError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/AssertionError-class.html
[Exception]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Exception-class.html

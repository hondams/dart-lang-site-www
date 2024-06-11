---
title: Effective Dart
description: >-
  Best practices for building consistent, maintainable,
  and efficient Dart libraries.
nextpage:
  url: /effective-dart/style
  title: Style
---

過去数年間、私たちは大量のDartコードを書き、何がうまく機能し、何がうまく機能しないかについて多くを学んできました。
これを皆さんと共有します。 そうすることで、あなたも 一貫性のある、堅牢で高速なコードを書くことができるようになります。包括的なテーマは2つあります：
Over the past several years, we've written a ton of Dart code and learned a lot
about what works well and what doesn't. We're sharing this with you so you can
write consistent, robust, fast code too. There are two overarching themes:


 1. **一貫性を保つ** フォーマットや大文字・小文字の使い分けのようなことに関しては、どちらが優れているかという議論は主観的なものであり、解決することは不可能だ。分かっているのは、**一貫性を保つ**ことは客観的に有用だということだ。
    **Be consistent.** When it comes to things like formatting, and casing,
    arguments about which is better are subjective and impossible to resolve.
    What we do know is that being *consistent* is objectively helpful.

    もし2つのコードが違って見えるなら、それは何か意味のある違いがあるからだ。あるコードが際立っていて、あなたの目を引くとき それは有益な理由によるものであるべきだ。
    If two pieces of code look different it should be because they *are*
    different in some meaningful way. When a bit of code stands out and catches
    your eye, it should do so for a useful reason.

 2. **簡潔にする** Dartは親しみやすいように設計されているため、C、Java、JavaScript、その他の言語と同じ文（statement）や式（expression）の多くを継承している。しかし、私たちがDartを作ったのは、これらの言語が提供するものには改善の余地がたくさんあるからだ。文字列補間（文字列リテラル内に埋め込まれたプレースホルダーを実行時評価）から初期化定型文まで、より単純で簡単に意図を表現できるように、我々は多くの機能を追加した。
    **Be brief.** Dart was designed to be familiar, so it inherits many of the
    same statements and expressions as C, Java, JavaScript and other languages.
    But we created Dart because there is a lot of room to improve on what those
    languages offer. We added a bunch of features, from string interpolation to
    initializing formals, to help you express your intent more simply and
    easily.

    複数の言い方がある場合、一般的には最も簡潔なものを選ぶべきである。これは、1行にプログラム全体を詰め込むような[code golf][]をしろと言っているのではない。プログラム全体を1行に詰め込むべきだということではありません。ゴールは
    *経済的な*コードであって、*密な*コードではない。
    If there are multiple ways to say something, you should generally pick the
    most concise one. This is not to say you should [code golf][] yourself into
    cramming a whole program into a single line. The goal is code that is
    *economical*, not *dense*.

[code golf]: https://en.wikipedia.org/wiki/Code_golf

## ガイド The guides

私たちは、ガイドラインを消化しやすいようにいくつかの別々のページに分けた：
We split the guidelines into a few separate pages for easy digestion:

  * **[スタイルガイド][]** &ndash; これは、レイアウトやコードの整理についてのルールを定義するもので、少なくとも[`dart format`][]が処理してくれない部分を定義するものだ。
    スタイルガイドでは、識別子の書式についても規定しています。
    `camelCase`, `using_underscores`, etc.
    **[Style Guide][]** &ndash; This defines the rules for laying out and
    organizing code, or at least the parts
    that [`dart format`][] doesn't handle for you. 
    The style guide also specifies how identifiers are formatted:
    `camelCase`, `using_underscores`, etc.

  * **[ドキュメントガイド][]** &ndash; コメントの中身について知っておくべきことは、これですべてわかる。
ドキュメント・コメントも、普通のありふれたコード・コメントも。
    **[Documentation Guide][]** &ndash; This tells you everything you need to
    know about what goes inside comments. Both doc comments and regular,
    run-of-the-mill code comments.

  * **[利用ガイド][]** &ndash; ここでは、動作を実装するために言語機能を最大限に活用する方法を学びます。
文（statement）や式（expression）の中であれば、ここでカバーされている。
    **[Usage Guide][]** &ndash; This teaches you how to make the best use of
    language features to implement behavior. If it's in a statement or
    expression, it's covered here.

  * **[設計ガイド][]** &ndash; これは最もソフトなガイドですが、最も広い範囲をカバーするものです。
ライブラリにとって、一貫性があり、使い勝手の良いAPIの設計について学んだことを網羅しています。
型シグネチャや宣言の中にあるものについては、このガイドで説明します。
    **[Design Guide][]** &ndash; This is the softest guide, but the one
    with the widest scope. It covers what we've learned about designing
    consistent, usable APIs for libraries. If it's in a type signature or
    declaration, this goes over it.

すべてのガイドラインへのリンクは
[まとめ](#summary-of-all-rules)
を参照。
For links to all the guidelines, see the
[summary](#summary-of-all-rules).

[`dart format`]: /tools/dart-format
[スタイルガイド]: /effective-dart/style
[style guide]: /effective-dart/style
[ドキュメントガイド]: /effective-dart/documentation
[documentation guide]: /effective-dart/documentation
[利用ガイド]: /effective-dart/usage
[usage guide]: /effective-dart/usage
[設計ガイド]: /effective-dart/design
[design guide]: /effective-dart/design

## ガイドの読み方 How to read the guides

各ガイドはいくつかのセクションに分かれている。セクションにはガイドラインのリストが含まれている。
各ガイドラインは、以下のいずれかの言葉で始まります：
Each guide is broken into a few sections. Sections contain a list of guidelines.
Each guideline starts with one of these words:

* **DO** ガイドラインには、常に従うべき慣行が記されている。
  そこから外れる正当な理由はほとんどないだろう。
  **DO** guidelines describe practices that should always be followed. There
  will almost never be a valid reason to stray from them.

* **DON'T** ガイドラインはその逆：ほとんど良いアイデアとは言えないものだ。
  願わくば、他の言語ほどこのようなものが多くないことを願っている。
  **DON'T** guidelines are the converse: things that are almost never a good
  idea. Hopefully, we don't have as many of these as other languages do because
  we have less historical baggage.

* **PREFER** ガイドラインは従う *べき* 慣行である。
  しかし、そうしない方が理にかなっている状況もあるかもしれない。
  ただ、ガイドラインを無視した場合の影響を十分に理解しておくこと。
  **PREFER** guidelines are practices that you *should* follow. However, there
  may be circumstances where it makes sense to do otherwise. Just make sure you
  understand the full implications of ignoring the guideline when you do.

* **AVOID** ガイドラインは "PREFER"と両輪である： 
  やってはいけないことだが、まれに正当な理由があるかもしれない。
  **AVOID** guidelines are the dual to "prefer": stuff you shouldn't do but
  where there may be good reasons to on rare occasions.

* **CONSIDER** ガイドラインとは、状況や前例、そしてあなた自身の好みに応じて、従うか従わないかを決めるものである。
  **CONSIDER** guidelines are practices that you might or might not want to
  follow, depending on circumstances, precedents, and your own preference.

ガイドラインの中には、そのルールが適用され*ない* **例外**が記載されているものもあります。
例外が列挙されている場合、その例外がすべてではないかもしれません。- 他のケースについても、あなたの判断が必要かもしれません。
Some guidelines describe an **exception** where the rule does *not* apply. When
listed, the exceptions may not be exhaustive—you might still need to use
your judgement on other cases.

これではまるで、靴紐を正しく結んでいないと警察がドアを叩き壊してしまうような話だ。
物事はそんなに悪くない。ここでのガイドラインのほとんどは常識的なものだし、私たちはみんな合理的な人間だ。
ゴールはいつも通り、読みやすくメンテナンスしやすいコードだ。
This sounds like the police are going to beat down your door if you don't have
your laces tied correctly. Things aren't that bad. Most of the guidelines here
are common sense and we're all reasonable people. The goal, as always, is nice,
readable and maintainable code.

Dart analyzerは、これらのガイドラインやその他のガイドラインに従った、優れた一貫性のあるコードを書くための linter を提供します。
ガイドラインに従うのに役立つ[linter ルール][lints]が1つ以上存在する場合、ガイドラインはそれらのルールにリンクします。
リンクの形式は以下の通りです：
The Dart analyzer provides a linter
to help you write good, consistent code
that follows these and other guidelines.
If one or more [linter rules][lints] exist
that can help you follow a guideline
then the guideline links to those rules.
The links use the following format:

{% include linter-rule-mention.md rule="unnecessary_getters_setters" %}

リンターの使い方については、[linter ルールの有効化][]と[linter ルール][lints]のリストを参照のこと。
To learn how to use the linter,
see [Enabling linter rules][]
and the list of [linter rules][lints].

[linter ルールの有効化]: /tools/analysis#enabling-linter-rules
[Enabling linter rules]: /tools/analysis#enabling-linter-rules
[lints]: /tools/linter-rules

## 用語集 Glossary

ガイドラインを簡潔なものにするため、ここではいくつかの略語を使用し、さまざまな Dart 構成要素 に言及します。
To keep the guidelines brief, we use a few shorthand terms to refer to different
Dart constructs.

* **ライブラリメンバ**とは、トップレベルのフィールド、ゲッター、セッター、関数のことです。
  基本的に、型でないトップ・レベルのものすべてを指します。
* A **library member** is a top-level field, getter, setter, or function.
  Basically, anything at the top level that isn't a type.

* **クラス・メンバ** は、クラス内で宣言されたコンストラクタ、フィールド、ゲッター、セッター、 関数、または演算子です。
  クラス・メンバは、インスタンスまたは静的、抽象または具象です。
* A **class member** is a constructor, field, getter, setter, function, or
  operator declared inside a class. Class members can be instance or static,
  abstract or concrete.

* **メンバ**はライブラリ・メンバでもクラス・メンバでもあります。
* A **member** is either a library member or a class member.

* **変数** は、一般的に使用される場合、トップレベル変数、パラメータ、ローカル変数を指します。
  静的フィールドやインスタンス・フィールドは含まれません。
* A **variable**, when used generally, refers to top-level variables,
  parameters, and local variables. It doesn't include static or instance fields.

* **型**は、任意の名前付き型宣言です。 : class, typedef, または enum
* A **type** is any named type declaration: a class, typedef, or enum.

* **プロパティ** は、トップレベル変数、ゲッター（クラス内部またはトップレベル、インスタンスまた静的）、セッター(同じ)、フィールド(インスタンスまたは静的)です。
  大まかには、"フィールドのような "名前付き構成要素です。
* A **property** is a top-level variable, getter (inside a class or at the top
  level, instance or static), setter (same), or field (instance or static).
  Roughly any "field-like" named construct.

## 全てのルールのまとめ Summary of all rules

{% include_relative _toc.md %}

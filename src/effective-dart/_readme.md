# Effective Dart writing guidelines

## TOC

ガイドラインを追加したり、ヘッダーを更新したりする場合は、
目次を再生成する必要があります。
[全ルールのまとめ][]を参照してください。
生成されたファイルをコミットする必要があります。
If you add a guideline or update the header of one,
you will need to regenerate the table of contents
found at [Summary of all rules][]
and commit the resulting generated file.

そのためには、リポジトリのルートディレクトリから以下を実行してください：
To do so, run the following from the repository root directory:

```terminal
$ dart run tool/effective_dart_rules/bin/main.dart
```

[全ルールのまとめ]: https://dart.dev/effective-dart#summary-of-all-rules
[Summary of all rules]: https://dart.dev/effective-dart#summary-of-all-rules

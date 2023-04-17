*原著バージョン: https://github.com/google/styleguide/blob/9ae38d43e38a5b182092723d0f1295c3b80b1e06/go/index.md*

# Go Style

https://google.github.io/styleguide/go

[Overview](index.ja.md) | [Style Guide](guide.ja.md) | [Style Decisions](decisions.ja.md) | [Best practices](best-practices.ja.md)

<a id="about"></a>

## About

Go スタイルガイドとそれに付随する文書は、読みやすく慣用的な Go を書くための現在の最善のアプローチを成文化したものです。
スタイルガイドの遵守は絶対的なものではありませんし、これらの文書は決して網羅的なものではありません。
私たちの意図は、読みやすい Go を書くための当て推量を最小限にし、Go を初めて使う人がよくある間違いを避けられるようにすることです。
また、このスタイル ガイドには、Google で Go のコードをレビューする人が与えるスタイル ガイドを統一する役割もあります。

文書            | リンク                                                  | 主な対象者    | [Normative] | [Canonical]
------------------- | ----------------------------------------------------- | ------------------- | ----------- | -----------
**Style Guide**     | [guide.ja.md](guide.ja.md)          | 全員            | Yes         | Yes
**Style Decisions** | [decisions.ja.md](decisions.ja.md)      | リーダビリティ・メンター | Yes         | No
**Best Practices**  | [best-practices.ja.md](best-practices.ja.md) | 興味のある方   | No          | No

[Normative]: #normative
[Canonical]: #canonical

<a id="docs"></a>

### Documents

1. **[Style Guide](guide.ja.md)**
   Google における Go スタイルの基礎となるものです。この文書は決定版であり [Style Decisions](decisions.ja.md) と [Best Practices](best-practices.ja.md) の提言のベースとなっています。

2. **[Style Decisions](decisions.ja.md)**
   特定のスタイルポイントに関する決定をまとめたより冗長なドキュメントで、適切な場合にはその判断の根拠を議論しています。
   これらの判断は、新しいデータ、新しい言語機能、新しいライブラリ、または新しいパターンに基づき、時折変更されることがありますが、それらの予定が無い場合でも、Google の各プログラマーは、このドキュメントの最新状況を常に把握しておくべきです。

3. **[Best Practices](best-practices.ja.md)**
    一般的な問題を解決し、読みやすく、コードのメンテナンスの必要性に強い、長い時間をかけて進化してきたパターンのいくつかを記録しています。
    これらのベストプラクティスは正規のものではありませんが、GoogleのGoプログラマーは、コードベースの統一と一貫性を保つために、可能な限りこれらのベストプラクティスを使用するよう奨励されています。

このドキュメントは、以下のことを目的としています:

* 代替スタイルを計量するための一連の原則に合意する
* Go スタイルに関する定型的な事柄を成文化
* Go のイディオムを文書化して正規の例を提供する
* 様々なスタイル決定の長所と短所を文書化する
* Goの可読性レビューで驚きを最小限に抑えるのに役立つ
* 読みやすさを追求するメンターは、一貫した用語とガイダンスを使用する

このドキュメントは、以下のことを目的として**いません**:

* 可読性レビューで与えられるコメントの網羅的なリストであること
* 誰もが覚えていて、常に守ることが期待されているルールをすべて列挙する
* 言語の特徴やスタイルの使用において、適切な判断に置き換える
* スタイルの違いを解消するために大規模な変更を正当化する

Goのプログラマーによって、またチームのコードベースによって、常に違いがあります。
しかし、私たちのコードベースができる限り一貫していることが、Google と Alphabet の最善の利益となるのです。
 (一貫性については [guide](guide.ja.md#consistency) を参照してください。)
そのため、スタイルガイドの違反を見つけるたびに、細かく指摘する必要はなく、自由にスタイルを改善してください。
特に、これらの文書は時間の経過とともに変更される可能性があり、それは既存のコードベースに余分な解約を引き起こす理由にはなりません。
そのため、最新のベストプラクティスを用いて新しいコードを書き、時間をかけて近くの問題に対処することで十分です。

スタイルの問題は本質的に個人的なものであり、常にトレードオフが内在していることを認識することが重要です。
このドキュメントのガイダンスの多くは主観的なものですが、`gofmt`と同じように、これらの文書が提供する統一性には大きな価値があるのです。
そのため、スタイルに関する推奨事項は、正当な理由なく変更されることはありません。Googleのプログラマーは、意見が異なる場合でもスタイルガイドに従うことが推奨されます。

<a id="definitions"></a>

## Definitions

スタイルドキュメントで使用される以下の単語は、以下のように定義されています：

* **Canonical**: 規定的なルールと永続的なルールを定める
    <a id="canonical"></a>

    これらの文書の中で、「canonical」は、すべてのコード（新旧）が従うべき標準とみなされるもので、時間の経過とともに大きく変化することが予想されないものを表すために使用されています。
    規範となる文書の原則は、著者とレビューアが同様に理解できるものでなければならないため、規範となる文書に含まれるすべてのものが高い水準を満たさなければなりません。
    そのため、canonical な文書は一般的に non-canonical の文書よりも短く、スタイルの要素も少なく規定されています。

    https://google.github.io/styleguide/go#canonical

* **Normative**: 一貫性の確立を意図したもの <a id="normative"></a>

    これらの文書の中で、「normative」とは、提案、用語、および正当性を一貫させるために、Goコードレビュアーが使用するスタイルの合意された要素であるものを説明するために使用されます。
    これらの要素は時間の経過とともに変更される可能性があり、これらの文書にはそのような変更が反映されるため、レビュアーは一貫した最新の状態を保つことができるようになります。
    Goコードの作者は、規範となる文書を熟知していることは期待されませんが、可読性レビューの際にレビュアーが参照することは頻繁にあります。

    https://google.github.io/styleguide/go#normative

* **Idiomatic**: 一般的で身近なもの <a id="idiomatic"></a>

    これらの文書では、「idiomatic」とは、Goのコードに普及しているもので、認識しやすい馴染みのあるパターンとなっているものを指す言葉として使用されています
    一般に、idiomatic なパターンは、文脈の中で同じ目的を果たすなら、non-idiomatic なものよりも優先されるべきです。

    https://google.github.io/styleguide/go#idiomatic

<a id="references"></a>

## Additional references

このガイドでは、読者が [Effective Go] に精通していることを前提としています。これは、Goコミュニティ全体でGoコードの共通ベースラインを提供するためです。

以下は、Goスタイルについて自己学習したい方と、レビューにリンク可能なコンテキストを提供したいレビュアーのための追加リソースです。
Goの可読性プロセスの参加者は、これらのリソースに精通していることは期待されていませんが、可読性レビューの文脈として発生する可能性があります。

[Effective Go]: https://go.dev/doc/effective_go

**External References**

* [Go Language Specification](https://go.dev/ref/spec)
* [Go FAQ](https://go.dev/doc/faq)
* [Go Memory Model](https://go.dev/ref/mem)
* [Go Data Structures](https://research.swtch.com/godata)
* [Go Interfaces](https://research.swtch.com/interfaces)
* [Go Proverbs](https://go-proverbs.github.io/)

* <a id="gotip"></a> Go Tip Episodes - stay tuned.

* <a id="unit-testing-practices"></a> Unit Testing Practices - stay tuned.

**Relevant Testing-on-the-Toilet articles**

* [TotT: Identifier Naming][tott-431]
* [TotT: Testing State vs. Testing Interactions][tott-281]
* [TotT: Effective Testing][tott-324]
* [TotT: Risk-driven Testing][tott-329]
* [TotT: Change-detector Tests Considered Harmful][tott-350]

[tott-431]: https://testing.googleblog.com/2017/10/code-health-identifiernamingpostforworl.html
[tott-281]: https://testing.googleblog.com/2013/03/testing-on-toilet-testing-state-vs.html
[tott-324]: https://testing.googleblog.com/2014/05/testing-on-toilet-effective-testing.html
[tott-329]: https://testing.googleblog.com/2014/05/testing-on-toilet-risk-driven-testing.html
[tott-350]: https://testing.googleblog.com/2015/01/testing-on-toilet-change-detector-tests.html

**Additional External Writings**

* [Go and Dogma](https://research.swtch.com/dogma)
* [Less is exponentially more](https://commandcenter.blogspot.com/2012/06/less-is-exponentially-more.html)
* [Esmerelda's Imagination](https://commandcenter.blogspot.com/2011/12/esmereldas-imagination.html)
* [Regular expressions for parsing](https://commandcenter.blogspot.com/2011/08/regular-expressions-in-lexing-and.html)
* [Gofmt's style is no one's favorite, yet Gofmt is everyone's favorite](https://www.youtube.com/watch?v=PAAkCSZUG1c&t=8m43s) (YouTube)

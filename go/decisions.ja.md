*原著バージョン: https://github.com/google/styleguide/blob/9ae38d43e38a5b182092723d0f1295c3b80b1e06/go/decisions.md*

# Go Style Decisions

https://google.github.io/styleguide/go/decisions

[Overview](index.ja.md) | [Style Guide](guide.ja.md) | [Style Decisions](decisions.ja.md) | [Best practices](best-practices.ja.md)

{% raw %}

**Note:** これは、Googleにおける [Go Style](index.ja.md) の概要を説明する一連のドキュメントの一部です。
この文書は **[normative](index.ja.md#normative) と [canonical](index.ja.md#canonical)** です。
詳しくは [the overview](index.ja.md#about) を参照してください。

<a id="about"></a>

## About

本書は、Goの可読性メンターによるアドバイスを統一し、標準的なガイダンス、説明、例を提供することを目的としたスタイル決定が含まれています。

この文書は **完全なものではありません**、時間の経過とともに増えていくものです。
[the core style guide](guide.ja.md) と本書のアドバイスが矛盾する場合は、**スタイル・ガイドが優先され**、本書はそれに応じて更新されるべきです。

Go Styleのドキュメント一式は [Overview](index.ja.md#about) をご覧ください。

以下のセクションは、スタイルの決定からガイドの別の部分に移動しています：

* **MixedCaps**: see [guide#mixed-caps](guide.ja.md#mixed-caps)
    <a id="mixed-caps"></a>

* **Formatting**: see [guide#formatting](guide.ja.md#formatting)
    <a id="formatting"></a>

* **Line Length**: see [guide#line-length](guide.ja.md#line-length)
    <a id="line-length"></a>

<a id="naming"></a>

## Naming

ネーミングに関する包括的なガイダンスについては、[the core style guide](guide.ja.md#naming) のネーミングセクションを参照してください。
次のセクションでは、ネーミングの特定の領域についてさらに詳しく説明しています。

<a id="underscores"></a>

### Underscores

Go の名前は、一般的にアンダースコアを含んではいけません。
この原則には3つの例外があります：

1. 生成されたコードによってのみインポートされるパッケージ名には、アンダースコアが含まれる場合があります。
   複数単語のパッケージ名の選び方についての詳細は、[package names](#package-names) を参照してください。
2. `*_test.go` ファイル内の Test、Benchmark、Example 関数名にはアンダースコアが含まれる場合があります。
3. オペレーティングシステムや cgo と相互運用する低レベルのライブラリは、[`syscall`] で行われているように、識別子を再利用することができます。これは、ほとんどのコードベースでは非常にまれであると予想されます。

[`syscall`]: https://pkg.go.dev/syscall#pkg-constants

<a id="package-names"></a>

### Package names

<a id="TOC-PackageNames"></a>

Go のパッケージ名は短く、小文字のみを使用します。
複数の単語からなるパッケージ名は、すべて小文字で切れ目を入れないようにします。
例えば、パッケージ [`tabwriter`] は `tabWriter` や `TabWriter`, `tab_writer` という名前ではありません。

よく使われるローカル変数名によって [shadowed] になってしまうようなパッケージ名は選ばないようにしましょう。
例えば、`count` はよく使われる変数名なので、`usercount` は `count` より良いパッケージ名です。

Go のパッケージ名にはアンダースコアが付いてはいけません。
名前にアンダースコアが含まれているパッケージをインポートする必要がある場合（通常は生成されたコードやサードパーティのコードから）、インポート時に Go コードで使用するのに適した名前に変更する必要があります。

ただし、生成されたコードによってのみインポートされるパッケージ名には、アンダースコアが含まれる場合があります。
具体的な例としては、以下のようなものがあります：

* 統合テストなどの外部テストパッケージに `_test` という接尾辞を使用する。
* [package-level documentation examples](https://go.dev/blog/examples) (パッケージレベルのドキュメントの例) に `_test` というサフィックスを使用する。

[`tabwriter`]: https://pkg.go.dev/text/tabwriter
[shadowed]: best-practices.ja.md#shadowing

`util`、`utility`、`common`、`helper` などのような情報量の少ないパッケージ名は避けてください。
いわゆる ["utility packages"](best-practices.ja.md#util-packages) についてもっと詳しく見てください。

インポートされたパッケージの名前が変更された場合 (例: `import foopb "path/to/foo_go_proto"`) 、パッケージのローカル名は上記のルールに従わなければなりません。
ローカル名はパッケージ内のシンボルがファイル内でどのように参照されるかを決定します。
あるインポートが複数のファイル、特に同じパッケージや近くのパッケージで名前が変更された場合、一貫性を保つために可能な限り同じローカル名を使用する必要があります。

<!--#include file="/go/g3doc/style/includes/special-name-exception.md"-->

こちらもご参照ください： [パッケージ名に関するGoブログ記事](https://go.dev/blog/package-names)

<a id="receiver-names"></a>

### Receiver names

<a id="TOC-ReceiverNames"></a>

[Receiver] (レシーバー) 名は必ず：

* 短い（通常1～2文字程度の長さのもの）
* タイプ自体の略称
* そのタイプのすべてのレシーバーに一貫して適用される

Long Name                   | Better Name
--------------------------- | -------------------------
`func (tray Tray)`          | `func (t Tray)`
`func (info *ResearchInfo)` | `func (ri *ResearchInfo)`
`func (this *ReportWriter)` | `func (w *ReportWriter)`
`func (self *Scanner)`      | `func (s *Scanner)`

[Receiver]: https://golang.org/ref/spec#Method_declarations

<a id="constant-names"></a>

### Constant names

定数名は、Goの他の名前と同様に [MixedCaps] を使用する必要があります。
([Exported] 定数は大文字で始まり、unexported 定数は小文字で始まります)。
これは、他の言語での慣例に反する場合でも適用されます。
定数名は、その値の派生物であってはならず、代わりに、その値が何を示すかを説明する必要があります。

```go
// Good:
const MaxPacketSize = 512

const (
    ExecuteBit = 1 << iota
    WriteBit
    ReadBit
)
```

[MixedCaps]: guide.ja.md#mixed-caps
[Exported]: https://tour.golang.org/basics/3

MixedCaps でない定数名や接頭辞が `K` の定数は使用しないでください。

```go
// Bad:
const MAX_PACKET_SIZE = 512
const kMaxBufferSize = 1024
const KMaxUsersPergroup = 500
```

定数には、値ではなく、役割に基づいて名前を付けます。
もし定数がその値とは別に役割を持たないのであれば、定数として定義する必要はありません。

```go
// Bad:
const Twelve = 12

const (
    UserNameColumn = "username"
    GroupColumn    = "group"
)
```

<!--#include file="/go/g3doc/style/includes/special-name-exception.md"-->

<a id="initialisms"></a>

### Initialisms

<a id="TOC-Initialisms"></a>

イニシャリズムや頭字語である名前の単語（例：`URL` と `NATO`）は同じ大文字と小文字を使用する必要があります。
`URL` は `URL` または `url` (`urlPony` や `URLPony` のように) と表記し、決して `Url` と表記してはいけません。
これは、`ID` が「識別子」の略である場合にも当てはまり、`appId` の代わりに `appID` と記述します。

* 複数の頭文字を持つ名前（例：`XML` と `API` を含むので `XMLAPI` ）では、与えられた頭文字内の各文字は同じケースを持つべきですが、名前内の各頭文字は同じケースである必要はありません。
* 小文字の頭文字を含む名前(例: `DDoS`、`iOS`、`gRPC`)では、[exportedness] のために最初の文字を変更する必要がない限り、頭文字は通常の散文と同じように表示すべきです。
  このような場合は、頭文字全体を同じケースにする必要があります（例：`ddos`、`IOS`、`GRPC`）。

[exportedness]: https://golang.org/ref/spec#Exported_identifiers

<!-- Keep this table narrow. If it must grow wider, replace with a list. -->

Initialism(s) | Scope      | Correct  | Incorrect
------------- | ---------- | -------- | --------------------------------------
XML API       | Exported   | `XMLAPI` | `XmlApi`, `XMLApi`, `XmlAPI`, `XMLapi`
XML API       | Unexported | `xmlAPI` | `xmlapi`, `xmlApi`
iOS           | Exported   | `IOS`    | `Ios`, `IoS`
iOS           | Unexported | `iOS`    | `ios`
gRPC          | Exported   | `GRPC`   | `Grpc`
gRPC          | Unexported | `gRPC`   | `grpc`
DDoS          | Exported   | `DDoS`   | `DDOS`, `Ddos`
DDoS          | Unexported | `ddos`   | `dDoS`, `dDOS`

<!--#include file="/go/g3doc/style/includes/special-name-exception.md"-->

<a id="getters"></a>

### Getters

<a id="TOC-Getters"></a>

関数やメソッドの名前には、基本的な概念で "get" という単語が使われていない限り、接頭辞として `Get` や `get` を使用すべきではありません（例：HTTP GET）。
例えば、`GetCounts` よりも `Counts` を使用するように、名詞を直接名前にすることをお勧めします。

もしその関数が複雑な計算を行ったり、リモートコールを実行したりする場合は、`Get` の代わりに `Compute` や `Fetch` といった別の単語を使い、その関数呼び出しに時間がかかり、ブロックや失敗する可能性があることを読者に明らかにすることができます。

<!--#include file="/go/g3doc/style/includes/special-name-exception.md"-->

<a id="variable-names"></a>

### Variable names

<a id="TOC-VariableNames"></a>

一般的な経験則では、名前の長さはそのスコープの大きさに比例し、そのスコープ内で使用される回数に反比例するはずです。
ファイルスコープで作成される変数には複数の単語が必要ですが、単一の内部ブロックにスコープされる変数は、コードを明確に保ち、余計な情報を避けるために、1単語、あるいは1文字か2文字で済むかもしれません。

ここでは、大まかな基準値を示します。
この数値ガイドラインは厳密なルールではありません。
文脈、[clarity]、[concision] に基づいて判断してください。

* 小さなスコープとは、1～7行と言った小さな操作を1～2回行うものです。
* 中程度のスコープでは、8～15行程度の小さな操作を数回、または大きな操作を1回行います。
* 大きなスコープは、1つまたはいくつかの大きな操作、例えば15-25行です。
* 非常に大きなスコープは、1ページ以上に及ぶもの（例えば、25行以上）です。

[clarity]: guide#clarity
[concision]: guide#concision

小さなスコープでは完全に明確な名前（たとえばカウンターを表す `c`）でも、大きなスコープでは不十分で、コードのさらに先で読者にその目的を思い出させるために明確な説明が必要になることがあります。
多くの変数が存在するスコープや、似たような値や概念を表す変数では、スコープが示すよりも長い変数名が必要になることがあります。

コンセプトの特異性は、変数名を簡潔に保つのに役立つこともあります。
例えば、使用するデータベースが1つだけだとすると、`db` のような短い変数名は、通常、非常に小さなスコープに対して予約されるかもしれませんが、スコープが非常に大きくても完全に明確なままです。
この場合、スコープの大きさによっては `database` という1つの単語でもよいかもしれませんが、`db` は他の解釈がほとんどない非常に一般的な単語の短縮形であるため、必須ではありません。

ローカル変数の名前は、その値がどこで生まれたかよりも、その変数が何を含み、現在のコンテキストでどのように使用されているかを反映する必要があります。
例えば、最適なローカル変数名が、構造体やプロトコルバッファのフィールド名と同じでないことはよくあることです。

一般的には：

* `count`や`options`のような一語の名前は、良い出発点です
* 例えば、`userCount`と`projectCount`のように、似たような名前を曖昧にするために追加の単語を追加することができます。
* タイピングの手間を省くために、単に文字を落とすことはしないでください。例えば、特にエクスポートされた名前では `Sbx` よりも `Sandbox` が好まれます。
* ほとんどの変数名から[types and type-like words] (型と型に似た言葉) を省略します。
  * 数値の場合は、`numUsers`や`usersInt`よりも`userCount`の方が良い名前です。
  * スライスの場合、`userSlice`よりも`users`の方が良い名前です。
  * 例えば、入力が `ageString` に格納され、解析された値には `age` を使用するような場合、スコープ内に2つのバージョンの値が存在するため、タイプライクな修飾子を含めることができます。
* [surrounding context] (周囲の文脈) から明らかな単語は省略します。例えば、`UserCount`メソッドの実装において、`userCount`というローカル変数はおそらく冗長で、`count`、`users`、あるいは`c`でも同じように読めます。

[types and type-like words]: #repetitive-with-type
[surrounding context]: #repetitive-in-context

<a id="v"></a>

#### Single-letter variable names

一文字の変数名は、[repetition](#repetition) を最小限に抑えるのに便利なツールですが、コードを不必要に不透明にする可能性もあります。
完全な単語が明らかで、1文字の変数の代わりにその単語が現れると繰り返しになるような場合に、その使用を制限してください。

一般的には：

* [method receiver variable] の場合、1文字または2文字の名前が好ましい。
* 一般的なタイプに馴染みのある変数名を使用することは、よく役立ちます：
  * `r` は `io.Reader` または `*http.Request` を表します。
  * `w` は `io.Writer` または `http.ResponseWriter` を表します。
* 特にインデックス（`i`など）や座標（`x`や`y`など）は、整数ループ変数として一文字の識別子が許容されます。
* スコープが短い場合、省略形でもループの識別が可能です。例えば、 `for _, n := range nodes { ... }`。

[method receiver variable]: #receiver-names

<a id="repetition"></a>

### Repetition

Goのソースコードの一部では、不必要な繰り返しを避ける必要があります。
よくあるのが名前の繰り返しで、不要な単語が含まれていたり、繰り返しになっていたりします。
その文脈や種類によって異なります。
また、同一または類似のコードセグメントが近接して複数回現れると、コード自体が不必要に反復されることがあります。

繰り返しの命名には、さまざまな形があります：

<a id="repetitive-with-package"></a>

#### Package vs. exported symbol name

エクスポートされたシンボルに名前を付ける場合、パッケージの名前は常にパッケージの外から見えるので、両者の間の冗長な情報は減らすか排除する必要があります。
パッケージが1つの型だけをエクスポートし、その型にパッケージ自体の名前が付けられている場合、コンストラクタが必要であれば、その正規の名前は `New` となります。

> **Examples:** Repetitive Name -> Better Name
>
> *   `widget.NewWidget` -> `widget.New`
> *   `widget.NewWidgetWithName` -> `widget.NewWithName`
> *   `db.LoadFromDatabase` -> `db.Load`
> *   `goatteleportutil.CountGoatsTeleported` -> `gtutil.CountGoatsTeleported`
>     or `goatteleport.Count`
> *   `myteampb.MyTeamMethodRequest` -> `mtpb.MyTeamMethodRequest` or
>     `myteampb.MethodRequest`

<a id="repetitive-with-type"></a>

#### Variable name vs. type

コンパイラは常に変数の型を把握しており、ほとんどの場合、その変数の使われ方によって、読者にも変数の型がわかるようになっています。
変数の型を明確にする必要があるのは、その値が同じスコープに2回出現する場合だけです。

Repetitive Name               | Better Name
----------------------------- | ----------------------
`var numUsers int`            | `var users int`
`var nameString string`       | `var name string`
`var primaryProject *Project` | `var primary *Project`

値が複数の形で現れる場合、`raw` や `parsed` のような余分な単語、または基礎となる表現で明確にすることができます：

```go
// Good:
limitStr := r.FormValue("limit")
limit, err := strconv.Atoi(limitStr)
```

```go
// Good:
limitRaw := r.FormValue("limit")
limit, err := strconv.Atoi(limitRaw)
```

<a id="repetitive-in-context"></a>

#### External context vs. local names

名前に周囲の文脈からの情報を含めると、多くの場合、利点のない余計なノイズが発生します。
パッケージ名、メソッド名、型名、関数名、インポートパス、そしてファイル名さえも、すべてのコンテキストを提供し、その中のすべての名前を自動的に修飾することができます。

```go
// Bad:
// In package "ads/targeting/revenue/reporting"
type AdsTargetingRevenueReport struct{}

func (p *Project) ProjectName() string
```

```go
// Good:
// In package "ads/targeting/revenue/reporting"
type Report struct{}

func (p *Project) Name() string
```

```go
// Bad:
// In package "sqldb"
type DBConnection struct{}
```

```go
// Good:
// In package "sqldb"
type Connection struct{}
```

```go
// Bad:
// In package "ads/targeting"
func Process(in *pb.FooProto) *Report {
    adsTargetingID := in.GetAdsTargetingID()
}
```

```go
// Good:
// In package "ads/targeting"
func Process(in *pb.FooProto) *Report {
    id := in.GetAdsTargetingID()
}
```

繰り返しは一般に、単独で評価するのではなく、その記号の使用者の文脈で評価されるべきです。
例えば、次のコードには、ある状況では問題なくても、文脈上では冗長な名前がたくさんあります：

```go
// Bad:
func (db *DB) UserCount() (userCount int, err error) {
    var userCountInt64 int64
    if dbLoadError := db.LoadFromDatabase("count(distinct users)", &userCountInt64); dbLoadError != nil {
        return 0, fmt.Errorf("failed to load user count: %s", dbLoadError)
    }
    userCount = int(userCountInt64)
    return userCount, nil
}
```

Instead, information about names that are clear from context or usage can often
be omitted:

```go
// Good:
func (db *DB) UserCount() (int, error) {
    var count int64
    if err := db.Load("count(distinct users)", &count); err != nil {
        return 0, fmt.Errorf("failed to load user count: %s", err)
    }
    return int(count), nil
}
```

<a id="commentary"></a>

## Commentary

コメントに関する規約（何をコメントするか、どのようなスタイルを使うか、実行可能な例をどのように提供するか、など）は、公開APIのドキュメントを読む経験をサポートすることを目的としています。
詳しくは [Effective Go](http://golang.org/doc/effective_go.html#commentary) を参照してください。

ベストプラクティス文書の[documentation conventions] (ドキュメント規約) の項では、この点についてさらに詳しく説明しています。

**Best Practice:** 開発中やコードレビュー中に [doc preview] を使用して、ドキュメントや実行可能な例が有用であるか、期待通りに表示されているかどうかを確認します。

**Tip:** リストとコードスニペットは、通常、行間を空けるためにインデントする必要があります。インデントを除けば、装飾は一般に避けるべきでしょう。

[doc preview]: best-practices#documentation-preview
[documentation conventions]:  best-practices#documentation-conventions

<a id="comment-line-length"></a>

### Comment line length

小さな画面でもソースからコメントが読み取れるようにしましょう。

コメントが長くなりすぎた場合は、複数の1行コメントにまとめることをお勧めします。
可能であれば、80カラム幅の端末でうまく読めるコメントを目指してください。しかし、これはハードカットではありません。
例えば、標準ライブラリでは、句読点に基づいてコメントを分割することが多く、個々の行が60-70文字のマークに近い状態になることがあります。

既存のコードには、コメントの長さが80文字を超えるものがたくさんあります。
このガイドラインは、可読性レビューの一環としてそのようなコードを変更する正当な理由として使用されるべきではありません（[consistency](guide.ja.md#consistency)を参照してください）。
このガイドラインの主な目的は、Goの可読性メンターが、いつ、どのような場合でも、同じ推奨をすることを確実にすることです。

解説については、こちらの[post from The Go Blog on documentation] (ドキュメントに関するGo Blogの記事) をご覧ください。

```text
# Good:
// This is a comment paragraph.
// The length of individual lines doesn't matter in Godoc;
// but the choice of wrapping makes it easy to read on narrow screens.
//
// Don't worry too much about the long URL:
// https://supercalifragilisticexpialidocious.example.com:8080/Animalia/Chordata/Mammalia/Rodentia/Geomyoidea/Geomyidae/
//
// Similarly, if you have other information that is made awkward
// by too many line breaks, use your judgment and include a long line
// if it helps rather than hinders.
```

小さな画面で何度も折り返すようなコメントは、読者体験が悪くなるので避けましょう。

```text
# Bad:
// This is a comment paragraph. The length of individual lines doesn't matter in
Godoc;
// but the choice of wrapping causes jagged lines on narrow screens or in code
review,
// which can be annoying, especially when in a comment block that will wrap
repeatedly.
//
// Don't worry too much about the long URL:
// https://supercalifragilisticexpialidocious.example.com:8080/Animalia/Chordata/Mammalia/Rodentia/Geomyoidea/Geomyidae/
```

<a id="doc-comments"></a>

### Doc comments

<a id="TOC-DocComments"></a>

すべてのトップレベルのエクスポートされた名前にはdocコメントが必要であり、明らかに動作や意味が不明な未エクスポートの型や関数宣言も同様です。
これらのコメントは、説明されるオブジェクトの名前で始まる [full sentences] (完全な文) であるべきです。
より自然に読めるように、名前の前に冠詞（"a"、"an"、"the"）を付けることができます。

```go
// Good:
// A Request represents a request to run a command.
type Request struct { ...

// Encode writes the JSON encoding of req to w.
func Encode(w io.Writer, req *Request) { ...
```

Docコメントは [Godoc](https://pkg.go.dev/) に表示され、IDEによって表面化されるため、パッケージを使う人のために書いておく必要があります。

[full sentences]: #comment-sentences

文書コメントは、以下のシンボル、または構造体の中に出現する場合はフィールドのグループに適用されます。

```go
// Good:
// Options configure the group management service.
type Options struct {
    // General setup:
    Name  string
    Group *FooGroup

    // Dependencies:
    DB *sql.DB

    // Customization:
    LargeGroupThreshold int // optional; default: 10
    MinimumMembers      int // optional; default: 2
}
```

**Best Practice:** 未エクスポートのコードにdocコメントがある場合は、エクスポートした場合と同じ習慣に従ってください（すなわち、未エクスポートの名前でコメントを開始する）。これにより、コメントとコードの両方で、エクスポートされていない名前を新しくエクスポートされた名前に置き換えるだけで、後で簡単にエクスポートすることができます。

<a id="comment-sentences"></a>

### Comment sentences

<a id="TOC-CommentSentences"></a>

完全な文であるコメントは、標準的な英語の文と同様に大文字と句読点を使用する必要があります。
(例外として、識別子の名前が明確であれば、文頭に大文字でない識別子を使ってもかまいません。このようなケースは、おそらく段落の最初にのみ行うのがベストでしょう)。

文章の断片であるコメントには、句読点や大文字小文字の指定はありません。

[Documentation comments] (文書コメント) は常に完全な文であるべきであり、そのため常に大文字と句読点を使用する必要があります。
単純な行末コメント（特に構造体フィールドの場合）は、フィールド名を主語とする単純なフレーズにすることができます。

```go
// Good:
// A Server handles serving quotes from the collected works of Shakespeare.
type Server struct {
    // BaseDir points to the base directory under which Shakespeare's works are stored.
    //
    // The directory structure is expected to be the following:
    //   {BaseDir}/manifest.json
    //   {BaseDir}/{name}/{name}-part{number}.txt
    BaseDir string

    WelcomeMessage  string // displayed when user logs in
    ProtocolVersion string // checked against incoming requests
    PageLength      int    // lines per page when printing (optional; default: 20)
}
```

[Documentation comments]: #doc-comments

<a id="examples"></a>

### Examples

<a id="TOC-Examples"></a>

パッケージは、その意図する使い方を明確に文書化する必要があります。
[runnable example] (実行可能な例) を提供するようにしましょう; 例はGodocで表示されます。
実行可能な例は、本番のソースファイルではなく、テストファイルに属します。
この例を見てください ([Godoc], [source])。

[runnable example]: http://blog.golang.org/examples
[Godoc]: https://pkg.go.dev/time#example-Duration
[source]: https://cs.opensource.google/go/go/+/HEAD:src/time/example_test.go

実行可能な例を提供することが不可能な場合は、コード・コメント内で例コードを提供することができます。コメント内の他のコードやコマンドラインのスニペットと同様に、標準的な書式規則に従わなければなりません。

<a id="named-result-parameters"></a>

### Named result parameters

<a id="TOC-NamedResultParameters"></a>

パラメータに名前をつけるときは、Godocで関数のシグネチャがどのように表示されるかを考えてください。
関数自体の名前と結果のパラメーターの型は、しばしば十分に明確です。

```go
// Good:
func (n *Node) Parent1() *Node
func (n *Node) Parent2() (*Node, error)
```

関数が同じ型の2つ以上のパラメータを返す場合、名前を追加することが有効です。

```go
// Good:
func (n *Node) Children() (left, right *Node, err error)
```

呼び出し側が特定の結果パラメータに対してアクションを起こす必要がある場合、その名前を付けることでアクションの内容を示唆することができます：

```go
// Good:
// WithTimeout returns a context that will be canceled no later than d duration
// from now.
//
// The caller must arrange for the returned cancel function to be called when
// the context is no longer needed to prevent a resource leak.
func WithTimeout(parent Context, d time.Duration) (ctx Context, cancel func())
```

上のコードでは、キャンセルとは呼び出し側が取るべき特定の動作のことです。
しかし、結果パラメータを `(Context, func())` とだけ書くと、「キャンセル関数」が何を意味するのかが不明になってしまいます。

名前付き結果パラメータを使用しないでください。

[unnecessary repetition](#repetitive-with-type).

```go
// Bad:
func (n *Node) Parent1() (node *Node)
func (n *Node) Parent2() (node *Node, err error)
```

関数内部での変数宣言を避けるために、結果パラメータに名前をつけないようにしましょう。
この習慣は、実装を簡潔にする代償として、不必要なAPIの冗長性をもたらします。

[Naked returns]が許されるのは、小さな関数の中だけです。
中規模の関数になったら、戻り値を明示するようにしましょう。同様に、ネイキッドリターンを使えるようになるからといって、結果パラメータに名前をつけないようにしましょう。
[Clarity](guide.ja.md#clarity) は、関数内で数行節約することよりも常に重要です。

遅延クロージャで値を変更する必要がある場合は、結果パラメータに名前を付けることが常に許容されます。

> **Tip:** 関数のシグネチャでは、名前よりも型の方が明確な場合が多いです。
> [GoTip #38: Functions as Named Types] では、このことを説明しています。
>
> 上記の[`WithTimeout`]では、実際のコードでは結果のパラメータリストに生の`func()`ではなく[`CancelFunc`]を使用しているため、ドキュメントを作成する労力はほとんどありません。

[Naked returns]: https://tour.golang.org/basics/7
[GoTip #38: Functions as Named Types]: https://google.github.io/styleguide/go/index.html#gotip
[`WithTimeout`]: https://pkg.go.dev/context#WithTimeout
[`CancelFunc`]: https://pkg.go.dev/context#CancelFunc

<a id="package-comments"></a>

### Package comments

<a id="TOC-PackageComments"></a>

パッケージコメントは、パッケージ条項のすぐ上に表示され、コメントとパッケージ名の間に空白行がないようにしなければいけません。

例:

```go
// Good:
// Package math provides basic constants and mathematical functions.
//
// This package does not guarantee bit-identical results across architectures.
package math
```

パッケージコメントは、1つのパッケージにつき1つでなければなりません。
パッケージが複数のファイルで構成されている場合、そのうちのちょうど1つのファイルがパッケージコメントを持つ必要があります。

`main` パッケージのコメントは、BUILDファイル内の `go_binary` ルールの名前がパッケージ名の代わりになる、少し変わった形式をとります。

```go
// Good:
// The seed_generator command is a utility that generates a Finch seed file
// from a set of JSON study configs.
package main
```

バイナリ名が BUILD ファイルに書かれている通りであれば、他のスタイルのコメントでもかまいません。
バイナリ名が最初の単語である場合、コマンドライン呼び出しのスペルと厳密に一致しなくても、大文字にする必要があります。

```go
// Good:
// Binary seed_generator ...
// Command seed_generator ...
// Program seed_generator ...
// The seed_generator command ...
// The seed_generator program ...
// Seed_generator ...
```

Tips:

* コマンドラインの呼び出し例やAPIの使用方法は、有用なドキュメントとなります。
  Godocフォーマットの場合、コードを含むコメント行はインデントしてください。

* 明らかなプライマリファイルがない場合や、パッケージコメントが異常に長い場合は、コメントとパッケージ条項のみを記述した`doc.go`という名前のファイルにdocコメントを入れてもよいでしょう。

* 複数の1行コメントの代わりに、複数行コメントを使用することができます。
  これは、バイナリのコマンドライン例やテンプレートの例など、ソースファイルからコピー＆ペーストすると便利な部分がドキュメントに含まれている場合に主に有効です。

    ```go
    // Good:
    /*
    The seed_generator command is a utility that generates a Finch seed file
    from a set of JSON study configs.

        seed_generator *.json | base64 > finch-seed.base64
    */
    package template
    ```

* メンテナ向けのコメントで、ファイル全体に適用されるものは、通常 import 宣言の後に置かれます。これらはGodocでは表面化されず、上記のパッケージ・コメントに関するルールの対象にはなりません。

<a id="imports"></a>

## Imports

<a id="TOC-Imports"></a>

<a id="import-renaming"></a>

### Import renaming

Importは、他のImportとの名前の衝突を避けるためにのみ名前を変更すべきです。
(このことは、[good package names](#package-names) (良いパッケージ名) は名前を変更する必要がないことを意味します)。
名前が衝突した場合、最もローカルな、あるいはプロジェクト固有のインポートの名前を変更することをお勧めします。
パッケージのローカル名(エイリアス)は、アンダースコアと大文字の使用禁止を含め、[the guidance around package naming](#package-names) (パッケージの命名に関するガイダンス) に従わなければなりません。

生成されたプロトコルバッファパッケージは、名前からアンダースコアを取り除くために名前を変更する必要があり、そのエイリアスは `pb` という接尾辞を持つ必要があります。詳しくは [proto and stub best practices] を参照してください。

[proto and stub best practices]: best-practices#import-protos

```go
// Good:
import (
    fspb "path/to/package/foo_service_go_proto"
)
```

有用な識別情報を持たないパッケージ名（例：`package v1`）を持つインポートは、以前のパスコンポーネントを含むように名前を変更する必要があります。
リネームは、同じパッケージをインポートする他のローカルファイルと一致させなければならず、バージョン番号を含めることもできます。

**Note:** パッケージ名を [good package names](#package-names) (良いパッケージ名) に合わせて変更することが望ましいのですが、ベンダーのディレクトリのパッケージでは実現不可能な場合が多いです。

```go
// Good:
import (
    core "github.com/kubernetes/api/core/v1"
    meta "github.com/kubernetes/apimachinery/pkg/apis/meta/v1beta1"
)
```

もし、使用したい共通のローカル変数名（例：`url`、`ssh`）と名前が衝突するパッケージをインポートする必要があり、パッケージ名を変更したい場合は、`pkg`サフィックス（例：`urlpkg`）を使って行うことが好ましい方法です。
パッケージの名前をローカル変数でシャドウすることも可能です。

<a id="import-grouping"></a>

### Import grouping

Imports は2つのグループに分けて整理してください：

* 標準ライブラリパッケージ
* その他（プロジェクト・ベンダー）パッケージ

```go
// Good:
package main

import (
    "fmt"
    "hash/adler32"
    "os"

    "github.com/dsnet/compress/flate"
    "golang.org/x/text/encoding"
    "google.golang.org/protobuf/proto"
    foopb "myproj/foo/proto/proto"
    _ "myproj/rpc/protocols/dial"
    _ "myproj/security/auth/authhooks"
)
```

例えば、リネーム、imported-only-for-side-effects、その他の特別なインポートグループを別のグループにしたい場合など、プロジェクトパッケージを複数のグループに分割することは問題ありません。

```go
// Good:
package main

import (
    "fmt"
    "hash/adler32"
    "os"


    "github.com/dsnet/compress/flate"
    "golang.org/x/text/encoding"
    "google.golang.org/protobuf/proto"

    foopb "myproj/foo/proto/proto"

    _ "myproj/rpc/protocols/dial"
    _ "myproj/security/auth/authhooks"
)
```

**Note:** オプションのグループを維持すること、つまり標準ライブラリと Google インポートの間の必須の分離のために必要なものを超えて分割することは、[goimports]　ツールでサポートされていません。
追加のインポートサブグループは、適合した状態で維持するために、著者とレビュアーの両方の側で注意を払う必要があります。

[goimports]: golang.org/x/tools/cmd/goimports

AppEngine アプリでもある Google プログラムは、AppEngine インポート用に別のグループを用意してください。

Gofmt は、インポートパスによって各グループをソートするように配慮しています。
しかし、インポートを自動的にグループに分けてくれるわけではありません。
[goimports] は、Gofmt とインポート管理を組み合わせたツールで、上記の決定に基づいてインポートをグループに分けてくれます。
[goimports] にインポートの配置を完全に管理させることも可能ですが、ファイルが改訂された場合、そのインポートリストは内部的に一貫性を保たなければなりません。

<a id="import-blank"></a>

### Import "blank" (`import _`)

<a id="TOC-ImportBlank"></a>

副作用のためだけにインポートされるパッケージ（`import _ "package"`という構文を使用）は、メインパッケージ、またはそれらを必要とするテストにおいてのみインポートすることができます。

このようなパッケージの例としては、以下のようなものがあります：

* [time/tzdata](https://pkg.go.dev/time/tzdata)
* [image/jpeg](https://pkg.go.dev/image/jpeg) in image processing code

ライブラリが間接的に依存している場合でも、ライブラリパッケージの空白のインポートは避けてください。
副作用のあるインポートをメインパッケージに拘束することで、依存関係を制御し、競合や無駄なビルドコストなしに別のインポートに依存するテストを書くことができるようになります。

ただし、以下の場合は例外とします：

* [nogo static checker]で禁止されているインポートのチェックを回避するために、ブランクインポートを使用することができます。
* コンパイラ指令 `//go:embed` を使用するソースファイルでは、[embed](https://pkg.go.dev/embed) パッケージの空インポートを使用することができます。

**Tip:** プロダクションで副作用のあるインポートに間接的に依存するライブラリパッケージを作成する場合、意図した使用方法を文書化してください。

[nogo static checker]: https://github.com/bazelbuild/rules_go/blob/master/go/nogo.rst

<a id="import-dot"></a>

### Import "dot" (`import .`)

<a id="TOC-ImportDot"></a>

`import .` 形式は、他のパッケージからエクスポートされた識別子を、現在のパッケージに無条件で持ち込むことを可能にする言語機能です。
詳しくは [language spec](https://go.dev/ref/spec#Import_declarations) を参照してください。

Google のコードベースでは、この機能を**使わないでください**; どこから来た機能なのかがわかりにくくなります。

```go
// Bad:
package foo_test

import (
    "bar/testutil" // also imports "foo"
    . "foo"
)

var myThing = Bar() // Bar defined in package foo; no qualification needed.
```

```go
// Good:
package foo_test

import (
    "bar/testutil" // also imports "foo"
    "foo"
)

var myThing = foo.Bar()
```

<a id="errors"></a>

## Errors

<a id="returning-errors"></a>

### Returning errors

<a id="TOC-ReturningErrors"></a>

関数が失敗する可能性があることを知らせるには、`error` を使用します。慣習上、`error` は最後の結果パラメータとなります。

```go
// Good:
func Good() error { /* ... */ }
```

`nil`エラーを返すことは、他の方法では失敗する可能性のある操作が成功したことを知らせるための慣用的な方法です。
関数がエラーを返した場合、呼び出し側は、明示的に文書化されていない限り、エラー以外の戻り値をすべて未定義として扱わなければなりません。
一般に、非エラーの戻り値はそのゼロ値であるが、これを仮定することはできません。

```go
// Good:
func GoodLookup() (*Result, error) {
    // ...
    if err != nil {
        return nil, err
    }
    return res, nil
}
```

エラーを返すエクスポートされた関数は、 `error` 型を使用してエラーを返す必要があります。
具体的なエラータイプは微妙なバグの影響を受けやすく、具体的な `nil` ポインタがインターフェースにラップされてしまい、nil でない値になってしまうことがあります (このトピックに関する [Go FAQ entry on the topic][nil error] のエントリーを参照してください)。

```go
// Bad:
func Bad() *os.PathError { /*...*/ }
```

**Tip**: `Context.Context` 引数を取る関数は、通常 `error` を返すべきです。そうすれば、関数の実行中にコンテキストがキャンセルされたかどうかを呼び出し側が判断できます。

[nil error]: https://golang.org/doc/faq#nil_error

<a id="error-strings"></a>

### Error strings

<a id="TOC-ErrorStrings"></a>

エラー文字列は、大文字にせず、句読点で終わらせてはいけません(エクスポート名、固有名詞、頭字語で始まる場合を除く)。これは、エラー文字列は通常、ユーザーに印刷される前に他の文脈で表示されるからです。

```go
// Bad:
err := fmt.Errorf("Something bad happened.")
```

```go
// Good:
err := fmt.Errorf("something bad happened")
```

一方、全文表示されるメッセージ（ロギング、テスト失敗、APIレスポンス、その他のUI）は、スタイルは異なりますが、通常は大文字で表記する必要があります。

```go
// Good:
log.Infof("Operation aborted: %v", err)
log.Errorf("Operation aborted: %v", err)
t.Errorf("Op(%q) failed unexpectedly; err=%v", args, err)
```

<a id="handle-errors"></a>

### Handle errors

<a id="TOC-HandleErrors"></a>

エラーに遭遇したコードは、それをどのように処理するかについて、意図的に選択する必要があります。
通常、`_` 変数を使用してエラーを破棄することは適切ではありません。
関数がエラーを返した場合は、次のいずれかを実行します：

* エラーをすぐに処理し、対処する。
* エラーを呼び出し元に返す。
* 例外的な状況では、[`log.Fatal`] または (絶対に必要な場合は) `panic` を呼び出す。

**Note:** `log.Fatalf` は標準ライブラリのログではありません。[#logging]を参照してください。

エラーを無視したり破棄したりすることが適切な稀な状況（例えば、絶対に失敗しないと文書化されている [`(*bytes.Buffer).Write`]の呼び出し）では、付随するコメントで、なぜそれが安全であるかを説明する必要があります。

```go
// Good:
var b *bytes.Buffer

n, _ := b.Write(p) // never returns a non-nil error
```

エラー処理の詳しい説明や例については、[Effective Go](http://golang.org/doc/effective_go.html#errors) や [best practices](best-practices.ja.md#error-handling) を参照してください。

[`(*bytes.Buffer).Write`]: https://pkg.go.dev/bytes#Buffer.Write

<a id="in-band-errors"></a>

### In-band errors

<a id="TOC-In-Band-Errors"></a>

C言語や類似の言語では、エラーや結果の欠落を知らせるために、関数が-1、null、空文字列などの値を返すのが一般的です。これはバンド内エラー処理として知られています。

```go
// Bad:
// Lookup returns the value for key or -1 if there is no mapping for key.
func Lookup(key string) int
```

バンド内エラー値のチェックを怠ると、バグが発生したり、エラーを間違った機能に帰属させたりする可能性があります。

```go
// Bad:
// The following line returns an error that Parse failed for the input value,
// whereas the failure was that there is no mapping for missingKey.
return Parse(Lookup(missingKey))
```

Goの複数の戻り値のサポートは、より良い解決策を提供します（[Effective Go section on multiple returns]を参照してください）。
クライアントにバンド内エラー値のチェックを要求する代わりに、関数はその他の戻り値が有効であるかどうかを示す追加の値を返すべきである。
この戻り値は，エラーであってもよいし，説明が不要な場合にはブール値であってもよく，最終的な戻り値であるべきです。

```go
// Good:
// Lookup returns the value for key or ok=false if there is no mapping for key.
func Lookup(key string) (value string, ok bool)
```

このAPIは、`Lookup(key)` が2つの出力を持つため、呼び出し側がコンパイルエラーを引き起こす `Parse(Lookup(key))` を誤って記述することを防ぎます。

このようにエラーを返すことで、より強固で明示的なエラー処理を促すことができます：

```go
// Good:
value, ok := Lookup(key)
if !ok {
    return fmt.Errorf("no value for %q", key)
}
return Parse(value)
```

`strings` パッケージのようないくつかの標準ライブラリ関数は、バンド内のエラー値を返します。
これは文字列操作のコードを大幅に簡略化しますが、その代償としてプログラマがより注意深くなる必要があります。
一般的に、GoogleコードベースのGoコードは、エラーに対して追加の値を返すべきです。

[Effective Go section on multiple returns]: http://golang.org/doc/effective_go.html#multiple-returns

<a id="indent-error-flow"></a>

### Indent error flow

<a id="TOC-IndentErrorFlow"></a>

エラーを処理してから、残りのコードに進みます。
これにより、読者が正常なパスを素早く見つけることができるようになり、コードの可読性が向上します。
この同じロジックは、ある条件をテストした後に終了条件となるブロック（例：`return`、`panic`、`log.Fatal`）にも適用されます。

終端条件が満たされない場合に実行されるコードは、`if` ブロックの後に表示されるべきで、`else` 句の中ではインデントされるべきではありません。

```go
// Good:
if err != nil {
    // error handling
    return // or continue, etc.
}
// normal code
```

```go
// Bad:
if err != nil {
    // error handling
} else {
    // normal code that looks abnormal due to indentation
}
```

> **Tip:** 数行以上のコードで変数を使用する場合、一般的に `if`-with-initializer スタイルを使用する価値はありません。
> このような場合は、宣言を外に出して、標準的な `if` 文を使用した方が良い場合がほとんどです：
>
> ```go
> // Good:
> x, err := f()
> if err != nil {
>   // error handling
>   return
> }
> // lots of code that uses x
> // across multiple lines
> ```
>
> ```go
> // Bad:
> if x, err := f(); err != nil {
>   // error handling
>   return
> } else {
>   // lots of code that uses x
>   // across multiple lines
> }
> ```

より詳細は [Go Tip #1: Line of Sight] と [TotT: Reduce Code Complexity by Reducing Nesting](https://testing.googleblog.com/2017/06/code-health-reduce-nesting-reduce.html) を参照してください。

[Go Tip #1: Line of Sight]: https://google.github.io/styleguide/go/index.html#gotip

<a id="language"></a>

## Language

<a id="literal-formatting"></a>

### Literal formatting

Goには非常に強力な[composite literal syntax] (複合リテラル構文) があり、深くネストした複雑な値を1つの式で表現することが可能です。
可能な限り、フィールドごとに値を構築する代わりに、このリテラル構文を使用する必要があります。
リテラルの `gofmt` フォーマットは一般的に非常に優れていますが、これらのリテラルを読みやすく、保守的に保つための追加のルールがいくつか存在します。

[composite literal syntax]: https://golang.org/ref/spec#Composite_literals

<a id="literal-field-names"></a>

#### Field names

構造体リテラルは、通常、現在のパッケージの外で定義された型の **フィールド名** を指定する必要があります。

* 他のパッケージの型のフィールド名を含めます。

    ```go
    // Good:
    good := otherpkg.Type{A: 42}
    ```

    構造体内のフィールドの位置やフィールドのフルセット（いずれもフィールド名を省略した場合に正しく理解する必要がある）は、通常、構造体の公開APIの一部とは考えられません。不要な結合を避けるために、フィールド名の指定が必要です。

    ```go
    // Bad:
    // https://pkg.go.dev/encoding/csv#Reader
    r := csv.Reader{',', '#', 4, false, false, false, false}
    ```

    構成や順序が安定していることが文書で確認されている小さな単純な構造体内では、フィールド名を省略することができます。

    ```go
    // Good:
    okay := image.Point{42, 54}
    also := image.Point{X: 42, Y: 54}
    ```

* パッケージローカル型の場合、フィールド名は任意です。

    ```go
    // Good:
    okay := Type{42}
    also := internalType{4, 2}
    ```

    フィールド名は、コードがより明確になるのであれば、依然として使用されるべきであり、そうすることは非常に一般的です。
    例えば、多数のフィールドを持つ構造体は、ほとんどの場合、フィールド名で初期化する必要があります。

    <!-- TODO: Maybe a better example here that doesn't have many fields. -->

    ```go
    // Good:
    okay := StructWithLotsOfFields{
      field1: 1,
      field2: "two",
      field3: 3.14,
      field4: true,
    }
    ```

<a id="literal-matching-braces"></a>

#### Matching braces

中括弧の組の終端は、常に開口中括弧と同じインデント量で行に表示されなければなりません。
1行のリテラルには必ずこの特性があります。リテラルが複数行にまたがる場合、このプロパティを維持することで、リテラルに対するブレースのマッチングは、関数や `if` 文などの一般的なGo構文に対するブレースマッチングと同じになります。

この分野で最もよくある間違いは、複数行の構造体リテラルの値と同じ行に閉じ括弧を置くことです。
このような場合、その行はコンマで終わり、閉じ波括弧は次の行に表示されるべきです。

```go
// Good:
good := []*Type{{Key: "value"}}
```

```go
// Good:
good := []*Type{
    {Key: "multi"},
    {Key: "line"},
}
```

```go
// Bad:
bad := []*Type{
    {Key: "multi"},
    {Key: "line"}}
```

```go
// Bad:
bad := []*Type{
    {
        Key: "value"},
}
```

<a id="literal-cuddled-braces"></a>

#### Cuddled braces

スライスや配列のリテラルで中括弧の間の空白を削除すること(抱き合わせ) は、以下の両方が成立する場合にのみ許可されます。

* [indentation matches](#literal-matching-braces) (インデントマッチ) の場合
* 内側の値もリテラルまたはプロトビルダー（つまり、変数などの式ではない）の場合

```go
// Good:
good := []*Type{
    { // Not cuddled
        Field: "value",
    },
    {
        Field: "value",
    },
}
```

```go
// Good:
good := []*Type{{ // Cuddled correctly
    Field: "value",
}, {
    Field: "value",
}}
```

```go
// Good:
good := []*Type{
    first, // Can't be cuddled
    {Field: "second"},
}
```

```go
// Good:
okay := []*pb.Type{pb.Type_builder{
    Field: "first", // Proto Builders may be cuddled to save vertical space
}.Build(), pb.Type_builder{
    Field: "second",
}.Build()}
```

```go
// Bad:
bad := []*Type{
    first,
    {
        Field: "second",
    }}
```

<a id="literal-repeated-type-names"></a>

#### Repeated type names

スライスやマップのリテラルでは、繰り返される型名が省略されることがあります。
これは、散らかりを減らすのに有効です。
型名を明示的に繰り返す妥当な場面は、プロジェクトで一般的でない複雑な型を扱う場合、繰り返される型名が離れた行にあり、読者に文脈を思い出させることができる場合です。

```go
// Good:
good := []*Type{
    {A: 42},
    {A: 43},
}
```

```go
// Bad:
repetitive := []*Type{
    &Type{A: 42},
    &Type{A: 43},
}
```

```go
// Good:
good := map[Type1]*Type2{
    {A: 1}: {B: 2},
    {A: 3}: {B: 4},
}
```

```go
// Bad:
repetitive := map[Type1]*Type2{
    Type1{A: 1}: &Type2{B: 2},
    Type1{A: 3}: &Type2{B: 4},
}
```

**Tip:** 構造体リテラルで繰り返される型名を削除したい場合は、`gofmt -s` を実行します。

<a id="literal-zero-value-fields"></a>

#### Zero-value fields

[Zero-value] (ゼロ値)フィールドは、構造体リテラルの明確性が損なわれない場合には、省略することができます。

よくできたAPIでは、可読性を高めるためにゼロ値構造を採用することがよくあります。
例えば、次の構造体から3つのゼロ値フィールドを省略すると、指定されている唯一のオプションに注目することができます。

[Zero-value]: https://golang.org/ref/spec#The_zero_value

```go
// Bad:
import (
  "github.com/golang/leveldb"
  "github.com/golang/leveldb/db"
)

ldb := leveldb.Open("/my/table", &db.Options{
    BlockSize: 1<<16,
    ErrorIfDBExists: true,

    // These fields all have their zero values.
    BlockRestartInterval: 0,
    Comparer: nil,
    Compression: nil,
    FileSystem: nil,
    FilterPolicy: nil,
    MaxOpenFiles: 0,
    WriteBufferSize: 0,
    VerifyChecksums: false,
})
```

```go
// Good:
import (
  "github.com/golang/leveldb"
  "github.com/golang/leveldb/db"
)

ldb := leveldb.Open("/my/table", &db.Options{
    BlockSize: 1<<16,
    ErrorIfDBExists: true,
})
```

テーブル駆動テスト内の構造体は、特にテスト構造体が些細なものでない場合、[explicit field names] (フィールド名の明示) が有効なことが多いです。
これにより、当該フィールドがテストケースに関連していない場合、作者はゼロ値フィールドを完全に省略することができます。
例えば、成功したテストケースは、エラー関連や失敗関連のフィールドを省略する必要があります。ゼロまたは `nil` 入力に対するテストのように、テストケースを理解するためにゼロ値が必要な場合は、フィールド名を指定する必要があります。

[explicit field names]: #literal-field-names

**Concise: 簡潔**

```go
tests := []struct {
    input      string
    wantPieces []string
    wantErr    error
}{
    {
        input:      "1.2.3.4",
        wantPieces: []string{"1", "2", "3", "4"},
    },
    {
        input:   "hostname",
        wantErr: ErrBadHostname,
    },
}
```

**Explicit: 明示的**

```go
tests := []struct {
    input    string
    wantIPv4 bool
    wantIPv6 bool
    wantErr  bool
}{
    {
        input:    "1.2.3.4",
        wantIPv4: true,
        wantIPv6: false,
    },
    {
        input:    "1:2::3:4",
        wantIPv4: false,
        wantIPv6: true,
    },
    {
        input:    "hostname",
        wantIPv4: false,
        wantIPv6: false,
        wantErr:  true,
    },
}
```

<a id="nil-slices"></a>

### Nil slices

ほとんどの場合、`nil` と空のスライスの間に機能的な違いはありません。
`len` や `cap` などの組み込み関数は `nil` スライスに対して期待通りの動作をします。

```go
// Good:
import "fmt"

var s []int         // nil

fmt.Println(s)      // []
fmt.Println(len(s)) // 0
fmt.Println(cap(s)) // 0
for range s {...}   // no-op

s = append(s, 42)
fmt.Println(s)      // [42]
```

空のスライスをローカル変数として宣言する場合（特に戻り値の元になる場合）、呼び出し側によるバグのリスクを減らすために、nilの初期化を推奨されます。

```go
// Good:
var t []string
```

```go
// Bad:
t := []string{}
```

nil と empty slice の区別をクライアントに強いるようなAPIを作らないでください。

```go
// Good:
// Ping pings its targets.
// Returns hosts that successfully responded.
func Ping(hosts []string) ([]string, error) { ... }
```

```go
// Bad:
// Ping pings its targets and returns a list of hosts
// that successfully responded. Can be empty if the input was empty.
// nil signifies that a system error occurred.
func Ping(hosts []string) []string { ... }
```

インターフェースを設計する際には、`nil`スライスと`nil`でない長さゼロのスライスを区別することは避けてください、これは微妙なプログラミングエラーにつながる可能性があります。
これは通常、`== nil` ではなく `len` を使って空をチェックすることで達成されます。

この実装では、`nil`と長さゼロのスライスの両方を "空 "として受け取ります：

```go
// Good:
// describeInts describes s with the given prefix, unless s is empty.
func describeInts(prefix string, s []int) {
    if len(s) == 0 {
        return
    }
    fmt.Println(prefix, s)
}
```

APIの一部として区別に依存するのではなく:

```go
// Bad:
func maybeInts() []int { /* ... */ }

// describeInts describes s with the given prefix; pass nil to skip completely.
func describeInts(prefix string, s []int) {
  // The behavior of this function unintentionally changes depending on what
  // maybeInts() returns in 'empty' cases (nil or []int{}).
  if s == nil {
    return
  }
  fmt.Println(prefix, s)
}

describeInts("Here are some ints:", maybeInts())
```

詳しくは[in-band errors]を参照してください。

[in-band errors]: #in-band-errors

<a id="indentation-confusion"></a>

### Indentation confusion

改行すると、残りの行がインデントされたコードブロックに揃うような場合は、改行を導入しないようにします。
やむを得ない場合は、ブロック内のコードと折り返し行を分離するためのスペースを空けてください。

```go
// Bad:
if longCondition1 && longCondition2 &&
    // Conditions 3 and 4 have the same indentation as the code within the if.
    longCondition3 && longCondition4 {
    log.Info("all conditions met")
}
```

具体的なガイドラインや例については、以下のセクションを参照してください：

* [Function formatting](#func-formatting)
* [Conditionals and loops](#conditional-formatting)
* [Literal formatting](#literal-formatting)

<a id="func-formatting"></a>

### Function formatting

関数やメソッド宣言のシグネチャは、[indentation confusion](#indentation-confusion) (インデントの混乱) を避けるため、1行にまとめておく必要があります。

関数の引数リストは、Goソースファイルの中で最も長い行を作る可能性があります。
しかし、それらはインデントの変更に先行しているため、後続の行が紛らわしく関数本体の一部のように見えない方法で改行するのは難解です：

```go
// Bad:
func (r *SomeType) SomeLongFunctionName(foo1, foo2, foo3 string,
    foo4, foo5, foo6 int) {
    foo7 := bar(foo1)
    // ...
}
```

多くの引数を持つような関数の呼び出し部位を短くするためのいくつかのオプションについては、[best practices](best-practices.ja.md#funcargs) をご覧ください。

```go
// Good:
good := foo.Call(long, CallOptions{
    Names:   list,
    Of:      of,
    The:     parameters,
    Func:    all,
    Args:    on,
    Now:     separate,
    Visible: lines,
})
```

```go
// Bad:
bad := foo.Call(
    long,
    list,
    of,
    parameters,
    all,
    on,
    separate,
    lines,
)
```

ローカル変数を因数分解することで、行を短くすることができる場合があります。

```go
// Good:
local := helper(some, parameters, here)
good := foo.Call(list, of, parameters, local)
```

同様に、関数とメソッドの呼び出しは、行の長さだけに基づいて分離されるべきではありません。

```go
// Good:
good := foo.Call(long, list, of, parameters, all, on, one, line)
```

```go
// Bad:
bad := foo.Call(long, list, of, parameters,
    with, arbitrary, line, breaks)
```

特定の関数パラメータにコメントを追加しないでください。
代わりに、[option struct](best-practices#option-structure) (オプション構造体) を使用するか、関数のドキュメントに詳細を追加してください。

```go
// Good:
good := server.New(ctx, server.Options{Port: 42})
```

```go
// Bad:
bad := server.New(
    ctx,
    42, // Port
)
```

コールサイトが不快なほど長い場合は、リファクタリングを検討してください：

```go
// Good:
// Sometimes variadic arguments can be factored out
replacements := []string{
    "from", "to", // related values can be formatted adjacent to one another
    "source", "dest",
    "original", "new",
}

// Use the replacement struct as inputs to NewReplacer.
replacer := strings.NewReplacer(replacements...)
```

APIを変更できない場合や、ローカル呼び出しが異常な場合（呼び出しが長すぎるかどうかは別として）、呼び出しの理解を助けるのであれば、改行を加えることは常に許容されます。

```go
// Good:
canvas.RenderCube(cube,
    x0, y0, z0,
    x0, y0, z1,
    x0, y1, z0,
    x0, y1, z1,
    x1, y0, z0,
    x1, y0, z1,
    x1, y1, z0,
    x1, y1, z1,
)
```

上の例の線は、特定の列の境界で折り返されるのではなく、座標のトリプルに基づいてグループ化されていることに注意してください。

関数内の長い文字列リテラルは、行の長さのために改行してはいけません。
このような文字列を含む関数では、文字列の書式の後に改行を追加し、引数は次行以降で提供することができます。
改行位置の決定は、純粋に行の長さではなく、入力の意味的なグループ分けに基づいて行うのが最善です。

```go
// Good:
log.Warningf("Database key (%q, %d, %q) incompatible in transaction started by (%q, %d, %q)",
    currentCustomer, currentOffset, currentKey,
    txCustomer, txOffset, txKey)
```

```go
// Bad:
log.Warningf("Database key (%q, %d, %q) incompatible in"+
    " transaction started by (%q, %d, %q)",
    currentCustomer, currentOffset, currentKey, txCustomer,
    txOffset, txKey)
```

<a id="conditional-formatting"></a>

### Conditionals and loops

`if` 文は改行してはいけません。
複数行の `if` 句は [indentation confusion](#indentation-confusion) (インデントの混乱) につながる可能性があります。

```go
// Bad:
// The second if statement is aligned with the code within the if block, causing
// indentation confusion.
if db.CurrentStatusIs(db.InTransaction) &&
    db.ValuesEqual(db.TransactionKey(), row.Key()) {
    return db.Errorf(db.TransactionError, "query failed: row (%v): key does not match transaction key", row)
}
```

短絡動作が必要ない場合は、Boolean オペランドを直接抽出することができます：

```go
// Good:
inTransaction := db.CurrentStatusIs(db.InTransaction)
keysMatch := db.ValuesEqual(db.TransactionKey(), row.Key())
if inTransaction && keysMatch {
    return db.Error(db.TransactionError, "query failed: row (%v): key does not match transaction key", row)
}
```

また、特にすでに繰り返されている条件の場合、抽出できる他のロケールがある可能性があります：

```go
// Good:
uid := user.GetUniqueUserID()
if db.UserIsAdmin(uid) || db.UserHasPermission(uid, perms.ViewServerConfig) || db.UserHasPermission(uid, perms.CreateGroup) {
    // ...
}
```

```go
// Bad:
if db.UserIsAdmin(user.GetUniqueUserID()) || db.UserHasPermission(user.GetUniqueUserID(), perms.ViewServerConfig) || db.UserHasPermission(user.GetUniqueUserID(), perms.CreateGroup) {
    // ...
}
```

クロージャや複数行の構造体リテラルを含む `if` 文は、[indentation confusion](#indentation-confusion) (インデントの混乱) を避けるために、[braces match](#literal-matching-braces) (括弧の一致) を確認する必要があります。

```go
// Good:
if err := db.RunInTransaction(func(tx *db.TX) error {
    return tx.Execute(userUpdate, x, y, z)
}); err != nil {
    return fmt.Errorf("user update failed: %s", err)
}
```

```go
// Good:
if _, err := client.Update(ctx, &upb.UserUpdateRequest{
    ID:   userID,
    User: user,
}); err != nil {
    return fmt.Errorf("user update failed: %s", err)
}
```

同様に、`for` 文に人工的な改行を挿入しようとしないでください。
リファクタリングするエレガントな方法がない場合は、いつでも行を単に長くしておくことができます：

```go
// Good:
for i, max := 0, collection.Size(); i < max && !collection.HasPendingWriters(); i++ {
    // ...
}
```

あることが多いのですが：

```go
// Good:
for i, max := 0, collection.Size(); i < max; i++ {
    if collection.HasPendingWriters() {
        break
    }
    // ...
}
```

`switch` と `case` の記述も1行にまとめておく必要があります。

```go
// Good:
switch good := db.TransactionStatus(); good {
case db.TransactionStarting, db.TransactionActive, db.TransactionWaiting:
    // ...
case db.TransactionCommitted, db.NoTransaction:
    // ...
default:
    // ...
}
```

```go
// Bad:
switch bad := db.TransactionStatus(); bad {
case db.TransactionStarting,
    db.TransactionActive,
    db.TransactionWaiting:
    // ...
case db.TransactionCommitted,
    db.NoTransaction:
    // ...
default:
    // ...
}
```

行が過度に長くなる場合は、[indentation confusion](#indentation-confusion) (インデントの混乱) を避けるため、すべてのケースをインデントして空白行で区切ってください：

```go
// Good:
switch db.TransactionStatus() {
case
    db.TransactionStarting,
    db.TransactionActive,
    db.TransactionWaiting,
    db.TransactionCommitted:

    // ...
case db.NoTransaction:
    // ...
default:
    // ...
}
```

変数と定数を比較する条件式では、変数値を等号演算子の左辺に置きます：

```go
// Good:
if result == "foo" {
  // ...
}
```

定数が先に来るというあまり明確でない表現ではなく（["Yoda style conditionals""](https://en.wikipedia.org/wiki/Yoda_conditions))：

```go
// Bad:
if "foo" == result {
  // ...
}
```

<a id="copying"></a>

### Copying

<a id="TOC-Copying"></a>

予期せぬエイリアスや同様のバグを避けるため、他のパッケージから構造体をコピーする際には注意が必要です。
例えば、`sync.Mutex` のような同期オブジェクトは、コピーしてはいけません。

`bytes.Buffer` 型は `[]byte` スライスを含み、小さな文字列のための最適化として、スライスが参照する小さなバイト配列があります。
`Buffer` をコピーすると、コピーのスライスが元の配列のエイリアスになる可能性があり、その後のメソッド呼び出しが意外な効果をもたらすことがあります。

一般に、`T` 型の値がポインタ型である `*T` に関連付けられたメソッドを持つ場合、その値をコピーしてはいけません。

```go
// Bad:
b1 := bytes.Buffer{}
b2 := b1
```

値のレシーバーを取るメソッドを呼び出すと、コピーを隠すことができます。
APIを作成する際、構造体にコピーしてはいけないフィールドがある場合は、一般的にポインタ型を受け取り、返すようにします。

これらは許容範囲内です：

```go
// Good:
type Record struct {
  buf bytes.Buffer
  // other fields omitted
}

func New() *Record {...}

func (r *Record) Process(...) {...}

func Consumer(r *Record) {...}
```

しかし、これらは大抵間違っています：

```go
// Bad:
type Record struct {
  buf bytes.Buffer
  // other fields omitted
}


func (r Record) Process(...) {...} // Makes a copy of r.buf

func Consumer(r Record) {...} // Makes a copy of r.buf
```

このガイダンスは、`sync.Mutex` をコピーする場合にも適用されます。

<a id="dont-panic"></a>

### Don't panic

<a id="TOC-Don-t-Panic"></a>

通常のエラー処理には `panic` を使用しないでください。代わりに `error` と複数の戻り値を使用します。
エラーについては [Effective Go section on errors] を参照してください。

`package main` と初期化コードの中で、プログラムを終了させるべきエラー（例：無効な設定）については [`log.Exit`] を考慮してください。これらのケースの多くでは、スタックトレースが読者の助けにならないからです。
[`log.Exit`] は [`os.Exit`] を呼び出し、遅延した関数は実行されないことに注意してください。

「不可能」な状態を示すエラー、すなわちコードレビューやテスト中に必ず発見されるべきバグについては、関数はエラーを返すか、[`log.Fatal`] を呼ぶのが妥当でしょう。

**Note:** `log.Fatalf` は標準ライブラリのログではありません。[#logging] を参照してください。

[Effective Go section on errors]: http://golang.org/doc/effective_go.html#errors
[`os.Exit`]: https://pkg.go.dev/os#Exit

<a id="must-functions"></a>

### Must functions

失敗したときにプログラムを停止させるセットアップヘルパー関数は、 `MustXYZ` (または `mustXYZ`) という命名規則に従っています。一般的に、これらはプログラムの起動初期にのみ呼び出すべきで、ユーザー入力のように通常のGoエラー処理を優先するような場合には呼び出すべきではありません。

[package initialization time](https://golang.org/ref/spec#Package_initialization) (パッケージの初期化時) に限定してパッケージレベルの変数を初期化するために呼び出される関数でよく出てくる項目です。
(例えば [template.Must](https://golang.org/pkg/text/template/#Must) や [regexp.MustCompile](https://golang.org/pkg/regexp/#MustCompile)).

```go
// Good:
func MustParse(version string) *Version {
    v, err := Parse(version)
    if err != nil {
        log.Fatalf("MustParse(%q) = _, %v", version, err)
    }
    return v
}

// Package level "constant". If we wanted to use `Parse`, we would have had to
// set the value in `init`.
var DefaultVersion = MustParse("1.2.3")
```

**Note:** `log.Fatalf` は標準ライブラリのログではありません。[#logging] を参照してください。

同じ規約は、現在のテストだけを停止させるテストヘルパーでも使用できます (`t.Fatal` を使用)。
このようなヘルパーは、例えば [table driven tests](#table-driven-tests) (テーブル駆動テスト) の struct フィールドにテスト値を作成する際に便利なことがよくあります。

```go
// Good:
func mustMarshalAny(t *testing.T, m proto.Message) *anypb.Any {
  t.Helper()
  any, err := anypb.New(m)
  if err != nil {
    t.Fatalf("MustMarshalAny(t, m) = %v; want %v", err, nil)
  }
  return any
}

func TestCreateObject(t *testing.T) {
  tests := []struct{
    desc string
    data *anypb.Any
  }{
    {
      desc: "my test case",
      // Creating values directly within table driven test cases.
      data: mustMarshalAny(t, mypb.Object{}),
    },
    // ...
  }
  // ...
}
```

これら両方のケースにおいて、このパターンの価値は、ヘルパーを「値」のコンテキストで呼び出すことができることです。
これらのヘルパーは、エラーを確実に捕捉するのが難しい場所や、エラーが [checked](#handle-errors) されるべきコンテキスト (たとえば、多くのリクエストハンドラ) では呼ばないようにしましょう。
定数入力の場合は、`Must` 引数が整形されていることを簡単に確認することができます。定数以外の入力の場合は、エラーが [properly handled or propagated] (best-practices.ja.md#error-handling) (適切に処理されるか伝播するか) を検証することができます。

テスト内で `Must` 関数を使用する場合、一般的には [marked as a test helper](#mark-test-helpers) (テストヘルパーとしてマーク) して、エラー時に `t.Fatal` を呼び出します (テストヘルパーを使う際の考慮点については [error handling in test helpers](best-practices.ja.md#test-helper-error-handling)を参照して下さい)。

[ordinary error handling](best-practices.ja.md#error-handling) (通常のエラー処理) が可能な場合は、使用すべきではありません（多少のリファクタリングを含む）：

```go
// Bad:
func Version(o *servicepb.Object) (*version.Version, error) {
    // Return error instead of using Must functions.
    v := version.MustParse(o.GetVersionString())
    return dealiasVersion(v)
}
```

<a id="goroutine-lifetimes"></a>

### Goroutine lifetimes

<a id="TOC-GoroutineLifetimes"></a>

ゴルーチン（goroutine）を生成する場合は、いつ終了するか、あるいは終了するかどうかを明確にしましょう。

ゴルーチンは、チャンネルの送受信をブロックすることでリークすることがあります。
ガベージコレクタは、ブロックされているチャネルが到達不可能であっても、ゴルーチンを終了させることはありません。

ゴルーチンがリークしない場合でも、不要になったゴルーチンをそのままにしておくと、他の微妙で診断しにくい問題を引き起こすことがあります。
閉じたチャネルで送信するとパニックになります。

```go
// Bad:
ch := make(chan int)
ch <- 42
close(ch)
ch <- 13 // panic
```

使用中の入力を「結果が不要になってから」変更すると、データレースにつながることがあります。
ゴルーチンを任意に長い時間放置すると、予測できないメモリ使用量になることがあります。

同時実行コードは、ゴルーチンの寿命が明らかであるように記述する必要があります。
一般的には、同期に関連するコードを関数の範囲内に閉じ込め、ロジックを [synchronous functions] (同期関数) にまとめることを意味します。
それでも並行処理が明白でない場合は、ゴルーチンがいつ、なぜ終了するかを文書化することが重要です。

コンテキストの使い方に関するベストプラクティスに従ったコードは、このことを明確にするのに役立つことがあります。
これは従来、`context.Context` で管理されていました：

```go
// Good:
func (w *Worker) Run(ctx context.Context) error {
    // ...
    for item := range w.q {
        // process returns at latest when the context is cancelled.
        go process(ctx, item)
    }
    // ...
}
```

上記の他にも、`chan struct{}` のような生の信号チャンネルを使うものや、同期変数、[condition variables][rethinking-slides] (条件変数) などの亜種があります。
重要なのは、ゴルーチンの終了が後続のメンテナにとって明らかであることです。

一方、次のコードは、生成されたゴルーチンがいつ終了するかについて、無頓着です：

```go
// Bad:
func (w *Worker) Run() {
    // ...
    for item := range w.q {
        // process returns when it finishes, if ever, possibly not cleanly
        // handling a state transition or termination of the Go program itself.
        go process(item)
    }
    // ...
}
```

このコードは一見問題ないように見えますが、根本的な問題がいくつかあります：

* このコードは、おそらく生産現場では未定義の動作をし、オペレーティングシステムがリソースを解放しても、プログラムはきれいに終了しないかもしれません。
* コードのライフサイクルが不確定なため、意味のあるテストが困難です。
* コードは、上記のようにリソースをリークする可能性があります。

こちらもご覧ください：

* [Never start a goroutine without knowing how it will stop][cheney-stop]
* Rethinking Classical Concurrency Patterns: [slides][rethinking-slides], [video][rethinking-video]
* [When Go programs end]

[synchronous functions]: #synchronous-functions
[cheney-stop]: https://dave.cheney.net/2016/12/22/never-start-a-goroutine-without-knowing-how-it-will-stop
[rethinking-slides]: https://drive.google.com/file/d/1nPdvhB0PutEJzdCq5ms6UI58dp50fcAN/view
[rethinking-video]: https://www.youtube.com/watch?v=5zXAHh5tJqQ
[When Go programs end]: https://changelog.com/gotime/165

<a id="interfaces"></a>

### Interfaces

<a id="TOC-Interfaces"></a>

Goインターフェースは一般的に、インターフェース型の値を*消費*するパッケージに属し、インターフェース型を*実装*するパッケージには属しません。
実装するパッケージは具体的な型（通常はポインタや構造体）を返す必要があります。
そうすれば、大規模なリファクタリングを必要とせずに、新しいメソッドを実装に追加することができます。
詳しくは [GoTip #49: Accept Interfaces, Return Concrete Types] (GoTip #49: インターフェイスを受け入れ、具体的な型を返す) を参照してください。

インターフェースを消費するAPIから、インターフェースの [test double] [double types] の実装をエクスポートしないでください。
代わりに、[real implementation] (実際の実装) の [public API] (公開 API) を使ってテストできるように API を設計してください。
詳しくは [GoTip #42: Authoring a Stub for Testing] (GoTip #42: テスト用のスタブを作成する) をご覧ください。
実際の実装を使用することが不可能な場合でも、実際の型のすべてのメソッドを完全にカバーするインターフェースを導入する必要はないかもしれません; [GoTip #78: Minimal Viable Interfaces] で示されるように、消費者は必要なメソッドだけを含むインターフェースを作成することができます。

Stubby RPC クライアントを使用するパッケージをテストするには、実際のクライアント接続を使用します。
実際のサーバーがテストに使えない場合、Googleの内部では、内部の `rpctest` パッケージ（近日公開予定！）を使ってローカルの [test double] への実際のクライアント接続を取得する方法がとられています。

使用する前にインターフェースを定義してはいけません ([TotT: Code Health: Eliminate YAGNI Smells][tott-438] を参照) 。
現実的な使用例がなければ、どのようなメソッドを含むべきかはおろか、インターフェースが必要なのかどうかさえも判断できません。

パッケージの利用者がパラメータに異なる型を渡す必要がない場合は、interface-typed パラメータを使用しないでください。

パッケージの利用者が必要としないインターフェイスをエクスポートしないでください。

**TODO:** インターフェースについてのより詳細なドキュメントを作成し、ここにリンクを貼る。

[GoTip #42: Authoring a Stub for Testing]: https://google.github.io/styleguide/go/index.html#gotip
[GoTip #49: Accept Interfaces, Return Concrete Types]: https://google.github.io/styleguide/go/index.html#gotip
[GoTip #78: Minimal Viable Interfaces]: https://google.github.io/styleguide/go/index.html#gotip
[real implementation]: best-practices#use-real-transports
[public API]: https://abseil.io/resources/swe-book/html/ch12.html#test_via_public_apis
[double types]: https://abseil.io/resources/swe-book/html/ch13.html#techniques_for_using_test_doubles
[test double]: https://abseil.io/resources/swe-book/html/ch13.html#basic_concepts
[tott-438]: https://testing.googleblog.com/2017/08/code-health-eliminate-yagni-smells.html

```go
// Good:
package consumer // consumer.go

type Thinger interface { Thing() bool }

func Foo(t Thinger) string { ... }
```

```go
// Good:
package consumer // consumer_test.go

type fakeThinger struct{ ... }
func (t fakeThinger) Thing() bool { ... }
...
if Foo(fakeThinger{...}) == "x" { ... }
```

```go
// Bad:
package producer

type Thinger interface { Thing() bool }

type defaultThinger struct{ ... }
func (t defaultThinger) Thing() bool { ... }

func NewThinger() Thinger { return defaultThinger{ ... } }
```

```go
// Good:
package producer

type Thinger struct{ ... }
func (t Thinger) Thing() bool { ... }

func NewThinger() Thinger { return Thinger{ ... } }
```

<a id="generics"></a>

### Generics

Generics (formally called "[Type Parameters]") are allowed where they fulfill your business requirements. 
In many applications, a conventional approach using existing language features (slices, maps, interfaces, and so on) works just as well without the added complexity, so be wary of premature use. 
See the discussion on [(guide#least-mechanism).

ジェネリック（正式には「[型パラメータ]」）は、ビジネス要件を満たす場合に許可されます。
多くのアプリケーションでは、既存の言語機能（スライス、マップ、インターフェースなど）を使った従来のアプローチが、複雑さを増すことなくうまく機能するので、早まった使用には注意が必要です。
[least mechanism](guide.ja.md#least-mechanism) (最小の機構) の議論を参照してください。

メンバー要素の型を気にしないアルゴリズムやデータ構造を実装するからといって、ジェネリックスを使わないでください。
実際にインスタンス化される型が1つしかない場合は、ジェネリックスをまったく使わずに、その型に対してコードを動作させることから始めましょう。
後でポリモーフィズムを追加するのは、不要だとわかった抽象化を削除するのに比べれば簡単です。

ジェネリックを利用してドメイン固有言語（DSL）を発明しないでください。
特に、DSLに大きな負担をかけるようなエラー処理のフレームワークを導入しないでください。
読者に負担をかけることになります。
それよりも、確立された「error handling」(#errors) (エラー処理) のプラクティスを優先しましょう。
テストに関しては、特に「assertion libraries」(#assert) (アサーションライブラリ) やフレームワークの導入に注意し、あまり役に立たない「test failures」(#useful-test-failures) (テストの失敗) が発生しないようにしましょう。

一般的に:

* [Write code, don't design types] (コードを書け、型を設計するな)。Robert Griesemer と Ian Lance Taylor による GopherCon での講演から。
* 有用な統一インターフェースを共有するいくつかの型がある場合、そのインターフェースを使用してソリューションをモデリングすることを検討してください。ジェネリックは必要ないかもしれません。
* その他、`any` 型や過剰な [type switching](https://tour.golang.org/methods/16) (型切り替え) に頼るのではなく、ジェネリックスを検討しましょう。

こちらもご参照ください：

* [Using Generics in Go], talk by Ian Lance Taylor
* [Generics tutorial] on Go's webpage

[Generics tutorial]: https://go.dev/doc/tutorial/generics
[Type Parameters]: https://go.dev/design/43651-type-parameters
[Using Generics in Go]: https://www.youtube.com/watch?v=nr8EpUO9jhw
[Write code, don't design types]: https://www.youtube.com/watch?v=Pa_e9EeCdy8&t=1250s

<a id="pass-values"></a>

### Pass values

<a id="TOC-PassValues"></a>

数バイトを節約するために、関数の引数としてポインターを渡さないようにしましょう。
関数が引数 `x` を `*x` としてのみ読み込む場合、その引数はポインタであってはなりません。
よくある例としては、文字列へのポインタ（`*string`）やインターフェース値へのポインタ（`*io.Reader`）を渡すことがあります。
どちらの場合も、値自体は固定サイズであるため、直接渡すことができます。

このアドバイスは、大きな構造体や、サイズが大きくなる可能性のある小さな構造体には当てはまりません。
特に、プロトコルバッファのメッセージは、一般的に値ではなくポインタで処理されるべきです。
ポインタ型は `proto.Message` インターフェース (`proto.Marshal` や `protocmp.Transform` などで使用可能) を満たしており、プロトコルバッファのメッセージは非常に大きく、時間が経つと大きくなることがよくあります。

<a id="receiver-type"></a>

### Receiver type

<a id="TOC-ReceiverType"></a>

A [method receiver] can be passed either as a value or a pointer, just as if it were a regular function parameter. 
The choice between the two is based on which [] the method should be a part of.

[method receiver] (メソッドレシーバー) は、通常の関数パラメータと同じように、値またはポインタとして渡すことができます。
この2つの選択は、そのメソッドがどの [method set(s)] (メソッドセット(s)) の一部であるべきかに基づいています。

[method receiver]: https://golang.org/ref/spec#Method_declarations
[method set(s)]: https://golang.org/ref/spec#Method_sets

**スピードやシンプルさよりも、正しさが勝る。** 
ポインタ値を使わなければならないケースもあります。
他のケースでは、大きな型や、コードがどのように成長するかよくわからない場合の将来対策としてポインタを選び、単純な [plain old data] には値を使用するようにしましょう。

以下のリストでは、それぞれのケースについてさらに詳しく説明しています：

* レシーバーがスライスで、メソッドがスライスの再スライスや再割り当てをしない場合は、ポインタではなく値を使用する。

    ```go
    // Good:
    type Buffer []byte

    func (b Buffer) Len() int { return len(b) }
    ```

* メソッドがレシーバーを変化させる必要がある場合、レシーバーはポインターでなければならない。

    ```go
    // Good:
    type Counter int

    func (c *Counter) Inc() { *c++ }

    // See https://pkg.go.dev/container/heap.
    type Queue []Item

    func (q *Queue) Push(x Item) { *q = append([]Item{x}, *q...) }
    ```

* レシーバーが、[cannot safely be copied](#copying) (安全にコピーできない) フィールドを含む構造体である場合、ポインタレシーバーを使用してください。一般的な例としては、[`sync.Mutex`] やその他の同期型が挙げられます。

    ```go
    // Good:
    type Counter struct {
        mu    sync.Mutex
        total int
    }

    func (c *Counter) Inc() {
        c.mu.Lock()
        defer c.mu.Unlock()
        c.total++
    }
    ```

    **Tip:** コピーしても安全か安全でないかについては、その型の [Godoc] を確認してください。

* レシーバーの構造体や配列が「大きい」場合は、ポインターレシーバーの方が効率的な場合があります。
  構造体を渡すことは、そのフィールドや要素をすべてメソッドの引数として渡すことと同じです。
  [pass by value](#pass-values) (値で渡す) には大きすぎるようであれば、ポインタを使うのがよいでしょう。

* レシーバーを変更する他の関数を呼び出したり、同時に実行したりするメソッドでは、それらの変更がメソッドから見えないようにする場合は値を使用し、そうでない場合はポインタを使用する。

* レシーバーが構造体や配列で、その要素のいずれかが変更可能なものへのポインターである場合は、ポインターレシーバーを用いて変更可能な意図を明確にすることが望ましい。

    ```go
    // Good:
    type Counter struct {
        m *Metric
    }

    func (c *Counter) Inc() {
        c.m.Add(1)
    }
    ```

* レシーバーが、整数や文字列など、修正の必要がない［built-in type］(組み込み型) の場合は、値を使用する。

    ```go
    // Good:
    type User string

    func (u User) String() { return string(u) }
    ```

* レシーバーがマップ、関数、チャンネルの場合は、ポインタではなく、値を使用する。

    ```go
    // Good:
    // See https://pkg.go.dev/net/http#Header.
    type Header map[string][]string

    func (h Header) Add(key, value string) { /* omitted */ }
    ```

* レシーバーが「小さな」配列や構造体であり、当然ながら値型であり、ミュータブルフィールドやポインタがない場合、通常、値型レシーバーが正しい選択となります。

    ```go
    // Good:
    // See https://pkg.go.dev/time#Time.
    type Time struct { /* omitted */ }

    func (t Time) Add(d Duration) Time { /* omitted */ }
    ```

* 迷ったら、ポインターレシーバーを使う。

一般的なガイドラインとして、ある型のメソッドは、すべてポインタ・メソッドか、すべて値メソッドにすることが望ましい。

**Note:**
関数に値を渡すかポインタを渡すかでパフォーマンスに影響が出るかどうかについては、多くの誤った情報があります。
コンパイラは、スタック上の値をコピーするのと同様に、スタック上の値へのポインタを渡すことを選択できますが、これらの考慮事項は、ほとんどの状況でコードの可読性と正しさに勝るものではありません。
性能に問題がある場合、一方のアプローチが他方を上回ると判断する前に、現実的なベンチマークで両方のアプローチをプロファイルすることが重要です。

[plain old data]: https://en.wikipedia.org/wiki/Passive_data_structure
[`sync.Mutex`]: https://pkg.go.dev/sync#Mutex
[built-in type]: https://pkg.go.dev/builtin

<a id="switch-break"></a>

### `switch` and `break`

<a id="TOC-SwitchBreak"></a>

ターゲットラベルのない `break` 文は `switch` 節の末尾に使用しないでください。
CやJavaとは異なり、Goの `switch` 節は自動的にブレークするので、Cスタイルの動作を実現するには `fallthrough` 文が必要です。
空節の目的を明確にしたい場合は、`break`ではなく、コメントを使用してください。

```go
// Good:
switch x {
case "A", "B":
    buf.WriteString(x)
case "C":
    // handled outside of the switch statement
default:
    return fmt.Errorf("unknown value: %q", x)
}
```

```go
// Bad:
switch x {
case "A", "B":
    buf.WriteString(x)
    break // this break is redundant
case "C":
    break // this break is redundant
default:
    return fmt.Errorf("unknown value: %q", x)
}
```

> **Note:** `switch` 節が `for` ループ内にある場合、`switch` 内で `break` を使用しても、囲んだ `for` ループを終了させることはできません。
>
> ```go
> for {
>   switch x {
>   case "A":
>      break // exits the switch, not the loop
>   }
> }
> ```
>
> 囲みループから逃れるには、`for` 文のラベルを使用します：
>
> ```go
> loop:
>   for {
>     switch x {
>     case "A":
>        break loop // exits the loop
>     }
>   }
> ```

<a id="synchronous-functions"></a>

### Synchronous functions

<a id="TOC-SynchronousFunctions"></a>

同期関数は結果を直接返し、コールバックやチャネル操作を終了させてから返します。
非同期関数よりも同期関数を優先してください。

同期関数は、ゴルーチンを呼び出しの中で局所化させます。
これにより、ゴルーチンの寿命が推測でき、リークやデータ競合を回避することができます。
また、同期関数は、ポーリングや同期を必要とせず、入力を渡して出力をチェックできるため、テストが容易です。

必要であれば、呼び出し側は別のgoroutineで関数を呼び出すことで並行性を持たせることができます。
しかし、呼び出し側で不要な同時実行を取り除くのはかなり困難（不可能な場合もある）です。

こちらもご参照ください：

* "Rethinking Classical Concurrency Patterns", talk by Bryan Mills: [slides][rethinking-slides], [video][rethinking-video]

<a id="type-aliases"></a>

### Type aliases

<a id="TOC-TypeAliases"></a>

新しい型を定義するには、*型定義*である `type T1 T2` を使用します。
新しい型を定義せずに既存の型を参照するには、[*型エイリアス*]、`type T1 = T2`を使用します。
タイプエイリアスはまれで、主な用途は、パッケージを新しいソースコードの場所に移行する際の補助となります。必要でないときは、タイプエイリアスを使わないでください。

[*type alias*]: http://golang.org/ref/spec#Type_declarations

<a id="use-percent-q"></a>

### Use %q

<a id="TOC-UsePercentQ"></a>

Goのフォーマット関数（`fmt.Printf`など）には、ダブルクォーテーションの中にある文字列を表示する `%q` という動詞があります。

```go
// Good:
fmt.Printf("value %q looks like English text", someText)
```

手動で `%s` を使って同等のことをするよりも、`%q` を使うことをお勧めします：

```go
// Bad:
fmt.Printf("value \"%s\" looks like English text", someText)
// Avoid manually wrapping strings with single-quotes too:
fmt.Printf("value '%s' looks like English text", someText)
```

入力値が空であったり、制御文字を含む可能性がある人間向けの出力では、`%q` を使用することが推奨されます。
無音の空文字列に気づくのは非常に難しいですが、`""` はそのようにはっきりと目立ちます。

<a id="use-any"></a>

### Use any

Go 1.18 では `interface{}` の [alias] として `any` 型が導入されています。
エイリアスであるため、 `any` は多くの場面で `interface{}` と同等であり、その他の場面では明示的な変換によって容易に交換することができる。
新しいコードでは `any` を使用することをお勧めします。

[alias]: https://go.googlesource.com/proposal/+/master/design/18130-type-alias.md

## Common libraries

<a id="flags"></a>

### Flags

<a id="TOC-Flags"></a>

Google コードベースの Go プログラムは、[standard `flag` package] (標準の `flag` パッケージ) の内部的な変種を使用します。
これは似たようなインターフェイスを持っていますが、Googleの内部システムとの相互運用がうまくいきます。
Goバイナリのフラグ名は、アンダースコアで単語を区切るのが好ましいですが、フラグの値を保持する変数は標準的なGoの名前のスタイル（[mixed caps]）に従わなければなりません。
具体的には、フラグ名はスネークケースで、変数名はキャメルケースで同等の名前にする必要があります。

```go
// Good:
var (
    pollInterval = flag.Duration("poll_interval", time.Minute, "Interval to use for polling.")
)
```

```go
// Bad:
var (
    poll_interval = flag.Int("pollIntervalSeconds", 60, "Interval to use for polling in seconds.")
)
```

フラグは `package main` またはそれに相当する場所でしか定義してはいけません。

汎用パッケージは、コマンドラインインタフェースではなく、GoのAPIを使用して設定する必要があります。
つまり、明示的な関数引数や構造体フィールドの割り当て、あるいは、それほど頻繁ではありませんが、厳密な監視のもとでエクスポートされたグローバル変数を使用することをお勧めします。
極稀にこのルールを破る必要がある場合、フラグ名はそのフラグが設定するパッケージを明確に示す必要があります。

フラグがグローバル変数である場合は、importsの項に従って、独自の `var` グループに配置します。

また、サブコマンドを使った[complex CLIs] (複雑なCLI) を作成するためのベストプラクティスに関する議論も行われています。

こちらもご参照ください：

* [Tip of the Week #45: Avoid Flags, Especially in Library Code][totw-45]
* [Go Tip #10: Configuration Structs and Flags](https://google.github.io/styleguide/go/index.html#gotip)
* [Go Tip #80: Dependency Injection Principles](https://google.github.io/styleguide/go/index.html#gotip)

[standard `flag` package]: https://golang.org/pkg/flag/
[mixed caps]: guide.ja.md#mixed-caps
[complex CLIs]: best-practices.ja.md#complex-clis
[totw-45]: https://abseil.io/tips/45

<a id="logging"></a>

### Logging

Google コードベースの Go プログラムは、[standard `log` package] (標準の `log` パッケージ) の変種を使用しています。
これは似たようなものですが、より強力なインターフェイスを持っており、Googleの内部システムとうまく相互運用することができます。
このライブラリのオープンソース版は [package `glog`] として提供されており、オープンソースの Google プロジェクトではそれを使用することができますが、このガイドではずっと `log` と表記しています。

**Note:**
異常終了の場合、本ライブラリではスタックトレース付きで中止する場合は `log.Fatal` を、スタックトレースなしで中止する場合は `log.Exit` を使用します。
標準ライブラリのような `log.Panic` 関数は存在しません。

**Tip:**
`log.Info(v)` は `log.Infof("%v", v)` と同等であり、他のロギングレベルでも同様です。
書式を設定する必要がない場合は、非書式化バージョンを優先してください。

こちらもご参照ください：

* Best practices on [logging errors](best-practices.ja.md#error-logging) and [custom verbosily levels](best-practices.ja.md#vlog)
* When and how to use the log package to [stop the program](best-practices.ja.md#checks-and-panics)

[standard `log` package]: https://pkg.go.dev/log
[package `glog`]: https://pkg.go.dev/github.com/golang/glog
[`log.Exit`]: https://pkg.go.dev/github.com/golang/glog#Exit
[`log.Fatal`]: https://pkg.go.dev/github.com/golang/glog#Fatal

<a id="contexts"></a>

### Contexts

<a id="TOC-Contexts"></a>

[`context.Context`] 型の値は、API やプロセスの境界を越えて、セキュリティ認証情報、トレース情報、期限、キャンセルシグナルを伝えます。
Google のコードベースではスレッドローカルストレージを使用する C++ や Java とは異なり、Go プログラムは RPC や HTTP リクエストの受信から送信までの関数呼び出しチェーン全体でコンテキストを明示的に渡します。

[`context.Context`]: https://pkg.go.dev/context

関数やメソッドに渡す場合は、常に `context.Context` が最初のパラメータとなるようにします。

```go
func F(ctx context.Context /* other arguments */) {}
```

ただし、例外はあります：

* HTTPハンドラで、コンテキストが [`req.Context()`](https://pkg.go.dev/net/http#Request.Context) に由来する場合。
* ストリーミングRPCメソッドで、コンテキストがストリームから来る場合。
  gRPC ストリーミングを使用するコードは、`grpc.ServerStream` を実装した生成されたサーバータイプの `Context()` メソッドからコンテキストにアクセスします。
  [gRPC Generated Code documentation](https://grpc.io/docs/languages/go/generated-code/) を参照してください。

* エントリポイント関数（そのような関数の例については後述）では、[`context.Background()`](https://pkg.go.dev/context/#Background) を使用します。
  * バイナリターゲットでは メイン(main)
  * 汎用的なコードやライブラリでは `init`
  * テストでは `TestXXX`、`BenchmarkXXX`、`FuzzXXX`

> **Note**:
> コールチェーンの途中のコードで、`context.Background()` を使ってそれ自身のベースコンテキストを作成する必要があることは非常にまれです。
> 間違ったコンテキストでない限り、常に通話相手からコンテキストを取ることを好みます。
>
> サーバライブラリ（ Google の Go 用サーバフレームワークの Stubby や gRPC、HTTP の実装）の中には、リクエストごとに新しいコンテキストオブジェクトを構築するものがあるかもしれません。
> これらのコンテキストは、受信したリクエストからの情報で直ちに満たされ、リクエストハンドラーに渡されたとき、コンテキストの付属の値は、クライアント呼び出し元からネットワーク境界を越えてそのコンテキストに伝搬されます。
> さらに、これらのコンテキストの寿命は、リクエストの寿命にスコープされます：リクエストが終了すると、コンテキストはキャンセルされます。
>
> サーバフレームワークを実装していない限り、ライブラリコードで `context.Background()` を使ってコンテキストを作成するべきではありません。
> その代わり、既存のコンテキストがある場合は、以下に述べるようなコンテキストデタッチメントを使用することをお勧めします。
> もし、エントリポイント関数の外で `context.Background()` が必要だと思う場合は、実装を決める前に Google Go style メーリングリストに相談してください。

関数では `context.Context` が最初に来るという慣例は、テストヘルパーにも適用されます。

```go
// Good:
func readTestFile(ctx context.Context, t *testing.T, path string) string {}
```

構造体タイプにコンテキスト・メンバーを追加しないでください。その代わりに、コンテキストパラメータを渡す必要がある型の各メソッドにコンテキストパラメータを追加してください。
ただし、シグネチャが標準ライブラリのインターフェイスと一致する必要があるメソッド、あるいは
Google の管理外のサードパーティライブラリを使用している場合があります。
このようなケースは非常にまれであり、実装や可読性の確認の前に、Google Go style メーリングリストに相談するようにしてください。

Google のコードベースにおいて、親コンテキストがキャンセルされた後に実行できるバックグラウンド操作を生成する必要があるコードは、切り離しのために内部パッケージを使用することができます。
オープンソースの代替案については、[issue #40221](https://github.com/golang/go/issues/40221)を参照してください。
コンテキストは不変なので、同じデッドライン、キャンセルシグナル、クレデンシャル、親トレースなどを共有する複数のコールに同じコンテキストを渡しても問題ありません。

こちらもご参照ください：

* [Contexts and structs]

[Contexts and structs]: https://go.dev/blog/context-and-structs

<a id="custom-contexts"></a>

#### Custom contexts

カスタムのコンテキストタイプを作成したり、関数のシグネチャで `context.Context` 以外のインターフェイスを使用したりしないでください。
このルールに例外はありません。

もしすべてのチームがカスタムコンテキストを持っていたらと想像してください。
パッケージ `p` からパッケージ `q` へ関数を呼び出すたびに、パッケージ `p` と `q` のすべてのペアについて、 `p.Context` を `q.Context` に変換する方法を決定しなければならないでしょう。
これは人間にとって非現実的であり、エラーが発生しがちです。また、コンテキストパラメータを追加する自動リファクタリングはほぼ不可能です。

アプリケーションデータを渡す場合は、パラメータ、レシーバー、グローバル、または `Context` 値の中に入れてください（本当にそこに属するものであれば）。
独自のコンテキストタイプを作成することは、本番で Go プログラムを正しく動作させるための Go チームの能力を損なうので、受け入れられません。

<a id="crypto-rand"></a>

### crypto/rand

<a id="TOC-CryptoRand"></a>

鍵の生成に `math/rand` パッケージを使わないでください、捨て鍵であってもです。
シードされていない場合、ジェネレータは完全に予測可能で す。
`time.Nanoseconds()` でシードした場合、ほんの数ビットのエントロピーが存在します。
代わりに `crypto/rand` の Reader を使い、テキストが必要な場合は 16 進数か base64 に出力してください。

```go
// Good:
import (
    "crypto/rand"
    // "encoding/base64"
    // "encoding/hex"
    "fmt"

    // ...
)

func Key() string {
    buf := make([]byte, 16)
    if _, err := rand.Read(buf); err != nil {
        log.Fatalf("Out of randomness, should never happen: %v", err)
    }
    return fmt.Sprintf("%x", buf)
    // or hex.EncodeToString(buf)
    // or base64.StdEncoding.EncodeToString(buf)
}
```

**Note:** `log.Fatalf` は標準ライブラリのログではありません。[#logging] を参照してください。

<a id="useful-test-failures"></a>

## Useful test failures

<a id="TOC-UsefulTestFailures"></a>

テストの失敗を、テストのソースを読まなくても診断できるようにします。
テストが失敗した場合、その詳細を示す有用なメッセージが表示されるようにします：

* 失敗の原因は何か
* どのような入力でエラーになったか
* 実際の結果
* 期待された内容

この目標を達成するための具体的な規約は以下の通りです。

<a id="assert"></a>

### Assertion libraries

<a id="TOC-Assert"></a>

テストのためのヘルパーとして「アサーション・ライブラリ」を作成しないこと。

アサーション・ライブラリは、テスト内で失敗メッセージの検証と生成を組み合わせようとするライブラリです（ただし、他のテストヘルパーにも同じ落とし穴があります）。
テストヘルパーとアサーションライブラリの区別については、[best practices] (best-practices.ja.md#test-functions) を参照してください。

```go
// Bad:
var obj BlogPost

assert.IsNotNil(t, "obj", obj)
assert.StringEq(t, "obj.Type", obj.Type, "blogPost")
assert.IntEq(t, "obj.Comments", obj.Comments, 2)
assert.StringNotEq(t, "obj.Body", obj.Body, "")
```

アサーション・ライブラリは、（`assert`が `t.Fatalf` や `panic` を呼び出した場合）テストを早期に中止するか、テストが正しく行われたかについての関連情報を省略する傾向があります：

```go
// Bad:
package assert

func IsNotNil(t *testing.T, name string, val interface{}) {
    if val == nil {
        t.Fatalf("data %s = nil, want not nil", name)
    }
}

func StringEq(t *testing.T, name, got, want string) {
    if got != want {
        t.Fatalf("data %s = %q, want %q", name, got, want)
    }
}
```

複雑なアサーション関数では、テスト関数内に存在する[useful failure messages] (有用な失敗メッセージ) やコンテキストが提供されないことがよくあります。
アサーション関数やライブラリの数が多すぎると、開発者の体験が断片的になってしまいます：どのアサーション・ライブラリを使うべきか、どのようなスタイルの出力形式を出すべきか、などです。
特に、ライブラリの保守担当者や大規模な変更の作成者は、下流で起こりうる不具合を修正する責任を負うことになるため、断片化は不必要な混乱をもたらします。
テスト用にドメイン固有の言語を作成する代わりに、Go自体を使用することです。

アサーションライブラリは、比較や等号のチェックを省略していることがあります。
代わりに [`cmp`] や [`fmt`] のような標準的なライブラリを使用することをお勧めします：

```go
// Good:
var got BlogPost

want := BlogPost{
    Comments: 2,
    Body:     "Hello, world!",
}

if !cmp.Equal(got, want) {
    t.Errorf("blog post = %v, want = %v", got, want)
}
```

よりドメインに特化した比較ヘルパーでは、`*testing.T`を渡してそのエラー報告メソッドを呼び出すのではなく、テストの失敗メッセージで使用できる値やエラーを返すことをお勧めします：

```go
// Good:
func postLength(p BlogPost) int { return len(p.Body) }

func TestBlogPost_VeritableRant(t *testing.T) {
    post := BlogPost{Body: "I am Gunnery Sergeant Hartman, your senior drill instructor."}

    if got, want := postLength(post), 60; got != want {
        t.Errorf("length of post = %v, want %v", got, want)
    }
}
```

**Best Practice:**
もし `postLength` が自明でないなら、それを使用するテストとは別に、直接テストするのが理にかなっているはずです。

こちらもご参照ください：

* [Equality comparison and diffs](#types-of-equality)
* [Print diffs](#print-diffs)
* For more on the distinction between test helpers and assertion helpers, see [best practices](best-practices.ja.md#test-functions)

[useful failure messages]: #useful-test-failures
[`fmt`]: https://golang.org/pkg/fmt/
[marking test helpers]: #mark-test-helpers

<a id="identify-the-function"></a>

### Identify the function

ほとんどのテストでは、失敗のメッセージには失敗した関数の名前を含めるべきです、たとえそれがテスト関数の名前から明白に思えるとしてもです。
具体的には、失敗のメッセージは「%vを得た、%vが欲しい」ではなく、「YourFunc(%v) = %v, want %v`」とすべきです。

<a id="identify-the-input"></a>

### Identify the input

ほとんどのテストでは、失敗メッセージには、関数入力が短い場合は、その入力を含める必要があります。
入力の関連する性質が明らかでない場合（例えば、入力が大きい、不透明であるなど）、テストケースにテスト対象の説明を付けて名前を付け、エラーメッセージの一部としてその説明を表示する必要があります。

<a id="got-before-want"></a>

### Got before want

テスト出力には、期待された値を表示する前に、関数が実際に返した値を含める必要があります。
テスト出力を表示するための標準的なフォーマットは `YourFunc(%v) = %v, want %v` です。
"actual" と "expected" と書くところ、それぞれ "got" と "want" という言葉を使うのが好ましいです。

差分の場合、方向性はあまりはっきりしないので、失敗を解釈するためのキーを含めることが重要です。
[section on printing diffs] (差分の表示に関するセクション) を参照してください。
既存のコードでは順序に一貫性がないため、失敗メッセージでどのようなdiffの順序を使用するにしても、失敗メッセージの一部として明示的に示す必要があります。

[section on printing diffs]: #print-diffs

<a id="compare-full-structures"></a>

### Full structure comparisons

関数が構造体（またはスライス、配列、マップなど複数のフィールドを持つデータ型）を返す場合、構造体のフィールドごとの比較を手作業で行うテスト コードを書くのは避けてください。
代わりに、関数が返すと予想されるデータを構築し、[deep comparison] (深い比較) を使用して直接比較します。

**Note:**
ただし、データに無関係なフィールドがあり、テストの意図が不明瞭な場合はこの限りではありません。

構造体を近似的に（または同等の意味で）等しく比較する必要がある場合や、等しく比較できないフィールドを含む場合（フィールドの 1 つが `io.Reader` の場合など）、[`cmpopts.IgnoreInterfaces`] などの [`cmpopts`] オプションで [`cmp.Diff`] または [`cmp.Equal`] 比較を調整すればニーズを満たすことができます ([example](https://play.golang.org/p/vrCUNVfxsvF)).

関数が複数の戻り値を返す場合、それらを構造体で包んでから比較する必要はありません。
返り値を個別に比較して表示すればよいのです。

```go
// Good:
val, multi, tail, err := strconv.UnquoteChar(`\"Fran & Freddie's Diner\"`, '"')
if err != nil {
  t.Fatalf(...)
}
if val != `"` {
  t.Errorf(...)
}
if multi {
  t.Errorf(...)
}
if tail != `Fran & Freddie's Diner"` {
  t.Errorf(...)
}
```

[deep comparison]: #types-of-equality
[`cmpopts`]: https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts
[`cmpopts.IgnoreInterfaces`]: https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts#IgnoreInterfaces

<a id="compare-stable-results"></a>

### Compare stable results

所有していないパッケージの出力安定性に依存する可能性のある結果を比較することは避けてください。
その代わりに、テストは、安定していて依存関係の変更に強い、意味的に関連する情報で比較する必要があります。
フォーマットされた文字列やシリアル化されたバイトを返す機能については、一般的に、出力が安定していると仮定するのは安全ではありません。

例えば、[`json.Marshal`]は、それが発する特定のバイトを変更することができます（過去に変更したこともあります）。
JSON 文字列に対して文字列の等式を実行するテストは、`json` パッケージがバイトをシリアライズする方法を変更した場合に壊れる可能性があります。
その代わりに、より堅牢なテストでは、JSON 文字列の内容を解析して、それが期待されるデータ構造と意味的に等価であることを確認します。

[`json.Marshal`]: https://golang.org/pkg/encoding/json/#Marshal

<a id="keep-going"></a>

### Keep going

テストは、1回の実行で失敗したチェックをすべて出力するために、失敗した後でもできるだけ長く続けなければなりません。
こうすれば、失敗したテストを修正している開発者は、バグを修正するたびに次のバグを見つけるためにテストを再実行する必要がなくなります。

ミスマッチを報告する場合は、`t.Fatal`よりも`t.Error`を優先的に呼び出します。
関数の出力の複数の異なるプロパティを比較する場合、それぞれの比較に `t.Error` を使用します。

`t.Fatal`の呼び出しは、主に予期せぬエラー状態を報告するのに役立ち、その後の比較の失敗が意味をなさないような場合です。

テーブル駆動テストでは、サブテストの使用を検討し、`t.Error` と `continue` ではなく、`t.Fatal` を使用します。 [GoTip #25: Subtests: Making Your Tests Lean](https://google.github.io/styleguide/go/index.html#gotip) も参照してください。

**Best practice:**
`t.Fatal`をどのような場合に使用すべきかについては [best practices](best-practices.ja.md#t-fatal) を参照してください。

<a id="types-of-equality"></a>

### Equality comparison and diffs

演算子 `==` は、[language-defined comparisons] (言語で定義された比較) を使って等式を評価します。
スカラー値（数値、ブーリアンなど）は、その値に基づいて比較されますが、この方法で比較できるのは一部の構造体とインターフェースだけです。
ポインターは、指し示す値の等しさではなく、同じ変数を指しているかどうかで比較されます。

[`cmp`] パッケージは、スライスのような `==` では適切に扱えない複雑なデータ構造の比較を行うことができます。
等価比較には [`cmp.Equal`] を使用し、オブジェクト間の差分を取得するには [`cmp.Diff`] を使用します。

```go
// Good:
want := &Doc{
    Type:     "blogPost",
    Comments: 2,
    Body:     "This is the post body.",
    Authors:  []string{"isaac", "albert", "emmy"},
}
if !cmp.Equal(got, want) {
    t.Errorf("AddPost() = %+v, want %+v", got, want)
}
```

汎用比較ライブラリである `cmp` は、特定の型を比較する方法を知らないことがあります。
例えば、[`protocmp.Transform`]オプションが渡された場合のみ、プロトコルバッファメッセージを比較することができます。

<!-- The order of want and got here is deliberate. See comment in #print-diffs. -->

```go
// Good:
if diff := cmp.Diff(want, got, protocmp.Transform()); diff != "" {
    t.Errorf("Foo() returned unexpected difference in protobuf messages (-want +got):\n%s", diff)
}
```

`cmp` パッケージは Go 標準ライブラリの一部ではありませんが、Go チームによってメンテナンスされているため、長期間にわたって安定した等式結果を得ることができるはずです。
このパッケージはユーザが設定可能で、ほとんどの比較のニーズに応えられるはずです。

[language-defined comparisons]: http://golang.org/ref/spec#Comparison_operators
[`cmp`]: https://pkg.go.dev/github.com/google/go-cmp/cmp
[`cmp.Equal`]: https://pkg.go.dev/github.com/google/go-cmp/cmp#Equal
[`cmp.Diff`]: https://pkg.go.dev/github.com/google/go-cmp/cmp#Diff
[`protocmp.Transform`]: https://pkg.go.dev/google.golang.org/protobuf/testing/protocmp#Transform

既存のコードは、以下の旧ライブラリを使用している可能性があり、一貫性を保つために使用を継続することができます：

* [`pretty`] は、美的感覚に優れた差分レポートを作成します。
  しかし、視覚的な表現が同じである値については、意図的に等しいとみなしているのです。
  特に、`pretty`はnilスライスと空のスライスの違いを捉えず、同一のフィールドを持つ異なるインターフェースの実装に敏感でなく、構造体値との比較の基礎として入れ子マップを使用することが可能です。
  また、値全体を文字列にシリアライズしてから diff を生成するため、大きな値の比較には向いていません。
  デフォルトでは、エクスポートされていないフィールドを比較するため、依存関係にある実装の詳細が変更された場合に影響を受けやすくなります。このため、protobuf メッセージに `pretty` を使用することは適切ではありません。

[`pretty`]: https://pkg.go.dev/github.com/kylelemons/godebug/pretty

新しいコードには `cmp` を使用することをお勧めします。また、古いコードでも、いつどこで `cmp` を使用するように更新することが実用的であるかを検討する価値があります。

古いコードでは、複雑な構造の比較に標準ライブラリの `reflect.DeepEqual` 関数を使用することがあります。
`Reflect.DeepEqual` は、エクスポートされていないフィールドやその他の実装の詳細の変更に敏感であるため、等価性のチェックに使用するべきではありません。
`Reflect.DeepEqual` を使用しているコードは、上記のライブラリのいずれかに更新する必要があります。

**Note:**
`cmp` パッケージは、実運用ではなく、テスト用に設計されています。
そのため、比較の実行が正しくないと判断した場合にパニックを起こすことがあり、テストがより脆くならないように改善する方法をユーザーに指示することができます。
cmp のパニックの傾向を考えると、誤ったパニックが致命的となる可能性があるため、実運用で使用するコードには不向きです。

<a id="level-of-detail"></a>

### Level of detail

従来の失敗メッセージは、ほとんどのGoテストに適しており、`YourFunc(%v) = %v, want %v`です。
しかし、より詳細な情報を必要とするケースもあります：

* 複雑なインタラクションを行うテストは、インタラクションも記述する必要があります。
  例えば、同じ `YourFunc` が何度も呼ばれた場合、どの呼び出しがテストに失敗したかを明らかにします。
  システムの追加的な状態を知ることが重要であれば、失敗の出力に（あるいは少なくともログに）それを含めてください。
* データが複雑な構造で定型文が多い場合は、重要な部分のみをメッセージに記述しても構いませんが、データを過度に曖昧にしないようにしてください。
* セットアップの失敗は、同じレベルのものを必要としません。
  テストヘルパーがスパナーテーブルに入力するが、スパナーがダウンしていた場合、おそらくどのテスト入力をデータベースに保存するつもりだったかを含める必要はないでしょう。
  `t.Fatalf("Setup: Failed to set up test database: %s", err)`は通常、問題を解決するのに十分役立ちます。

**Tip:**
開発中に失敗モードのトリガーを作っておきましょう。
失敗メッセージがどのようなものか、メンテナが効果的に対処できるかを検討しましょう。

テストの入出力を明確に再現するためのテクニックがあります：

* 文字列データを出力する場合、値が重要であることを強調し、悪い値をより簡単に発見するために、[`%q` is often useful](#use-percent-q) (`%q`はしばしば有用) となります。
* (小さな) 構造体を出力する場合、`%v`よりも`%+v`の方が便利な場合があります。
* 大きな値の検証に失敗した場合、[printing a diff](#print-diffs) (diffを出力する) ことで失敗を理解しやすくすることができます。

<a id="print-diffs"></a>

### Print diffs

関数が大きな出力を返す場合、テストが失敗したときに、失敗メッセージを読む人がその違いを見つけるのは難しいかもしれません。
返された値と欲しい値の両方を表示する代わりに、差分を作成します。

このような値の差分を計算するには、特に新しいテストや新しいコードの場合は `cmp.Diff` が好ましいですが、他のツールを使うこともできます。
各機能の長所と短所に関するガイダンスについては、[types of equality] (等号の種類) を参照してください。

* [`cmp.Diff`]
* [`pretty.Compare`]

[`diff`] パッケージを使用すると、複数行の文字列や文字列のリストを比較することができます。
これを他の種類の差分のためのビルディングブロックとして使用することができます。

[types of equality]: #types-of-equality
[`diff`]: https://pkg.go.dev/github.com/kylelemons/godebug/diff
[`pretty.Compare`]: https://pkg.go.dev/github.com/kylelemons/godebug/pretty#Compare

失敗メッセージに、差分の方向を説明するテキストを追加してください。

<!--
The reversed order of want and got in these examples is intentional, as this is
the prevailing order across the Google codebase. The lack of a stance on which
order to use is also intentional, as there is no consensus which is
"most readable."
-->

* `diff (-want +got)` のようなものは、`cmp`, `pretty`, `diff` パッケージを使用している場合に適しています（関数に `(want, got)` を渡す場合）。なぜなら、フォーマット文字列に追加した `-` と `+` が、実際に diff 行頭で現れる `-` と `+` と一致するからです。
  関数に `(got, want)` を渡すと、代わりに `(-got +want)` が正しいキーとなります。

* `messagediff` パッケージは別の出力形式を使うので、それを使う場合（関数に `(want, got)` を渡す場合）、矢印の方向が「変更」行の矢印の方向と一致するため、`diff (want -> got)` というメッセージが適切です。

diff は複数行にまたがるので、diff を表示する前に改行をしておくとよいでしょう。

<a id="test-error-semantics"></a>

### Test error semantics

ユニットテストで文字列比較を行ったり、バニラの `cmp` を使って特定の入力に対して特定の種類のエラーが返されることをチェックしたりすると、将来これらのエラーメッセージが言い換えられたときに、テストがもろくなることがあります。
これはユニットテストを変更検出器にしてしまう可能性があるので (参照 [TotT: Change-Detector Tests Conside Harmful][tott-350] )、関数が返すエラーの種類をチェックするために文字列比較を使わないでください。
しかし、テスト対象のパッケージから送られてくるエラーメッセージが、ある特性を満たしているかどうかをチェックするために文字列比較を使うことは許されています。

Goのエラー値には、通常、人間の目を意識した成分と、意味的な制御フローを意識した成分があります。
テストは、人間のデバッグを目的とした情報を表示するのではなく、確実に観察できる意味的な情報のみをテストするように努めるべきです、これは将来的に変更されることが多いからです。
意味的な意味を持つエラーを作成するためのガイダンスについては、[best-practices regarding errors](best-practices.ja.md#error-handling) をご覧ください。
意味的な情報が不十分なエラーが自分のコントロール外の依存関係から発生している場合、エラーメッセージの解析に頼るのではなく、APIを改善するためにその所有者に対してバグを提出することを検討してください。

ユニットテストでは、エラーが発生したかどうかだけを気にすることがよくあります。
その場合は、エラーが発生すると予想されるときに、エラーがnon-nilであるかどうかだけをテストすれば十分です。
もし、エラーが他のエラーと意味的に一致することをテストしたい場合は、`cmp` と [`cmpopts.EquateErrors`] を使用することを検討してください。

> **Note:**
> テストが [`cmpopts.EquateErrors`] を使用しているが、その `wantErr` の値がすべて `nil` または `cmpopts.AnyError` である場合、`cmp` の使用は [unnecessary mechanism] (guide.ja.md#least-mechanism)  (不要な機構) です。
> wantフィールドを`bool`にすることで、コードを簡略化することができます。
> そして、`!=`で単純な比較をすることができます。
>
> ```go
> // Good:
> gotErr := f(test.input) != nil
> if gotErr != test.wantErr {
>     t.Errorf("f(%q) returned err = %v, want error presence = %v", test.input, gotErr, test.wantErr)
> }
> ```

[GoTip #13: Designing Errors for Checking](https://google.github.io/styleguide/go/index.html#gotip) も参照してください。

[tott-350]: https://testing.googleblog.com/2015/01/testing-on-toilet-change-detector-tests.html
[`cmpopts.EquateErrors`]: https://pkg.go.dev/github.com/google/go-cmp/cmp/cmpopts#EquateErrors

<a id="test-structure"></a>

## Test structure

<a id="subtests"></a>

### Subtests

標準のGoテストライブラリには、[define subtests] (サブテストを定義する) 機能があります。
これにより、セットアップとクリーンアップ、並列性の制御、テストのフィルタリングを柔軟に行うことができます。
サブテストは便利ですが (特にテーブル駆動型テストに)、使用は必須ではありません。
サブテストに関するGoブログの記事](https://blog.golang.org/subtests)も参照してください。

サブテストは、`go test -run` フラグや Bazel [test filter] 式を使って個別に実行できることが期待されるため、成功や初期状態について他のケースの実行に依存してはいけません。

[define subtests]: https://pkg.go.dev/testing#hdr-Subtests_and_Sub_benchmarks
[test filter]: https://bazel.build/docs/user-manual#test-filter

<a id="subtest-names"></a>

#### Subtest names

サブテストの名前は、テスト出力で読みやすく、テストフィルタリングのユーザーにとってコマンドラインで便利なようにします。
t.Run`を使用してサブテストを作成する場合、最初の引数はテストの説明的な名前として使用されます。
テスト結果がログを読む人間にとって読みやすいものであることを保証するために、エスケープした後も有用で読みやすいサブテスト名を選びます。
サブテスト名は、散文的な説明というよりも、関数の識別子のようなものだと考えてください。
テストランナーは、スペースをアンダースコアに置き換え、印字されない文字をエスケープします。
もしテストデータに長い説明が必要な場合は、説明を別のフィールドに書くことを検討してください (おそらく `t.Log` を使って出力するか、失敗メッセージと一緒に出力することになるでしょう)。

サブテストは、[Go test runner]や Bazel [test filter]のフラグを使って個別に実行することができるので、入力しやすい説明的な名前を選びましょう。

> **Warning:**
> スラッシュ文字は、[special meaning for test filters] (テストフィルタに特別な意味) を持つため、サブテスト名には特に不向きです。
>
> > ```sh
> > # Bad:
> > # Assuming TestTime and t.Run("America/New_York", ...)
> > bazel test :mytest --test_filter="Time/New_York"    # Runs nothing!
> > bazel test :mytest --test_filter="Time//New_York"   # Correct, but awkward.
> > ```

[identify the inputs] (関数の入力を特定) するには、テストの失敗メッセージの中に、テストランナーによってエスケープされないような入力を入れてください。

```go
// Good:
func TestTranslate(t *testing.T) {
    data := []struct {
        name, desc, srcLang, dstLang, srcText, wantDstText string
    }{
        {
            name:        "hu=en_bug-1234",
            desc:        "regression test following bug 1234. contact: cleese",
            srcLang:     "hu",
            srcText:     "cigarettát és egy öngyújtót kérek",
            dstLang:     "en",
            wantDstText: "cigarettes and a lighter please",
        }, // ...
    }
    for _, d := range data {
        t.Run(d.name, func(t *testing.T) {
            got := Translate(d.srcLang, d.dstLang, d.srcText)
            if got != d.wantDstText {
                t.Errorf("%s\nTranslate(%q, %q, %q) = %q, want %q",
                    d.desc, d.srcLang, d.dstLang, d.srcText, got, d.wantDstText)
            }
        })
    }
}
```

ここでは、避けるべきものの例をいくつか紹介します：

```go
// Bad:
// Too wordy.
t.Run("check that there is no mention of scratched records or hovercrafts", ...)
// Slashes cause problems on the command line.
t.Run("AM/PM confusion", ...)
```

[Go test runner]: https://golang.org/cmd/go/#hdr-Testing_flags
[identify the inputs]: #identify-the-input
[special meaning for test filters]: https://blog.golang.org/subtests#:~:text=Perhaps%20a%20bit,match%20any%20tests

<a id="table-driven-tests"></a>

### Table-driven tests

同じようなテストロジックで多くの異なるテストケースをテストできる場合は、テーブル駆動型テストを使用します。

* ある関数の実際の出力が期待される出力と等しいかどうかをテストする場合。
  例えば、多くの[tests of `fmt.Sprintf`] (`fmt.Sprintf` のテスト) や以下の最小限のスニペットです。
* ある関数の出力が常に同じ不変条件セットに適合しているかどうかをテストする場合。
  例えば、[tests for `net.Dial`].

[tests of `fmt.Sprintf`]: https://cs.opensource.google/go/go/+/master:src/fmt/fmt_test.go
[tests for `net.Dial`]: https://cs.opensource.google/go/go/+/master:src/net/dial_test.go;l=318;drc=5b606a9d2b7649532fe25794fa6b99bd24e7697c

ここでは、標準的な `strings` ライブラリからコピーした、テーブル駆動型テストの最小構造を示します。
必要に応じて、異なる名前を使ったり、テストスライスをテスト関数に移動したり、サブテストやセットアップ・クリーンアップ関数のような余分な機能を追加することができます。
常に [useful test failures](#useful-test-failures) を念頭においてください。

```go
// Good:
var compareTests = []struct {
    a, b string
    i    int
}{
    {"", "", 0},
    {"a", "", 1},
    {"", "a", -1},
    {"abc", "abc", 0},
    {"ab", "abc", -1},
    {"abc", "ab", 1},
    {"x", "ab", 1},
    {"ab", "x", -1},
    {"x", "a", 1},
    {"b", "x", -1},
    // test runtime·memeq's chunked implementation
    {"abcdefgh", "abcdefgh", 0},
    {"abcdefghi", "abcdefghi", 0},
    {"abcdefghi", "abcdefghj", -1},
}

func TestCompare(t *testing.T) {
    for _, tt := range compareTests {
        cmp := Compare(tt.a, tt.b)
        if cmp != tt.i {
            t.Errorf(`Compare(%q, %q) = %v`, tt.a, tt.b, cmp)
        }
    }
}
```

**Note**:
この例の失敗メッセージは、「identify the function」(#identify-the-function) (機能を特定する) と「identify the input」(#identify-the-input) (入力を特定する) というガイダンスを満たしています。
[identify the row numerically](#table-tests-identifying-the-row) (行を数値で識別する) 必要はありません。

あるテストケースが他のテストケースと異なるロジックでチェックされる必要がある場合、[GoTip #50: Disjoint Table Tests] で説明したように、複数のテスト関数を書く方が適切です。
テーブルの各エントリに、入力に対する出力をチェックするための異なる条件ロジックがあると、テストコードのロジックが理解しづらくなることがあります。もしテストケースが異なるロジックを持つ場合でも
同じ設定であれば、1つのテスト関数の中に [subtests](#subtests) (サブテスト) の列があってもよいかもしれません。

テーブル駆動テストは、複数のテスト関数と組み合わせることができます。
例えば、ある関数の出力が期待される出力と完全に一致することと、無効な入力に対して関数が nil でないエラーを返すことをテストする場合、2つの別々のテーブル駆動テスト関数を書くことが最適な方法です：一つはエラーでない通常の出力用、一つはエラー出力用です。

[GoTip #50: Disjoint Table Tests]: https://google.github.io/styleguide/go/index.html#gotip

<a id="table-tests-data-driven"></a>

#### Data-driven test cases

テーブルテストの行は、行の値がテストケース内の条件動作を決定するため、時に複雑になることがあります。
テストケース間の重複による余計なわかりやすさは、読みやすさのために必要です。

```go
// Good:
type decodeCase struct {
    name   string
    input  string
    output string
    err    error
}

func TestDecode(t *testing.T) {
    // setupCodex is slow as it creates a real Codex for the test.
    codex := setupCodex(t)

    var tests []decodeCase // rows omitted for brevity

    for _, test := range tests {
        t.Run(test.name, func(t *testing.T) {
            output, err := Decode(test.input, codex)
            if got, want := output, test.output; got != want {
                t.Errorf("Decode(%q) = %v, want %v", test.input, got, want)
            }
            if got, want := err, test.err; !cmp.Equal(got, want) {
                t.Errorf("Decode(%q) err %q, want %q", test.input, got, want)
            }
        })
    }
}

func TestDecodeWithFake(t *testing.T) {
    // A fakeCodex is a fast approximation of a real Codex.
    codex := newFakeCodex()

    var tests []decodeCase // rows omitted for brevity

    for _, test := range tests {
        t.Run(test.name, func(t *testing.T) {
            output, err := Decode(test.input, codex)
            if got, want := output, test.output; got != want {
                t.Errorf("Decode(%q) = %v, want %v", test.input, got, want)
            }
            if got, want := err, test.err; !cmp.Equal(got, want) {
                t.Errorf("Decode(%q) err %q, want %q", test.input, got, want)
            }
        })
    }
}
```

以下の反例では、テストケースごとにどのタイプの `Codex` が使われているかを、ケースセットアップで区別するのがいかに難しいかに注目してください。(ハイライトした部分は、[TotT: Data Driven Traps!][tott-97] のアドバイスに反しています)。

```go
// Bad:
type decodeCase struct {
  name   string
  input  string
  codex  testCodex
  output string
  err    error
}

type testCodex int

const (
  fake testCodex = iota
  prod
)

func TestDecode(t *testing.T) {
  var tests []decodeCase // rows omitted for brevity

  for _, test := tests {
    t.Run(test.name, func(t *testing.T) {
      var codex Codex
      switch test.codex {
      case fake:
        codex = newFakeCodex()
      case prod:
        codex = setupCodex(t)
      default:
        t.Fatalf("unknown codex type: %v", codex)
      }
      output, err := Decode(test.input, codex)
      if got, want := output, test.output; got != want {
        t.Errorf("Decode(%q) = %q, want %q", test.input, got, want)
      }
      if got, want := err, test.err; !cmp.Equal(got, want) {
        t.Errorf("Decode(%q) err %q, want %q", test.input, got, want)
      }
    })
  }
}
```

[tott-97]: https://testing.googleblog.com/2008/09/tott-data-driven-traps.html

<a id="table-tests-identifying-the-row"></a>

#### Identifying the row

テストテーブルの中のテストのインデックスを、テストの名前や入力を出力するための代用品として使用しないでください。
どのテストケースが失敗しているかを知るために、テストテーブルを調べて項目を数えるのは、誰もやりたくありません。

```go
// Bad:
tests := []struct {
    input, want string
}{
    {"hello", "HELLO"},
    {"wORld", "WORLD"},
}
for i, d := range tests {
    if strings.ToUpper(d.input) != d.want {
        t.Errorf("failed on case #%d", i)
    }
}
```

テスト構造体にテストの説明を追加し、失敗メッセージとともに出力します。
サブテストを使用する場合、サブテスト名は行を識別するのに有効であるべきです。

**Important:**
たとえ `t.Run` が出力と実行をスコープしていても、常に [identify the input] (入力を特定) しなければなりません。
テーブルテストの行名は、[subtest naming] (サブテストの命名) のガイダンスに従わなければなりません。

[identify the input]: #identify-the-input
[subtest naming]: #subtest-names

<a id="mark-test-helpers"></a>

### Test helpers

テストヘルパーは、セットアップやクリーンアップのタスクを実行する関数です。
テストヘルパーで発生するすべての失敗は、（テスト対象のコードではなく）環境の失敗であると予想されます。例えば、このマシンにはもう空きポートがないため、テスト用データベースを開始できない場合などです。

もし `*testing.T` を渡した場合、[`t.Helper`] を呼び出して、テストヘルパーの失敗をヘルパーが呼び出された行に帰着させます。
このパラメータは、[context](#contexts)パラメータがある場合はその後に、残りのパラメータがある場合はその前に置く必要があります。

```go
// Good:
func TestSomeFunction(t *testing.T) {
    golden := readFile(t, "testdata/golden-result.txt")
    // ... tests against golden ...
}

// readFile returns the contents of a data file.
// It must only be called from the same goroutine as started the test.
func readFile(t *testing.T, filename string) string {
    t.Helper()
    contents, err := runfiles.ReadFile(filename)
    if err != nil {
        t.Fatal(err)
    }
    return string(contents)
}
```

このパターンは、テストの失敗とその原因となった条件との関連性を不明瞭にする場合には使用しないでください。
特に、[assert libraries](#assert) に関するガイダンスはまだ適用され、[`t.Helper`] はそのようなライブラリの実装に使用すべきではありません。

**Tip:**
テストヘルパーとアサーションヘルパーの区別については、[best practices](best-practices.ja.md#test-functions)を参照してください。

上記は `*testing.T` の話ですが、ベンチマークや ファズヘルパーについても同様のことが言えます。

[`t.Helper`]: https://pkg.go.dev/testing#T.Helper

<a id="test-package"></a>

### Test package

<a id="TOC-TestPackage"></a>

<a id="test-same-package"></a>

#### Tests in the same package

テストは、テストされるコードと同じパッケージで定義することができます。

同じパッケージでテストを書く場合：

* テストを `foo_test.go` ファイルに配置する。
* テストファイルに `package foo` を使用する。
* テスト対象のパッケージを明示的にインポートしない

```build
# Good:
go_library(
    name = "foo",
    srcs = ["foo.go"],
    deps = [
        ...
    ],
)

go_test(
    name = "foo_test",
    size = "small",
    srcs = ["foo_test.go"],
    library = ":foo",
    deps = [
        ...
    ],
)
```

同じパッケージ内のテストは、パッケージ内のエクスポートされていない識別子にアクセスすることができます。
これにより、より良いテストカバレッジと、より簡潔なテストが可能になるかもしれません。
テスト内で宣言された [examples] は、ユーザーがコードで必要とするパッケージ名を持っていないことに注意してください。

[`library`]: https://github.com/bazelbuild/rules_go/blob/master/docs/go/core/rules.md#go_library
[examples]: #examples

<a id="test-different-package"></a>

#### Tests in a different package

テストされるコードと同じパッケージでテストを定義することは、必ずしも適切とは限りません。
このような場合は、パッケージ名に `_test` という接尾辞を付けてください。
これは、[package names](#package-names) (パッケージ名) の「アンダースコアなし」ルールの例外となります。
例えば、以下のようになります：

* 結合テストが所属するライブラリが明らかでない場合

    ```go
    // Good:
    package gmailintegration_test

    import "testing"
    ```

* 同じパッケージでテストを定義すると、循環依存になる場合

    ```go
    // Good:
    package fireworks_test

    import (
      "fireworks"
      "fireworkstestutil" // fireworkstestutil also imports fireworks
    )
    ```

<a id="use-package-testing"></a>

### Use package `testing`

Go の標準ライブラリには、[`testing` package] が用意されています。
これは、Google コードベースの Go コードに許可されている唯一のテストフレームワークです。
特に、[assertion libraries](#assert) (アサーションライブラリ) やサードパーティ製のテストフレームワークは許可されていません。

`testing`パッケージは、良いテストを書くための最小限の、しかし完全な機能セットを提供します：

* トップレベルテスト
* ベンチマーク
* [Runnable examples](https://blog.golang.org/examples) (実行可能な例)
* サブテスト
* ロギング
* 失敗と致命的な失敗

These are designed to work cohesively with core language features like [composite literal] and [if-with-initializer] syntax to enable test authors to write [].

これらは、「[composite literal] (複合リテラル)」や「[if-with-initializer]」構文などの中核となる言語機能と協調して動作し、テスト作成者が「[clear, readable, and maintainable tests] (明確で、読みやすく、保守性の高いテスト)」を書くことができるように設計されています。

[`testing` package]: https://pkg.go.dev/testing
[composite literal]: https://go.dev/ref/spec#Composite_literals
[if-with-initializer]: https://go.dev/ref/spec#If_statements

<a id="non-decisions"></a>

## Non-decisions

スタイルガイドは、すべての事柄について肯定的な処方を列挙することはできませんし、意見を述べないすべての事柄を列挙することもできません。
とはいえ、ここでは、読みやすさのコミュニティが以前から議論し、コンセンサスを得られていない事柄をいくつか紹介します。

* **ローカル変数のゼロ値での初期化**: `var i int` と `i := 0` は等価です。
  [initialization best practices] (初期化のベストプラクティス) も参照してください。
* **空の複合リテラルと `new` または `make` の比較**: `&File{}` と `new(File)` は等価です。
  `map[string]bool{}` と `make(map[string]bool)` も同様です。
  [composite declaration best practices] (複合宣言のベストプラクティス) も参照してください。
* **`cmp.Diff` の呼び出しで引数の順序を変えたい**: ローカルに一貫性を持たせ、失敗メッセージには [include a legend](#print-diffs) (凡例) を入れてください。
* **書式なし文字列の `errors.New` と `fmt.Errorf` の比較**:
  `errors.New("foo")` と `fmt.Errorf("foo")` は同じ意味で使用することができます。

もし、それらが再び出てくるような特別な状況があれば、読みやすさのメンターがオプションでコメントをつけるかもしれませんが、一般的には、著者は与えられた状況で好きなスタイルを自由に選ぶことができます。

もちろん、スタイルガイドに記載されていないことで、さらに議論が必要なことがあれば、著者の方は、具体的なレビューや社内掲示板で質問していただいて結構です。

[composite declaration best practices]: https://google.github.io/styleguide/go/best-practices#vardeclcomposite
[initialization best practices]: https://google.github.io/styleguide/go/best-practices#vardeclinitialization

{% endraw %}

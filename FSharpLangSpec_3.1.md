# F# 3.1 言語仕様(ワーキングドラフト)

> 原文： http://fsharp.org/specs/language-spec/3.1/FSharpSpec-3.1-working.docx

> 注釈：この文章はMicrosoftResearchおよびMicrosoftDeveloperDivisionによって
> 2013年6月に作成された、F# 3.1リリース向けの言語仕様です。

この言語仕様には3.1の実装と一致しない箇所がある場合があります。
文章中ではそれらの該当箇所にコメントの形でなるべく注釈を加えてあります。
言及のない不一致を見つけた場合は是非連絡してください。
そうすれば将来リリースされる言語仕様ではそれとわかるようにさせていただきます。
F#の開発チームはいつでもこの言語仕様、およびF#の設計や実装に対する
フィードバックを歓迎します。
フィードバックを送信するには、
http://github.com/fsharp/fsfoundation/docs/language-spec
にIssueをオープンしたり、コメントを追加したり、
Pull Requestを送信したりといった方法があります。

言語仕様の最新版は [fsharp.org](http://fsharp.org/) にあります。
これまでのドキュメントに対するF# ユーザーコミュニティからのフィードバックには
大変感謝しています。

この仕様書の一部ではC# 4.0やUnicode、IEEEといった仕様への言及があります。

**著者：**
Don Syme および補佐として Anar Alimov, Keith Battocchi, Jomo Fisher,
Michael Hale, Jack Hu, Luke Hoban, Tao Liu, Dmitry Lomov,
James Margetson, Brian McNamara, Joe Pamer, Penny Orwick,
Daniel Quirk, Kevin Ransom, Chris Smith, Matteo Taveggia,
Donna Malayeri, Wonseok Chae, Uladzimir Matsveyeu, Lincoln Atkinson
等による。

**警告**

_© 2005-2013 Microsoft Corporation and contributors. [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0.html)ライセンスの下に利用出来ます。_

_Microsoft, Windows, Visual F#はアメリカ合衆国 Microsoft Corporationの商標、あるいはアメリカ合衆国内外における登録商標です。_

_文章内で言及されるその他の製品や会社名にはそれぞれ固有の所有者が存在する場合があります。_

**更新履歴**

* 2014年 5月 バージョン番号をF# 3.1に変更
* 2013年 6月 F# 3.1用の最初の更新 ([言語のアップデートに関するオンラインの議論](http://blogs.msdn.com/b/fsharpteam/archive/2013/06/27/announcing-a-pre-release-of-f-3-1-and-the-visual-f-tools-in-visual-studio-2013.aspx)を参照)
* 2012年 9月 F# 3.0用の更新
* 2012年 8月 書式の更新
* 2011年12月 文法の要約を更新
* 2011年 2月 用語集と索引の更新、書式の修正
* 2010年 8月 用語集と索引の更新、書式の修正

## 目次

// TODO

## 1. イントロダクション

F#はスケーラブルで簡潔かつ型安全で、型推論も行われ、実行効率もよい
関数型/宣言型/オブジェクト指向のプログラミング言語です。
この言語は.NET FrameworkおよびEcma 335 Common Language Infrastructure(CLI: 共通言語基盤)
の実装上で動作する、最初の型付き関数型プログラミング言語となるものです。
F#は部分的にOCaml言語から影響を受けており、一般的なコア部分においてOCamlと共通するものもあります。

### 1.1 最初のプログラム

以降のセクションでは、小さなF#プログラムを紹介して、
F#の重要な機能を説明していきます。
F#の最初の一歩として、以下のプログラムを見てみましょう：

```fsharp
let numbers = [ 1.. 10 ]

let square x = x * x

let squares = List.map square numbers

printfn "N^s = %A" squares
```

このプログラムは以下の方法で試すことができます：

* Visual Studioなどの開発環境上にて、プロジェクトとしてコンパイルする
* F#のコマンドラインコンパイラ`fsc.exe`を手動で実行する
* F#の配布物に同梱されている、動的コンパイラであるF# Interactiveを使用する

#### 1.1.1 軽量構文

F#言語では、軽量構文と呼ばれる、単純かつインデント重視の構文機能を使用します。
先ほどのセクションにあったサンプルのプログラムには一連の宣言文が並んでおり、
いずれも同じ列に揃えられていました。
たとえば以下のコードの2行はそれぞれ別の宣言文です：

```fsharp
let squares = List.map square numbers

printfn "N^2 = %A" squares
```

軽量構文はF#の構文の主要な部分すべてにおいて通用します。
次の例にあるコードは正しく整列されていません。
宣言文は1行目から始まって2行目以降に続いています。
そのため、2行目以降は同じ列に整列しなければいけません：

```text
let computeDerivative f x =
    let p1 = f (x - 0.05)
  let p2 = f (x + 0.05)
       (p2 - p1) / 0.1
```

正しく整列すると以下のようになります：

```fsharp
let computeDerivative f x =
    let p1 = f (x - 0.05)
    let p2 = f (x + 0.05)
    (p2 - p1) / 0.1
```

軽量構文は`.fs`, `.fsx`, `.fsi`, `.fsscript`の拡張子を持ったF#コードファイルにおける標準の構文です。

#### 1.1.2 データを簡単にする

サンプルの1行目では、単に1から10までの数のリストを宣言しています。

```fsharp
let numbers = [1 .. 10]
```

F#のリストは不変のリンクリストで、これは関数型プログラミングにおいて拡張可能なデータ型として使用されるものです。
この型には、リストの先頭に1つの要素を追加する`::`演算子や、
2つのリストを連結する`@`演算子といったものが用意されています。
F# Interactive上でこれらの演算子を試してみると以下のようになります：

```text
> let vowels = ['e'; 'i'; 'o'; 'u'];;
val vowels: char list = ['e'; 'i'; 'o'; 'u']

> ['a'] @ vowels;;
val it: char list = ['a'; 'e'; 'i'; 'o'; 'u']

> vowels @ ['y'];;
val it: char list = ['e'; 'i'; 'o'; 'u'; 'y']
```

F# Interactive上では2つのセミコロンで行が区切られることに注意してください。
また、それぞれの結果が変数ではなく不変の値だということを表す
_val_ という文字がF# Interactive上で出力されている点にも注意してください。

F#にはモデル化処理やデータ操作を簡単に行うことが出来るように、
タプルやオプション、レコード、共用体、シーケンス式といった、
きわめて効率のよいテクニックをサポートする機能が揃えられています。
タプルは順序つきの値のコレクションで、アトミックな単位で扱う事ができます。
多くの言語において、関連のある一連の値を1つのエンティティとしてあちこちに渡したい場合、
クラスやレコードなどのような名前の付いた型を用意して、そこに値を格納することになります。
一方、タプルを利用する場合には関連する値をグループとしてまとめるだけでよく、
新しく型を用意する必要はありません。

タプルは各コンポーネントをカンマ区切りに記述して定義します。

```text
> let tuple = (1, false, "text");;
val tuple : int * bool * string = (1, false, "text")

> let getNumberInfo (x : int) = (x, x.ToString(), x * x);;
val getNumberInfo : int -> int * string *int

> getNumberInfo 42;;
val it : int * string * int = (42, "42", 1764)
```

F#の重要なコンセプトは**不変性(immutability)**です。
タプルやリストはF#における多数の不変な型のうちの1つです。
実際、F#における多くの要素が標準的には不変です。
不変性とは、一度値が作成されてそれに名前が付けられると、
その名前に関連づけられた値を変えることができないということです。
不変性には利点がいくつかあります。
最も顕著なものは、いくつかのパターンのバグを防ぐ事ができること、
そして不変なデータは本質的にスレッドセーフであり、
並列処理を行うコードを簡単に記述できるようになるという点です。

#### 1.1.3 型を単純にする

サンプルプログラムの次の行では、入力値の2乗を計算する `square`
という名前の関数を定義しています。

```fsharp
let square x = x * x
```

静的型付け言語の多くは、関数を定義する際に型情報を明示的に指定する必要があります。
しかしF#の場合、基本的には型情報が推論されるようになっています。
この処理は**型推論**と呼ばれます。

型のシグネチャから、F#は `square` が `x` という名前の引数を1つとり、
`x * x` を返す関数だと認識します。
F#では、関数本体において最後に評価されたものが関数の返り値になります。
したがってこのコードにも \*return\* キーワードが出てきません。
多くのプリミティブ型( `byte` や `uint64` `double` など)において
積算(`*`)がサポートされています。
しかしF#において数値演算を行う場合、標準では `int` として型が推論されます。

F#はたいていの場合には自動的に型を推論するようになっていますが、
場合によっては明示的に型を指定する必要もあります。
たとえば以下のコードでは引数の1つに型注釈を指定して、
入力値の型をコンパイラに伝えています：

```text
> let concat (x : string) y = x + y;;
val concat : string -> string -> string
```

`x` が `string` 型だと宣言されていて、左辺に `string` 型をとる
`+` 演算子は右辺にも `string` 型をとるため、F# コンパイラによって
`y` も同じく `string` 型だと推論されます。
したがって `x + y` の結果は連結された文字列になります。
型注釈が無かった場合、F# コンパイラはどのバージョンの `+` を使うべきか
判断できないため、標準の動作として `int` 型のデータだとみなします。

型推論の処理によって、宣言が**自動汎化(automatic generalization)**
される場合もあります。
つまり可能であれば型が**ジェネリック**型になり、
多数の型を対象とすることができるコードとして定義されるということです。
たとえば以下のコードでは2つの値が入れ換えられている、新しいタプル型を返します：

```text
> let swap (x, y) = (y, x);;
val swap : 'a * 'b -> 'b -> 'a

> swap (1, 2);;
val it : int * int = (2, 1)

> swap ("you", true);;
val it : bool * string = (true, "you")
```

この`swap`はジェネリック関数で、`'a`と`'b`は**型変数**、
つまりジェネリックコードにおける型のプレースホルダーです。
型推論と自動汎化により、再利用可能なコードを
非常に単純に記述できるようになります。

#### 1.1.4 関数型プログラミング

サンプルプログラムでは、続けて`numbers`という整数のリストと
`square`関数、そしてリストの各要素に対して
この関数を適用した新しいリストを返す関数を定義しています。
この操作はリストの各要素に対して関数を**マッピング**すると表現されます。
F#ライブラリにある`List.map`がまさにこの処理に該当します：

```fsharp
let squares = List.map square numbers
```

別の例を見てみましょう：

```text
> List.map (fun x -> x % 2 = 0) [1 .. 5];;

val it : bool list
= [false; true; false; true; false]
```

このコードでは`(fun x -> x % 2 = 0)`という、**関数式**と呼ばれる
匿名関数を定義しています。
この関数式は引数`x`をとり、`x % 2 = 0`つまり`x`が偶数かどうかを
ブール値として返します。
`->`というシンボルは引数リスト(`x`)と関数の本体(`x % 2 = 0`)を区切るものです。

いずれの例においても、関数を別の関数の引数として渡しています。
つまり`List.map`の1番目の引数はそれ自体が別の関数だということです。
関数を**関数値**として使用できるということが
関数型プログラミングの特徴です。

データを変換したり分析したりする場合には、
**パターンマッチ**という方法もあります。
この強力な切り替え機能を使用すると、制御フローを切り替えたり
新しい値をバインドしたりすることができます。
たとえばF#のリストの場合、リスト内の一連の要素に対する
マッチングを行う事ができます。

```fsharp
let checkList alist =
    match alist with
    | [] -> 0
    | [a] -> 1
    | [a; b] -> 2
    | [a; b; c] -> 3
    | _ -> failwith "リストが長すぎます！"
```

この例では要素として取り得る各パターンに対して
`alist`がマッチするかどうかを比較しています。
`alist`がパターンに一致すると結果の式が評価されて、
マッチ式の値が返されます。
ここでの`->`はパターンと、マッチの結果返される値とを区切っています。

パターンマッチは制御フローとしても機能します。
たとえばパターンを使用して型が動的にキャスト可能かどうかを
チェックすることができます：

```fsharp
let getType (x : obj) =
    match x with
    | :? string           -> "xはstringです"
    | :? int              -> "xはintです"
    | :? System.Exception -> "xはexceptionです"
```

`:?`は値が特定の型に一致する場合にtrueを返します。
したがって`x`が文字列の場合、`getType`は「`xはstringです`」という値を返します。

関数値は**パイプライン演算子** `|>` を使用して連結することもできます。
たとえば以下の3つの関数があるとします：

```fsharp
let square x        = x * x
let toStr (x : int) = x.ToString()
let reverse (x : string) = new System.String(Array.rev (x.ToCharArray()))
```

これらの関数をパイプライン内の値として使用できます：

```text
> let result = 32 |> square |> toStr |> reverse;;
val it : string = "4201"
```

パイプラインのデモはF#が関数型プログラミングにおける重要なコンセプトである
**連結性(compositionality)**をサポートすることの一例になっています。
パイプライン演算子を使用すると、
1つの関数の結果が別の関数へと渡されるような、
組み合わされたコードを簡単に記述できるようになります。

#### 1.1.5 宣言型プログラミング

サンプルプログラムの次の行ではコンソールウィンドウに文字列を表示しています。

```fsharp
printfn "N^2 = %A" squares
```

F#ライブラリの関数`printfn`は非常に単純かつ型安全な方法で
文字列をコンソールウィンドウに表示するものです。
次の例を参照してください。
ここでは整数と浮動小数点数、文字列を表示しています：

```text
> printf "%d * %f = %s" 5 0.75 ((5.0 * 0.75).ToString());;
5 * 0.750000 = 3.75
val it : unit = ()
```

書式指定子 `%d` `%f` `%s` はそれぞれ
整数、浮動小数点数、文字列用のプレースホルダーです。
`%A` は任意のデータ型(たとえばリストなど)を表示します。

`printfn` 関数は*宣言型プログラミング*の一例です。
つまり関数呼び出しが副作用を起こします。
宣言型プログラミングを活用する場面としては、
配列やディクショナリ(またはハッシュテーブル)を使用する場合などが一般的です。
F#プログラムでは通常、関数型のテクニックと
宣言型のテクニックを組み合わせて使用します。

#### 1.1.6 .NET相互運用性とCLI準拠

先のプログラムでは、
共通言語基盤(CLI: Common Language Infrastructure)の
`System.Console.ReadKey`メソッドを使用して、コンソールウィンドウが閉じる前に
プログラムを一時停止させています。

```fsharp
System.Console.ReadKey(true)
```

F#はCLI実装上で動作するため、F#から任意のCLIライブラリを呼び出す事ができます。
さらに、その他のCLI言語がF#コンポーネントを使用することも簡単にできます。

#### 1.1.7 並列および非同期プログラミング

F#は*並列*かつ*リアクティブ*な言語です。
F#プログラムの実行時には複数のアクティブな評価が並列して行われ、
イベントやメッセージへの応答を待機しているようなコールバックやエージェントのように、
応答が保留中になっているものも複数存在します。

並列かつリアクティブなF#プログラムを作成するには、
たとえば*非同期*式を使用します。
具体的に言うと、以下のコードはセクション1.1にあるコードと似たところがありますが、
ここでは(いくらか時間のかかるテクニックを使用して)フィボナッチ数を
計算しています。
ただし数値の計算処理は並列にスケジュールされています：

```fsharp
let rec fib x = if x < 2 then 1 else fib(x-1) + fib(x-2)

let fibs =
    Async.Parallel [ for i in 0..40 -> async { return fib(i) } ]
    |> Async.RunSynchronously

printfn "フィボナッチ数は%A" fibs

System.Console.ReadKey(true)
```

このコードでは複数、並列、CPU依存の計算処理を行う例を示しています。

F#はリアクティブな言語でもあります。
以下の例では複数のWebページに対して並列にリクエストを出し、
各リクエストに対するレスポンスを処理し、
最終結果を集計しています。

```fsharp
open System
open System.IO
open System.Net

let http url =
    async { let req = WebRequest.Create(Uri url)
            use! resp = req.AsyncGetResponse()
            use stream = resp.GetResponseStream()
            use reader = new StreamReader(stream)
            let contents = reader.ReadToEnd()
            return contents }

let sites = [ "http://www.bing.com"; "http://www.google.com";
              "http://www.yahoo.com"; "http://www.search.com" ]

let htmlOfSites =
    Async.Parallel [ for site in sites -> http site ]
    |> Async.RunSynchronously
```

非同期ワークフローとその他のCLIライブラリとの機能を組み合わせる事によって、
並列タスクや並列I/O処理、メッセージ受信エージェントといったF#プログラムを
作成することができます。

#### 1.1.8 厳密に型指定された浮動小数点数コード

F#では*測定単位の推論およびチェック機能*によって、浮動小数点を扱う分野において
型のチェックや推論を行う事ができるようになっています。
この機能を使用する事により、
物理的あるいは抽象的な量を表す浮動小数点数を
他の言語には無いような厳密な方法でチェックできるようなプログラムを作成できます。
コンパイル後のコードではパフォーマンスの劣化もありません。
この機能は浮動小数点数を扱うコードに対する型システムととらえることができます。

以下のような例を見てみましょう：

```fsharp
[<Measure>] type kg
[<Measure>] type m
[<Measure>] type s

let gravityOnEarth = 9.81<m/s^2>
let heightOfTowerOfPisa = 55.86<m>
let speedOfImpact = sqrt(2.0 * gravityOnEarth * heightOfTowerOfPisa)
```

`Measure`属性を指定すると、F#では`kg` `s` `m`が通常の型ではなく、
測定単位を定義するものだと認識されるようになります。
今回の場合、`speedOfImpact`の型は`float<m/s>`だと推論されます。

#### 1.1.9 オブジェクト指向プログラミングとコードの構成

この章の最初で出てきたサンプルプログラムは*スクリプト*です。
スクリプトはプロトタイプを迅速に作成する用途にはうってつけですが、
大きなソフトウェアコンポーネントを開発することにはあまり向いていません。
F#ではいくつかのテクニックを駆使する事により、
スクリプトを構造的なコードへと変換することができるようになっています。

最も重要なテクニックとしては、クラス型の定義やインターフェイス型の定義、
オブジェクト式といった機能を使用する*オブジェクト指向プログラミング*があります。
オブジェクト指向プログラミングとは
アプリケーションプログラミングインターフェイス(API)を設計する
重要なテクニックであり、これによって巨大なソフトウェアプロジェクトを
制御します。
たとえば以下ではオブジェクトをエンコード/デコードするクラスを定義しています。

```fsharp
open System

/// 文字と、エンコーディング後の文字との間をマッピングするような
/// エンコーダー/デコーダーオブジェクトを定義します。
/// エンコーディングは [('a','z'); ('Z','a')] というような
/// 文字のペアのシーケンスとして指定します。

type CharMapEncoder(symbols: seq<char*char>) =
    let swpa (x, y) = (y, x)

    /// エンコーディング用の不変なツリーマップ
    let fwd = symbols |> Map.ofSeq

    /// デコーディング用の不変なツリーマップ
    let bwd = symbols |> Seq.map swap |> Map.ofSeq

    let encode (s:string) =
        String [| for c in s -> if fwd.ContainsKey(c) then fwd.[c] else c |]
    let decode (s:string) =
        String [| for c in s -> if bwd.ContainsKey(c) then bwd.[c] else c |]

    /// 入力文字列をエンコード
    member x.Encode(s) = encode s

    /// 入力文字列をデコード
    member x.Decode(s) = decode s
```

この型のオブジェクトのインスタンスは以下のようにして作成できます：

```fsharp
let rot13 (c:char) =
    char(int 'a' + ((int c - int 'a' + 13) % 26))
let encoder =
    CharMapEncoder( [for c in 'a'..'z' -> (c, rot13 c)] )
```

そしてこのオブジェクトは以下のように使用できます：

```text
> "F# is fun!" |> encoder.Encode ;;
val it : string = "F# vs sha!"

> "F# is fun!" |> encoder.Encode |> encoder.Decode ;;
val it : string = "F# is fun!"
```

インターフェイス型は一連のオブジェクトの型をカプセル化できます：

```fsharp
open System

type IEncoding =
    abstract Encode : string -> string
    abstract Decode : string -> string
```

この例にある`IEncoding`は`Encode`と`Decode`というオブジェクトの型を
両方含むようなインターフェイス型です。

インターフェイスはオブジェクト式と型定義のいずれの方法でも実装できます。
たとえば以下のコードではオブジェクト式を使用して
`IEncoding`インターフェイス型を実装しています：

```fsharp
let nullEncoder =
    { new IEncoding with
        member x.Encode(s) = s
        member x.Decode(s) = s }
```

*モジュール*はコードを簡単にカプセル化する方法の1つで、
ラピッドプロトタイピングのように厳密なオブジェクト指向の型階層を設計することに
あまり時間をかけたくないような場合に役立ちます。
以下の例ではこの章で作成したサンプルコードをモジュール内に配置しています。

```fsharp
module ApplicationLogic =
    let numbers n = [1 .. n]
    let square x = x * x
    let squares n = numbers n |> List.map square

printfn "5までの平方数 = %A" (ApplicationLogic.squares 5)
printfn "10までの平方数 = %A" (ApplicationLogic.squares 10)
System.Console.ReadKey(true)
```

モジュールは型に特別な機能を持たせることができるよう、
F#ライブラリ内で使用されることもあります。
たとえば`List.map`はモジュール内の関数です。

ソフトウェアエンジニアリングをサポートするメカニズムとして、
コンポーネントに対して明示的に型を指定する*シグネチャ*や、
膨大大なAPI群を特定の名前で階層毎にまとめることができる
*名前空間*という機能もあります。

#### 1.1.10 インフォメーションリッチプログラミング

F#のインフォメーションリッチプログラミング(情報豊富なプログラミング)とは、
データやサービス、情報などを最大限活用してプログラミングすることを指します。
インフォメーションリッチプログラミングの重要なポイントは、
インターネットや現代的なエンタープライズ環境において参照可能な情報ソースを
活用する際に、障壁となるものを除去することにあります。
F#におけるインフォメーションリッチプログラミングをサポートする重要な機能としては、
型プロバイダーやクエリ式といったものがあります。

F#の型プロバイダーメカニズムを使用する事によって、
外部ソースとして存在するデータやサービスを厳密に型指定された方法で
シームレスに活用できるようになります。
*型プロバイダー*を使用すると、
基本的には外部の情報ソースによるスキーマを元に定義された
新しい型やメソッドがプログラム中で使用できるようになります。
たとえば構造化問い合わせ言語(SQL: Structured Query Language)用の
F#型プロバイダーでは、任意のSQLデータベースのテーブルを直接操作できるような
型あるいはメソッドを利用できるようになります。

```fsharp
// FSharp.Data.TypeProviders, System.Data, System.Data.Linqの参照を追加しておく
type schema = SqlDataConnection<"Data Source=localhost;Integrated Security=SSPI;">

let db = schema.GetDataContext()
```

型プロバイダーはデータベースへ自動的に接続し、
そこからIntelliSenseや型情報を取得します。

(F# 3.0から追加された)クエリ式を使用すると、
SQLやオープンデータプロトコル(OData: Open Data Protocol)、あるいはその他の
構造的またはリレーショナルなデータソースに対してクエリベースのプログラミングを
行う事ができるようになります。
クエリ式ではF#における統合言語クエリ(LINQ: Language-Integrated Query)を
サポートする他、追加されたクエリ演算子を使用する事でさらに複雑なクエリを
作成することもできるようになっています。
たとえばデータソース内の顧客情報をフィルタするクエリは以下のように作成できます：

```fsharp
let countOfCustomers =
    query { for customer in db.Customers do
            where (customer.LastName.StartsWith("N"))
            select (customer.FirstName, customer.LastName) }
```

SQLデータベースやWeb上のデータプロトコル向けの型プロバイダが
組み込みで用意されているため、エンタープライズやWeb、クラウド環境において
これまでよりも手軽に重要なデータソースへとアクセスできるようになっています。
必要であれば独自の型プロバイダーを作成したり、他者が作成した型プロバイダーを
利用したりすることもできます。
たとえば所属する組織において、特定のデータスキーマによって名前づけられた
膨大なデータセットを抱えたデータサービスが公開されているとします。
そうした場合、このスキーマを読み込むような型プロバイダを作成することにより、
外部のプログラマが厳密に型指定された方法で最新のデータセットを
利用できるようになります。

### 1.2 本仕様書における表記法について

本仕様書では、非公式あるいは準公式のテクニックを使用して
F#言語を解説しています。
本仕様書にあるすべての例は特に言及がない限り、いずれも軽量構文で記述されています。

以下の表にあるような、一般的な正規表現もあります：

|表記              |意味                              |
|==================|==================================|
|regexp+           |1回以上現れる                     |
|regexp*           |0回以上現れる                     |
|regexp?           |0または1回現れる                  |
|[ char - char ]   |ASCII文字の区間                   |
|[ ^ char - char ] |これらの区間に含まれない任意の文字|

Unicode文字クラスについては、正規表現用のCLIライブラリで使用される略記と
同じ記法が採用されます。
たとえば`\Lu`は任意の大文字に該当します。
以下の文字はそれぞれ該当する記号を表します：

|文字|名前          |記号                               |
|====|==============|===================================|
|`\b`|バックスペース|ASCII/UTF-8/UTF-16/UTF-32のコード08|
|`\n`|改行          |ASCII/UTF-8/UTF-16/UTF-32のコード10|
|`\r`|復帰          |ASCII/UTF-8/UTF-16/UTF-32のコード13|
|`\t`|タブ          |ASCII/UTF-8/UTF-16/UTF-32のコード09|

## 4. 基本的な文法要素

このセクションでは、後のセクションで繰り返し使われる文法要素を定義します。

### 4.1 演算子名

文法のところどころでは、 `ident` ではなく `ident-or-op` を参照します:

```
ident-or-op :=
    | ident
    | ( op-name )
    | (*)

op-name :=
    | symbolic-op
    | range-op-name
    | active-pattern-op-name

renge-op-name :=
    | ..
    | .. ..

active-pattern-op-name :=
    | | ident | ... | ident |
    | | ident | ... | ident | _ |
```

演算子定義では、演算子名は括弧内に置かれます。
例えば:

```fsharp
let (+++) x y = (x, y)
```

この例ではバイナリ演算子 `+++` を定義します。
テキスト `(+++)` は、関連するテキスト `+++` で識別子としてふるまう `ident-or-op` です。
同様に、アクティブパターン定義 ([セクション7](#section7)) の場合には、アクティブパターンケース名は次の例のように括弧内に置かれます:

```fsharp
let (|A|B|C|) x = if x < 0 then A elif x = 0 then B else C
```

`ident-or-op` は識別子として振る舞うため、そのような名前は式で利用できます。
例えば:

```fsharp
List.map ((+) 1) [ 1; 2; 3 ]
```

3文字トークン `(+)` は `*` 演算子を定義します:

```fsharp
let (*) x y = (x + y)
```

`*` で始まる他の演算子を定義するためには、空白は左括弧の次にこなければなりません。
そうでなければ `(*` はコメントの開始と解釈されます:

```fsharp
let ( *+* ) x y  (x + y)
```

記号演算子といくつかの記号的なキーワードは、 F# プログラムのコンパイルされた形式で見えるコンパイルされた名前を持っています。
コンパイルされた名前を以下に示します。

```
[] 	op_Nil
::	op_ColonColon
+	op_Addition
-	op_Subtraction
*	op_Multiply
/	op_Division
**	op_Exponentiation
@	op_Append
^	op_Concatenate
%	op_Modulus
&&&	op_BitwiseAnd
|||	op_BitwiseOr
^^^	op_ExclusiveOr
<<<	op_LeftShift
~~~	op_LogicalNot
>>>	op_RightShift
~+	op_UnaryPlus
~-	op_UnaryNegation
=	op_Equality
<>	op_Inequality
<=	op_LessThanOrEqual
>=	op_GreaterThanOrEqual
<	op_LessThan
>	op_GreaterThan
?	op_Dynamic
?<-	op_DynamicAssignment
|>	op_PipeRight
||>	op_PipeRight2
|||>	op_PipeRight3
<|	op_PipeLeft
<||	op_PipeLeft2
<|||	op_PipeLeft3
!	op_Dereference
>>	op_ComposeRight
<<	op_ComposeLeft
<@ @>	op_Quotation
<@@ @@> op_QuotationUntyped
~%	op_Splice
~%%	op_SpliceUntyped
~&	op_AddressOf
~&&	op_IntegerAddressOf
||	op_BooleanOr
&&	op_BooleanAnd
+=	op_AdditionAssignment
-=	op_SubtractionAssignment
*=	op_MultiplyAssignment
/=	op_DivisionAssignment
..	op_Range
.. ..	op_RangeStep
```

他の記号演算子のためのコンパイルされた名前は、N1 から Nn が下表のような記号のための名前からなる `op_N1...Nn` です。
例えば、記号演算子 `<*` はコンパイルされた名前 `op_LessMultiply` を持ちます:

```
>	Greater
<	Less
+	Plus
-	Minus
*	Multiply
=	Equals
~	Twiddle
%	Percent
.	Dot
&	Amp
|	Bar
@	At
#	Hash
^	Hat
!	Bang
?	Qmark
/	Divide
.	Dot
:	Colon
(	LParen
,	Comma
)	RParen
[	LBrack
]	RBrack
```

### 4.2 修飾された識別子

修飾された識別子 `Long-ident` は `'.'` と任意の空白で区切られた識別子のシーケンスです。
修飾された識別子 `Long-ident-or-op` は、演算子名で終了する修飾された識別子です。

```
long-ident :=  ident '.' ... '.' ident

long-ident-or-op :=
  | long-ident '.' ident-or-op
  | ident-or-op
```

# 1. Java プログラミングの基礎的事項

オブジェクト指向プログラミングは、プログラムをオブジェクト (データ構造とそれに対する操作) の集合体として捉え、オブジェクト間の操作の呼び出しによりプログラムを実行しようとする考え方です。今日では構造化プログラミング、関数型プログラミングと並ぶ重要なプログラミングの考え方で、Java は主にオブジェクト指向プログラミングを想定して設計されたプログラミング言語です。

ここでは、Java で記述した様々なオブジェクトを例にとり、Java の言語仕様がサポートする様々なオブジェクト指向プログラミングのからくりを見てゆきます。

## 1.1. クラスとインスタンス

Java のオブジェクトにはクラスとインスタンスという考え方があります。

クラスは、オブジェクトのデータ構造とそれに対する操作がどのように表現されるかを表した設計のようなものです。Java の「ソースコード」と言ったときは、クラスの記述 (または記述したファイル) のことを指します。Java においては、データ構造の表現を「フィールド」、操作の表現を「メソッド」と呼んでいます。

インスタンスは、あるクラスによって表され Java VM (Java の実行環境) 上に確保されたオブジェクトの実体です。Java VM にはヒープ・メモリと呼ばれるメモリ領域が用意されており、インスタンスはヒープ・メモリ上に確保された、実際に実行可能なオブジェクト (= プログラムの部品) です。同じクラスであってもインスタンスが異なる場合には、データ構造すなわちフィールドは異なる状態を持ちます。

インスタンスは常時ヒープ・メモリ上に存在するわけではなく、プログラムで必要になった時に都度ヒープ・メモリを割り当てて生成します。使用しなくなったインスタンスは Java VM の判断により破棄され、割り当てられていたヒープ・メモリは自動的に解放されます。ヒープ・メモリを自動的に解放する仕組みは「ガベージ・コレクション (GC)」と呼ばれ、Java VM の重要な機能の一つとなっています。

なお、インスタンスではなくクラスに属する特殊なフィールドとメソッドも存在し、それぞれ「`static` フィールド」「`static` メソッド」と呼ばれています。これらはインスタンスを生成することなく利用できるという特徴を持ちますが、それが逆に用途を限定することにもつながっています。

- `static` フィールドは主に名前付き定数を宣言する場合に用いられます。一般的なデータ保持には通常のフィールドを使用します。
- `static` メソッドは一部のユーティリティ・メソッドや特別な役割を持つメソッドにのみ用いられます。Java アプリケーションのエントリ・ポイントは「`main`」という名前の `static` メソッドになります。

Java のプログラミングを習得するにあたって、クラスとインスタンスの違いは明確に意識するようにしてください。

## 1.2. クラスの継承とポリモーフィズム

クラスは、あるクラスのフィールドやメソッドを維持したまま、フィールドやメソッドを追加して定義することもできます。その場合の元のクラスをスーパークラス、新たに定義するクラスをサブクラスと呼び、サブクラスを定義することを「継承」と言います。継承時には、メソッドの処理を上書きして再定義することも可能であり、それをメソッドの「オーバーライド」と呼びます。

他のプログラミング言語、例えば C++ では複数のスーパークラスから継承できる「多重継承」をサポートする場合もありますが、Java は常に単一のスーパークラスからのみ継承する「単一継承」のアプローチを採っています。

Java のすべてのクラスは、そのスーパークラスを辿っていくと必ず `java.lang.Object` というクラスに行きつきます。言い換えると、特にスーパークラスを指定しなかった場合は `java.lang.Object` クラスを継承します。

>Java が `java.lang.Object` ようなクラスを用意しているのは、初期のオブジェクト指向言語である Smalltalk の影響です。Java が最初に参考とした C++ では、すべてのクラスが暗黙的に継承するようなクラスを持たない仕様となっています。

クラス A と、クラス A を継承したクラス B、クラス C が存在する場合、クラス A で宣言されたメソッドを用いてクラス B、クラス C のインスタンスを操作することができます。これを「サブタイピングによるポリモーフィズム」と言います。

>「ポリモーフィズム」の実現方法はいくつか存在しており、Java 自身も他のポリモーフィズム (型パラメータを使用する方法) をサポートします。ここではクラス間の継承、すなわち「サブタイピング」によるポリモーフィズムを取り扱います。

サブタイピングによるポリモーフィズムの具体例を挙げると、Java の標準 API にはバイト列の入力を行う `java.io.InputStream` クラスが存在し、そのサブクラスとしてバイト単位でメモリの読み込みを行う `java.io.ByteArrayInputStream` やバイト単位でファイルの読み込みを行う `java.io.FileInputStream` などが用意されています。バイト単位でデータの読み込みを行う点に注目すると、スーパークラスの `InputStream` で宣言されたメソッドを用いて、対象がメモリ (`java.io.ByteArrayInputStream`) であってもファイル (`java.io.FileInputStream`) であっても統一的に扱うことが可能です。従来の Visual Basic などでは入力データがメモリかファイルかによって処理を分けなければなりませんでしたが、Java では入力のデータソースによらず `java.io.InputStream` クラスのメソッドを用いて処理を統一することが可能です。

Java に代表されるポリモーフィズムを持つプログラミング言語は、そうでない言語と比較して、分岐処理 (Visual Basic の If...Then...Else 構文など) を大幅に減らし、処理を共通化できます。当然のことながらバグが入り込む余地も減らすことができます。

ポリモーフィズムはオブジェクト指向言語を学習する上で最初にぶつかる大きな壁ですが、ポリモーフィズムが理解できない＝オブジェクト指向言語が理解できないと断言しても良いくらい重要な概念ですので、必ず自分の言葉で理解するようにしてください。幸い、Java の標準 API にはポリモーフィズムの例が多数含まれていますので、参考にするとよいでしょう。

## 1.3. クラスを補完する仕組み

Java ではクラスを補完するデータ型としてインタフェース、列挙型、アノテーション、プリミティブ型が存在します。いずれもクラスと並んで Java のプログラムを構成する要素で、主にクラスだけでは表現しきれない部分を担います。

### 1.3.1. インタフェース

インタフェースはクラスとよく似ていますが、クラスと異なりデータ構造を持たず、操作のみが存在します。操作も原則としてメソッドの呼び出し書式の宣言に留まり、メソッドの処理内容までは記述しません。

>インタフェースでメソッドの処理を記述することも、条件付きながら可能です。

インタフェースは単体ではインスタンスを生成することはできず、インタフェースの記述内容からクラスを作成する必要があります。インタフェースをもとにクラスを作成することを「実装」と言います。インタフェースの「実装」はクラスの「継承」に似ています (行うべきことも概ね同じです) が、以下のような違いがあります。

- インタフェースは原則としてメソッドの定義を持たないため、すべてクラス側で定義しなければならない。
- インタフェースはクラスではないため、複数のインタフェースを「実装」することは可能である。

スーパークラスと同様に、インタフェースでもポリモーフィズムを実現することができます。むしろインタフェースの方がポリモーフィズムの実現手段としては強力なものとなっています。

インタフェースによるポリモーフィズムの例を挙げると、Java の標準 API にリスト構造を表す `java.util.List` インタフェースと、その実装としてデータ構造に配列を用いた `java.util.ArrayList` クラス、連結リストを用いた `java.util.LinkedList` クラスがあります。C や Visual Basic などではリスト構造を独自に実装しなければならず、アルゴリズムとデータ構造の分離は困難でしたが、Java では `java.util.List` インタフェースで宣言されたメソッドでデータを操作する限り、実際のデータ構造 (`java.util.ArrayList` または `java.util.LinkedList`) を意識しなくてよいため、アルゴリズムとデータ構造の分離を API レベルで実現しています。

>`java.util.LinkedList` は両端キュー構造を表す `java.util.Deque` インタフェースも同時に実装していることから (複数のインタフェースの実装例)、`java.util.Deque` が必要な場面でも `LinkedList` を利用することができます。

### 1.3.2. 列挙型

列挙型は名前付き定数の宣言に特化したクラスです。

列挙型は多くの言語でサポートされている構文です (ただし JavaScript にはありません)。主に特定の整数値に名前を付けたものとして実現され、想定外の値が入力されることを防ぐために用意されています。各言語の仕様にもよりますが、列挙型の値は宣言順の整数になっていることが多いようです (文字列を値として利用できる言語もあります)。Visual Basic のライブラリでは、パラメータや戻り値で列挙型 (`vbOK`、`vbCancel` 等) を使用しているものが散見されます。

Java の列挙型は文法こそ C のそれに似ていますが、言語仕様上は `java.lang.Enum` クラスを継承した特殊なクラスとして扱われます。基本的な使い方は他の言語の列挙型に準じますが、クラスであるため値の表現などにかなりの自由度があります。列挙型をクラスとしてみた場合、さらなる継承が不可能で、かつインスタンスを新たに生成できない (あらかじめ生成されたインスタンスを参照するのみ) 特別な存在となります。

### 1.3.3. アノテーション (注釈)

アノテーション (注釈) は、クラス、インタフェース、メソッド、フィールド、列挙型などに付加して意味を持たせる、目印のような役割を持つものです。

プログラムの構成要素に何らかの意味を持たせるには、古くからネーミング規約を用いて人間が管理する手法が採られていました。例えば、共通処理に何らかのアプリケーション・フレームワークを利用する場合、従来であればネーミング規約を用いて「ロジックを記述するクラス名は `～Bean` で終わる」、「データベースのカラムに対応するフィールドは `～Column` で終わる」 といったルールをアプリケーション・フレームワーク側で定め、各アプリケーションはそれに従う必要がありました。あくまでネーミング規約ですからタイプミス等でルールから外れてしまう場合があり、ソースコードレビュー (つまり人間の目視) で誤りを検出しなければなりませんでした。

アノテーションはこのような作業の一部を機械的に行うために導入された仕組みです。前述の例をアノテーションで置き換えると、「ロジックを記述するクラスの先頭にはアノテーション `@javax.inject.Named` を付加する」、「データベースのカラムに対応するフィールドの先頭にはアノテーション `@javax.persistence.Column` を付加する」というような感じになります。アノテーションは Java コンパイラでチェックされるため、タイプミスがあればコンパイル自体が失敗します。ソースコードレビューに委ねられていた部分の一部を Java コンパイラが肩代わりするため、省力化につながります。

なお、Java の標準 API が用意しているアノテーションのひとつに `@java.lang.Deprecated` があります。これは、標準 API の中でも陳腐化などのため利用が推奨されなくなったクラスやメソッドに付加されるもので、`@java.lang.Deprecated` アノテーションが付加されたクラスやメソッドを使用していると Java コンパイラが警告を出す仕組みになっています。

>Java SE においては、現時点ではアノテーション (注釈) の使用は限定的ですが、Java EE においては XML ファイルによる各種設定記述の代替として積極的に用いられています。Java EE のうち後発の API については「アノテーション (注釈) 地獄」と揶揄されるほど多用されています。

### 1.3.4. プリミティブ型

プリミティブ型は Java にあらかじめ備わっているデータ型です。算術演算や比較結果を示すために用いられます。プリミティブ型は四則演算をはじめとする各種演算子を使用できる一方で、クラスではないため言語仕様上の制約を受ける場合があります。そのため、各プリミティブ型には相互変換が可能な「ラッパークラス」と呼ばれるクラスが用意されています。

プリミティブ型のアイデアは C++ から拝借したものですが、Java の言語仕様に合わせてカスタマイズしているため同一ではありません。

|プリミティブ型の種類|プリミティブ型|対応するラッパークラス|サイズ|値の取りうる範囲|
|--------------|--------|--------------------|------|------------------|
|数値型 (整数型)|`byte`  |`java.lang.Byte`    |8-bit |-128 ～ 127       |
|              |`short` |`java.lang.Short`   |16-bit|-32768 ～ 32767   |
|              |`int`   |`java.lang.Integer` |32-bit|-2147483648 ～ 2147483647|
|              |`long`  |`java.lang.Long`    |64-bit|-9223372036854775808 ～ 9223372036854775807|
|              |`char`  |`java.lang.Character`|16-bit|0 ～ 65535 (UTF-16 '\u0000' ～ '\uffff')|
|数値型 (浮動小数点型)|`float`|`java.lang.Float`|32-bit|32-bit IEEE 754 浮動小数点数|
|              |`double`|`java.lang.Double`  |64-bit|64-bit IEEE 754 浮動小数点数|
|論理型        |`boolean`|`java.lang.Boolean`|N/A   |`true` or `false` |

## 1.4. ソースファイルとクラスファイル

Java のプログラムは以下の手順で作成・実行します:

1. Java のソース・コードを記述したソース・ファイルを作成する (拡張子は `.java`)。
2. ソース・ファイルをコンパイルしてクラス・ファイルを作成する (拡張子は `.class`)。
3. クラス・ファイルを Java VM 上で実行する。

Java のクラス・ファイルはどの OS でコンパイルしても同じものが作成され、実行する OS に依存しません。実際には Java VM はクラス・ファイルを読み込み、それを OS 固有のコードに変換して実行します。これが Java VM (Java Virtual Machine ; Java 仮想マシン) と呼ばれる所以です。

Java VM については、Java のプログラムを動作させるのに必要なもの、という程度の認識で当座は問題ありません。なお、Java 以外の言語も同様に VM 上で動作することを前提としているものは多数ありますが、Java VM の実行効率は群を抜いて優れており、プログラムの書き方次第では C++ で書かれた OS 固有のコードと同等か、それ以上の実行速度が得られるとも言われています。

なお、ソース・ファイル (`.java`) とクラス・ファイル (`.class`) は必ずしも 1 対 1 に対応するとは限りません。1 つのソース・ファイルから 1 つのクラス・ファイルが作成されることもあれば、1 つのソース・ファイルから複数のクラス・ファイルが作成されることもあります (複数のソース・ファイルから 1 つのクラス・ファイルが作成されることはありません)。

## 1.5. クラス名とパッケージ

Java のクラス名 (およびメソッド名、フィールド名) は、Unicode で表現できる名前 (日本語文字を含む) を使用することができ、長さには制限がありません。ただし、言語仕様上以下の規則に従う必要があります。

- 先頭文字を数字にすることはできません。
- 区切り文字 (`(`、`)`、`@` など) や演算子 (`-`、`&`、`=` など) を含めることはできません。
- Java のキーワード (予約語) と一致する名前を使用することはできません。

なお、Java のキーワードは以下の通りです。原則として Java の言語仕様の一部として使用されている単語ですが、一部 `const` や `goto` などキーワードではあっても Java の言語仕様では使用していないものもあります。

```
abstract   continue   for          new         switch
assert     default    if           package     synchronized
boolean    do         goto         private     this
break      double     implements   protected   throw
byte       else       import       public      throws
case       enum       instanceof   return      transient
catch      extends    int          short       try
char       final      interface    static      void
class      finally    long         strictfp    volatile
const      float      native       super       while
```

さらに、慣習として以下のような命名規約に従うことが多いです。

- ASCII のアルファベットで表現可能な範囲で命名します。
- 基本的にキャメル・ケース (例: `CharSequence`) を使用し、スネーク・ケース (例: `char_sequence`) は使用しません。
- クラス名の先頭は大文字 (例: `CharSequence`)、メソッドおよびフィールド名の先頭は小文字 (例: `subSequence`) とします。
- ドル記号 `$` は Java 内部で使用されるため、名前に含めることは推奨されません (エラーにはなりませんが混乱の原因となります)。

クラスのソース・ファイル名は、クラス名に拡張子 `.java` を付加したもの (例: `CharSequence.java`) となります。

クラス名には、上記に示した基本クラス名と、所属するパッケージ名を付加した完全クラス名があります。パッケージ名とは、クラス名の重複によるトラブルを回避するために付加する補助的な名前です。C++ にはほぼ同じ概念として「名前空間 (namespace)」があります。クラスを識別するときには、原則としてパッケージ名を含む完全クラス名を使用します (※例外あり)。

パッケージ名は `.` (ピリオド) でつないで階層構造**のように**表現することができます。ソース・ファイルに対応するパッケージはディレクトリ (フォルダ) の階層によって表現します。例えば、`java.math.BigDecimal` クラス (基本クラス名: `BigDecimal`、パッケージ名: `java.math`) の場合、ソースファイルは `java/math/BigDecimal.java` となります。

プログラム中でクラスを参照する場合には、原則として完全クラス名で記述しなければなりませんが、以下の場合はパッケージ名を省略することができます。

- 同一パッケージ内のクラスを参照する場合。
- ソース・コードで `import` 文を宣言した場合。`import package_name . ClassName ;` (`ClassName`: 適用対象のクラス名) または `import package_name . * ;` (パッケージ内の全クラスを適用対象) とします。
- `java.lang` パッケージのクラスを参照する場合。`java.lang` パッケージは Java の言語仕様と密接に関連するクラスが集まっているため、常に `java.lang.*;` が宣言されているものとみなします。 
- デフォルト・パッケージ (無名パッケージ) を使用する場合。ただしデフォルト・パッケージの使用は推奨されていません。

実際のソースファイルでは、ほとんどの場合で `import` 文を使用して基本クラス名でクラスを参照します。次章以降の解説では原則として基本クラス名を用います。

## 1.6. Java API

Java には標準・サードパーティを含め無数の API が存在していますが、基本となるのは標準である Java SE と、サーバーサイド向け拡張版である Java EE の 2 種類です。通常は、これら標準 API の範囲だけで Java アプリケーションは開発できるはずです。

Java API には以下の ２ 通りのタイプがあります。

- クラス・ライブラリ : アプリケーションから呼び出す形式の共通部品的な位置づけの機能
- フレームワーク : 半完成品のアプリケーションで、アプリケーション固有部分を追加実装することで完成品となる

Java SE 標準 API は、ほぼすべてが上記のクラス・ライブラリに該当します。目的は「車輪の再発明を防ぐ」ことで、現在 (Java SE 8) は数千個 (クラス) の API が用意されています。もっとも、よく利用されるのは全体のごく一部で、既にかなりの API が陳腐化して非推奨となっています。

Java EE 標準 API は、主にフレームワークとなります。目的は「開発生産性の向上」であり、アプリケーションに必要なコードのみ記述すれば良いように設計されています。ただし JavaMail などのクラスライブラリも残存しており、またクラス・ライブラリとフレームワークのハイブリッド型 API も存在します (JAX-WS、JAX-RS 等)。

>XML パーサーや JDBC などのクラスライブラリは、当初 Java EE 標準 API でしたが、現在では Java SE に移管されています。

Java EE のアプリケーションは「コンテナ」と呼ばれる、Java VM 上で動作する専用の実行環境上で利用しますが、最近ではコンテナを用意しなくても Java EE アプリケーションを実行可能な技術が登場しています。

Java は API の膨大さに圧倒されてしまうことが多いですが、言語仕様自体は比較的コンパクトであるため、使用頻度の高い API から慣れていくようにすると良いでしょう。また、Java API を暗記することは避け、いつでも Java API のリファレンス (Javadoc) を参照できるようにしておくのが望ましい方法です。

Java SE 8 API リファレンス 
http://docs.oracle.com/javase/jp/8/docs/api/

Java SE については API のソースコードも付属しています。Java の文法に習熟した後、API の挙動に不可解な点が見られたなら、ソースコードを参照するのも一案です。

## 1.7. JAR ファイルとクラス・パス

実際に Java のプログラムを記述すると大量のクラス・ファイルが作成されることになります。そのままでは扱いづらいため、クラス・ファイルを適切なまとまりで ZIP アーカイブして、ZIP アーカイブ内のクラス・ファイルを Java VM に読み込んでプログラムを実行する仕掛けが Java に備わっています。

この ZIP アーカイブにはクラスファイルの他に追加情報が含まれるため、拡張子を .jar として、JAR (Java ARchive) ファイルと呼びます。JAR ファイルが存在する関係で、Java の API には ZIP アーカイブを操作するものが豊富に用意されています。

Java VM が読み込むクラス・ファイルの場所は、既定ではカレント・ディレクトリになっています。しかし、JAR ファイルはその原則に従わないため、別途 Java VM に JAR ファイルの場所を指定する必要があります。Java ではその場所のことをクラス・パスと呼んでいます。クラス・パスにはクラス・ファイルのディレクトリまたは JAR ファイルを必要な数だけ指定することができます。

Java プログラムの実行時に発生する問題の 1 つとして、このクラス・パスが適切に設定されていないため、必要な JAR ファイルを読み込むことができない事象が挙げられます。エラー出力に `ClassNotFoundException` や `NoClassDefFoundError` といった文字列が現れたら、まずはクラス・パスが適切に設定されているか確認してください。

>エラー出力に `NoClassDefFoundError` が現れた場合は悪性度が高く、クラス・パスの見直しだけでは解決しない場合もあります。

## 1.8. Java プログラムのビルド

Java のプログラムをすべてコンパイル、テスト、パッケージング (JAR ファイル化) する作業は、プログラムの規模が大きくなるほど確実に行うことが難しくなります。これら一連の作業は定型化されているため、ツールを用いて自動化した方が良いでしょう。Java には、Ant、Maven、Gradle 等といったビルド作業を自動化するツールが揃っているため、これらを用いるのが便利です。現在の主流は Maven ですが、最近では Gradle が利用されるケースも増えています。Java プログラムのビルドについては具体例とともに次章で取り上げます。

# 6. 例外処理

## 6.1. 例外処理とは

Java プログラムの処理実行中に何らかの異常が発生した場合に、プログラムまたは Java VM が処理を中断して対策を講じるための仕組みを「例外処理」と呼びます。例外処理では、異常が発生すると「例外のスロー」という形でプログラムを一時中断します。中断したプログラムは「例外のキャッチ」によってリカバリ処理を行います。

例外処理は Java をはじめとするオブジェクト指向言語で多く採用されている仕組みで、プログラムの正常系処理と異常系処理を明確に分離するのに役立ちます。例外処理をサポートしていないプログラミング言語では戻り値に特別な値を設定することで正常系処理と異常系処理を区別しています。

>例外処理は C には存在しません。Visual Basic には On Error 構文によるエラーハンドリングのみ用意されています。C++、JavaScript および Python は例外処理を持ちますが、いずれも Java とは異なる構文となっています。C++ の例外処理は Java のそれと類似していますが、細部に大きな違いがあるため注意が必要です。

## 6.2. 例外オブジェクト

例外のスローにおいては、異常の原因を表すための情報を例外オブジェクトという形で付加します。例外をキャッチしてリカバリ処理を行う際には、例外オブジェクトを参照して必要な処理を行うことになります。例外オブジェクトには専用のクラスが用意されており、それらを使用しなければなりません。

Java の例外オブジェクトは `Throwable` クラスとそのサブクラスのインスタンスである必要があり、以下のように細分化されています。

- `Throwable` : すべての例外オブジェクトのスーパークラス。サブクラスは `Error` および `Exception` だけであり、キャッチする意味を持たない。
  - `Error` : サブクラスを含め主に Java VM のエラーを表す。キャッチすべきではない (非チェック例外)。
  - `Exception` : サブクラスを含めプログラム内で発生したエラー一般を表す。キャッチしなければならない (チェック例外)。
    - `RuntimeException` : サブクラスを含めプログラム内で発生したエラーのうちリカバリ不能なものを表す。Java SE においては基本的にバグが起因であり、キャッチする必要はない (非チェック例外)。Java EE においてはキャッチ任意の一般例外を意味する場合もある。

## 6.3. 例外のスロー

処理中にエラーその他が発生する場合、throw 文を用いて例外オブジェクトをスローすることができます。

- 書式: `throw expr ;` (`expr`: 例外オブジェクトの生成式)

```java
throw new IllegalArgumentException();  // 引数に不正な値が渡された
```

アプリケーションで throw 文を記述する場合、その多くは引数の妥当性チェック (バリデーション) でのエラーであり、必然的に非チェック例外 (具体的には `RuntimeException` のサブクラス) が多くなりますが、ファイル操作時などでチェック例外 (`IOException`) をスローする場合もあります (`IOException` のスローについては、アプリケーションから呼び出す Java 標準 API がスローする場合が大半を占めます)。

以下にアプリケーションでよくスローされる例外のクラスを示します。これらはプログラムがスローする場合もあれば、Java VM が異常を検出してスローする場合もあります。

|例外クラス                 |チェック|意味|
|--------------------------|:------:|------------------------------------------------------------|
|`Exception`               |○       |すべての例外のスーパークラス|
|`IOException`             |○       |I/O 処理でエラーが発生した|
|`SQLException`            |○       |JDBC での SQL 実行時にエラーが発生した|
|`RuntimeException`        |×       |被チェック例外 (ランタイム例外) のスーパークラス|
|`NullPointerException`    |×       |引数が null である|
|`IllegalArgumentException`|×       |引数に不正な値が渡された (引数が null の場合にも用いる場合がある)|
|`NumberFormatException`   |×       |文字列が数値に変換できない書式になっている|
|`ArithmeticException`     |×       |異常な算術演算が行われた、例えば数値を 0 で割ったなど|
|`UnsupportedOperationException`|×  |メソッドの実行はサポートされていない|
|`UncatchedIOException`    |×       |(後述の catch 節にて) IOException を非チェック例外に変換する|

上記のうち特に `NullPointerException` は、`null` のインスタンスのメソッドを呼び出そうとした場合に Java VM がスローする例外でもあります。特にメソッドの戻り値が `null` になりうる場合において、`null` に対する処理 (`null` の場合はメソッドを呼び出さない、など) が漏れているときに発生しやすいバグです。

チェック例外はアプリケーションのどこかでキャッチしてリカバリ処理を行う必要があります。非チェック例外の場合はキャッチの対象外ですが (Java VM が処理します)、`RuntimeException` とそのサブクラスについては必要であればキャッチしても構いません。

例外の処理方法については、メソッド内で例外を処理する try-catch 文と、呼び出し元に例外処理をゆだねる throws 句の 2 通りがあります。

## 6.4. try-catch 文

スローされた例外を自メソッドでキャッチして例外処理を行うための構文として try-catch 文が用意されています。

- 書式 (1): `try try_block catch ( CatchType e ) catch_block`
- 書式 (2): `try try_block [catch ( CatchType e ) catch_block] finally finally_block`
- `try try_block` を try 節、`catch ( CatchType e ) catch_block` を catch 節、`finally finally_block` を finally 節と呼びます。
- try 節はキーワード `try` とそれに続くブロック `try_block` からなり、ブロックに囲まれた範囲でスローされる例外が処理の対象であることを示します。書式 (1) (2) とも、try 節は必ず必要です。
- catch 節は書式 `catch (CatchType e) catch_block` からなり、キーワード `catch`、キャッチする例外 `CatchType e`、例外処理を記述するブロック `block` からなります。
  - `CatchType` はキャッチする例外のクラスを表し、複数指定することが可能です (マルチキャッチ)。`CatchType` を複数指定する場合は `|` で繋ぎます。
  - `e` は各 catch 節でのみ有効なローカル変数として扱われます。
- catch 節はキャッチする対象の例外クラスの分だけ繰り返し記述することができます。catch 節は書式 (2) に示すように、場合によっては完全に省略することもできます。
- finally 節はキーワード `finally` とそれに続くブロック `finally_block` からなり、try-catch 文を終了する直前に実行する処理を記述します。catch 節の処理は対応する例外がスローされないと実行されませんが、finally 節の処理は catch 節の実行有無にかかわらず必ず実行されます。finally 節はファイルやネットワークのクローズなど後始末の処理に用いられます。finally 節は書式 (1) に示すように、場合によっては省略することもできます。
- catch 節と finally 節はともに省略可能ですが、両方とも省略することはできません。仮に両方とも省略できた場合、その try-catch 文は意味をなさないからです。

### 6.4.1. catch 節の照合順序

catch 節は上から順に例外オブジェクトと照合され、処理されるため、サブクラスの方から順に記述する必要があります。例えば、`FileNotFoundException`、`IOException`、`Exception` を処理する場合、`IOException` は `Exception` のサブクラス、`FileNotFoundException` は `IOException` のサブクラスであるため、`FileNotFoundException`、`IOException`、`Exception` の順に catch 節を記述します。

```java
try {
    // この範囲の処理で throw 文が使用されるか、または呼び出し先から例外がスローされる場合
    ...
} catch (FileNotFoundException e) {
    // FileNotFoundException の例外処理
    ...
} catch (IOException e) {
    // FileNotFoundException を除く IOException の例外処理
    ...
} catch (Exception e) {
    // IOException を除く Exception の例外処理
    ...
}
```

誤って次のように記述すると、`FileNotFoundException` の例外処理は実行されません。

```java
try {
    // この範囲の処理で throw 文が使用されるか、または呼び出し先から例外がスローされる場合
    ...
} catch (IOException e) {
    // FileNotFoundException を除く IOException の例外処理
    ...
} catch (FileNotFoundException e) {
    // FileNotFoundException の例外処理、これは実行されない
    ...
} catch (Exception e) {
    // IOException を除く Exception の例外処理
    ...
}
```

悪いパターンは、複数の例外処理があるにもかかわらず先頭に `Exception` の例外処理を記述することです。

```java
try {
    // この範囲の処理で throw 文が使用されるか、または呼び出し先から例外がスローされる場合
    ...
} catch (Exception e) {
    // Exception の例外処理、RuntimeException を含めすべてこの例外処理が実行される
    ...
} catch (FileNotFoundException e) {
    // FileNotFoundException の例外処理、これは実行されない
    ...
} catch (IOException e) {
    // FileNotFoundException を除く IOException の例外処理、これも実行されない
    ...
}
```

このようにすると、`RuntimeException` を含むすべての `Exception` とそのサブクラスの例外処理を行うことになり、それ以下の例外処理が実行されないことになってしまいます。同様に先頭で `Throwable` をキャッチすると、本来キャッチすべきでない `Error` (とそのサブクラス) を含むあらゆる例外オブジェクトをそこで処理することになり、例外処理の仕組みを破綻させてしまいます。先に `Throwable` はキャッチしてはいけないと述べたのはこれが理由です。

### 6.4.2. 例外のマルチキャッチ

try-catch 文の catch 節のところでも取り上げましたが、複数の例外処理を 1 つにまとめることができます。これを例外のマルチキャッチといいます。マルチキャッチでは、対象となる例外クラスを `|` でつないで並べます。

```java
// マルチキャッチを使用しない場合
try {
    ...
} catch (IllegalAccessException e) {
    // 例外処理; InstantiationException と同じ処理
    ...
} catch (InstantiationException e) {
    // 例外処理; IllegalAccessException と同じ処理
    ...
}
```

```java
// マルチキャッチを使用した場合
try {
    ...
} catch (IllegalAccessException | InstantiationException e) {
    // 例外処理; IllegalAccessException、InstantiationException で共通の処理
    ...
}
```

実は第 2 の例のようにマルチキャッチを行っても、コンパイル後には第 1 の例のようなマルチキャッチを使わないコードに変換されます。catch 節の宣言順序もマルチキャッチでの宣言順になるようです。

## 6.5. try-with-resources 文

try-catch 文はリソースのオープンとクローズを伴って使用されることが多く、finally 節によるリソースのクローズが必須です。そこで、try-catch 文にリソースのオープンと自動クローズを合わせた簡略記法である try-with-resources 文も用意されています。

>try-with-resources 文は Java SE 7 で導入された構文です。try-with-resources 文の導入に合わせて Java SE API でクローズ処理を必要とするものの多くが自動クローズに対応するよう書き換えられましたが、XML パーサーなど一部の API では対応がなされていないため注意が必要です (XML パーサーの仕様には Java 外部の標準化団体も関わっているため容易に変更できなかったものと推察されます)。

- 書式: `try ( resources ) try_block catch ( CatchType e ) catch_block finally finally_block`
- `try ( resources ) try_block` を try 節、`catch ( CatchType e ) catch_block` を catch 節、`finally finally_block` を finally 節と呼びます。
- try 節はキーワード `try`、この try-with-resources 文でオープンするリソースのリスト `resources`、それに続くブロック `try_block` からなり、ブロックに囲まれた範囲でスローされる例外が処理の対象であることを示します。try 節は必ず必要です。
  - オープンするリソースは `T var = expr` で宣言されます (`T`: クラス名、`var` ローカル変数名、`expr`: リソースをオープンする式)。複数のリソースを同時にオープンすることも可能で、その場合はリソースを `;` で区切ります。
  - try 節でオープンされたリソースは、try 文終了時に自動的にクローズされます (自動クローズ処理に対応できるリソースには条件があります)。
- catch 節は書式 `catch (CatchType e) catch_block` からなり、キーワード `catch`、キャッチする例外 `CatchType e`、例外処理を記述するブロック `block` からなります。
  - `CatchType` はキャッチする例外のクラスを表し、複数指定することが可能です (マルチキャッチ)。`CatchType` を複数指定する場合は `|` で繋ぎます。
  - `e` は各 catch 節でのみ有効なローカル変数として扱われます。
- catch 節はキャッチする対象の例外クラスの分だけ繰り返し記述することができます。catch 節は、場合によっては完全に省略することもできます。
- finally 節はキーワード `finally` とそれに続くブロック `finally_block` からなり、try 文を終了する直前に実行する処理を記述します。catch 節の処理は対応する例外がスローされないと実行されませんが、finally 節の処理は catch 節の実行有無にかかわらず必ず実行されます。finally 節は書式 (1) に示すように、場合によっては省略することもできます。
  - try 節でオープンしたリソースについては finally 節でクローズする必要はありません。try-with-resources 文を終了する際、自動的にクローズされるためです。
  - 自動クローズ処理に対応していないリソースは try 節に記述できないため、try-catch 文と同様に finally 節でクローズすることになります。
- catch 節と finally 節はともに省略可能です。try-catch 文と異なり両方を同時に省略することもできます (リソースのオープンと自動クローズのみに使用する場合)。

try-with-resources 文は [11 章](chapter11.md)で具体例を示します。

## 6.6. throws 句

throws 句は、自メソッドでは例外を処理せず、呼び出し元で例外を処理してほしい場合に使用します。具体的には、以下のようにメソッド `throws 例外クラス` を宣言します。

```java
public int read(byte[] buf) throws IOException {
    ...
    // エラー発生のため IOException をスローする
    throw new IOException();
    ...
}
```

メソッド同様に、コンストラクタに対しても throws 句を用いることができます。

```java
public Integer(String s) throws NumberFormatException {
    ...
    // エラー発生のため NumberFormatException をスローする
    throw new NumberFormatException();
}
```

例外は、それをスローしたメソッド内の try-catch 文で処理するのがベストですが、実際には例外をスローしたメソッド側ではどのように対処すべきか判断できない場合がほとんどで、基本的には throws 句を使用することになります。throws 句で宣言する例外は複数あっても良く、また実際にスローする例外クラスのスーパークラスであっても構いません。非チェック例外 (`RuntimeException` とそのサブクラス) は throws 句に宣言する必要はありませんが、禁止されているわけではなく、ドキュメンテーション的な意味合いで宣言する場合があります。なお、多くの統合開発環境では、try-catch 文を自動生成する際に throws 句を参照して catch 節を組み立てます。

## 6.7. 例外の再スローと例外連鎖

### 6.7.1. 例外の再スロー

catch 節でキャッチした例外は、throw 文を使用して再びスローすることができます。

```java
public int read(byte[] buf) throws IOException {
    ...
    try {
        ...
    } catch (IOException e) {
        ...
        // 例外の再スロー
        throw e;
    }
    ...
}
```

例外の再スローは、例えば例外をキャッチした記録だけを取り、その他の処理を呼び出し元に委ねる場合などに使用します。

また、catch 節で例外をキャッチした後、別の例外オブジェクトを生成してスローすることもできます (例外の付け替え)。

```java
public int read(byte[] buf) throws Exception {
    ...
    try {
        ...
    } catch (IOException e) {
        ...
        // アプリケーション独自の ApplicationException に付け替える
        throw new ApplicationException("ERROR-00600");
    }
    ...
}
```

例外の付け替えは、チェック例外を非チェック例外に変える場合や、Java 標準の例外をアプリケーション独自の例外に変える場合などに使用します。特に非チェック例外への変換は、呼び出し元でのキャッチが不要になるため、特別なリカバリ処理を要しないアプリケーションに向いています。

Java 標準 API は元々主要な例外クラスがチェック例外とされてきました。Java の最初のターゲットであった組み込み分野では、エラー発生時のリカバリ処理が欠かせなかったためと考えられます。しかし、Java EE が対象とする企業システムでは、例えばデータベースのアクセスエラーが発生した場合などは処理続行不可能となるため即座にシステムダウンとすることが一般的です。Java の古典的なデータベース操作 API である JDBC では、データベースのエラーが発生した場合にはチェック例外 `SQLException` をスローするため、データベースにアクセスするアプリケーションでは `SQLException` の例外処理が常に付きまとっていました。現在ではより新しい Java Persistence API が提供され、こちらではデータベースのエラーが発生した場合に非チェック例外 `PersistenceException` をスローするため、冗長な例外処理を回避できるようになっています。

### 6.7.2. 例外連鎖

前節の第 2 の例で例外オブジェクトの付け替えを紹介しましたが、付け替えた例外オブジェクトに元の例外オブジェクトを関連付けてスローすることができます。これを「例外連鎖」といい、元の例外オブジェクトを「原因例外」と呼びます。例外連鎖は原則として Java 標準 API に含まれる例外クラスにはすべて備わっており、例外クラスのコンストラクタの引数に原因例外を設定する書式が存在しています。例外連鎖を使用するか否かはプログラマの裁量に委ねられますが、より多くの情報を呼び出し元に伝えることができるため、可能な限り使用した方が良いでしょう (呼び出し元で原因例外の情報が不要であれば無視することもできます)。

ただし、例外連鎖の実装は必須ではなく、特にユーザーが実装した例外クラスでは例外連鎖がサポートされていない場合があります。反対に、例外連鎖を必須とする例外クラス (コンストラクタの引数に原因例外を必ず設定しなければならない) も存在します。Java 標準 API の `InvocationTargetException` や `UncheckedIOException` は例外連鎖が必須となる代表例です。

```java
public int read(byte[] buf) {
    ...
    try {
        ...
    } catch (IOException e) {
        throw new UncheckedIOException(e);
    }
    ...
}

public void doProcess() {
    ...
    try {
        ...
    } catch (UncheckedIOException e) {
        // e.getCause() で原因例外 (ここでは IOException のインスタンス) を取得する
        // 原因例外が設定されていない場合は e.getCause() の値が null となるため、その対策をしておく
        // (ただし UncheckedIOException に限っては必ず原因例外を持つため、対策はなくても可)
        if (e.getCause() != null) {
            // e.getCause().printStackTrace() で標準エラー出力に例外のスタックトレースを出力する
            // スタックトレースは例外がスローされてから Java VM に届くまで (= プログラムが停止するまで) の詳細な記録である
            // 原因例外を含むすべての例外の情報を含み、プログラム中で例外がスローされた箇所が特定されている
            e.getCause().printStackTrace();
        }
    }
    ...
}
```

>本節で取り上げた例外連鎖は J2SE 1.4 以降に例外オブジェクトの統一仕様として導入されたものです。`InvocationTargetException` や `SQLException` など一部の例外ではそれ以前から独自の例外連鎖の仕組みを持っていますが、現在では独自の例外連鎖は使用せず、統一仕様の例外連鎖を使用してください。

## 6.8. 例外処理で避けたいこと

### 6.8.1. catch 節で何もしない

try-catch 文で例外をキャッチしても、catch 節で何も処理をしないと、原因をわからなくするどころか例外自体をなかったことにしてしまいます。これを俗に「例外を握りつぶす」行為といい、Java の開発現場で最も忌避される手法です (その割に例外を握りつぶしてしまう人が後を絶たないのは困りものですが)。例外を握りつぶすことは、例外処理の機構そのものを破壊することとほぼ等しいため、絶対に行わないようにしましょう。

```java
try {
    ...
} catch (Exception e) {
    // 何もしない (俗に「例外を握りつぶす」という)
}
```

### 6.8.2. RuntimeException の濫用と例外連鎖の不使用の組み合わせ

`RuntimeException` は標準で用意されている汎用の非チェック例外であり、呼び出し元で try-catch 文を使用する必要がなくなるため、使い方によっては非常に便利です。しかし、みだりに使用するのも考え物です。特にチェック例外を catch 節で `RuntimeException` に付け替え、その際に原因例外を設定しない場合、スタックトレースでは `RuntimeException` に付け替えたところまでしか追跡することができず、エラーの原因を示す例外を特定できなくなってしまいます。そもそも `RuntimeException` は非チェック例外すべてのスーパークラスであり、何が原因で例外スローに至ったのかクラス名だけではわからない欠点があります。非チェック例外をスローする場合は `RuntimeException` のスローはできるだけ減らし、原因を示すような名前を持つ非チェック例外 (`RuntimeException` のサブクラス) を必要に応じて作成してスローするのが良いでしょう。

```java
try {
    ...
} catch (IOException e) {
    // RuntimeException の濫用 + 例外連鎖の不使用
    // 原因例外がないためこれ以上エラーを追跡できなくなる
    throw new RuntimeException();
}
```

### 6.8.3. Throwable のキャッチおよびスロー

try-catch 文で `Throwable` をキャッチするようなコーディングは避けてください。`Exception`、`RuntimeException` だけでなく `Error` までキャッチしてしまう恐れがあるためです。`Error` は Java VM のエラーなど深刻な状態を表すため、特別な理由がなければキャッチすべきではない例外オブジェクトです。仮に `Error` をキャッチしてもアプリケーションで対処できることはありません。

```java
try {
    ...
} catch (Throwable t) {
    // 本来キャッチすべきでない Error もキャッチしてしまう
    // Error は Java VM のエラーなど深刻な状態を表すため、特別な理由がなければキャッチすべきではない
    ...
}
```

同じ理由で、throws 句に `Throwable` を指定するのもいけません。呼び出し元が `Throwable` をキャッチせざるを得なくなり、先に挙げた「`Error` をキャッチしてしまう可能性」が発生するためです。

```java
// 呼び出し元が Throwable をキャッチせざるを得なくなる 
public final void doProcess() throws Throwable {
    ...
}
```

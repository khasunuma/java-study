# 4. 例外処理

コンストラクタやメソッドの処理実行中に何らかの異常が発生した場合に、プログラムまたは Java VM が処理を中断して対策を講じる仕組みを「例外処理」と呼びます。例外処理では、異常が発生した箇所で「例外のスロー」という形でプログラムを一時中断します。中断したプログラムは「例外のキャッチ」によってリカバリ処理を行います。

例外処理は Java をはじめとするオブジェクト指向言語で多く採用されている仕組みで、プログラムの正常系処理と異常系処理を明確に分離するのに役立ちます。例外処理をサポートしていないプログラミング言語では戻り値に特別な値を設定することで正常系処理と異常系処理を区別しています。

## 4.1. 例外オブジェクト

例外のスローにおいては、異常の原因を表すための情報を例外オブジェクトという形で付加します。例外をキャッチしてリカバリ処理を行う際には、例外オブジェクトを参照して必要な処理を行うことになります。例外オブジェクトは、例えば C++ では任意のデータを用いることができますが、Java では例外オブジェクト専用のクラスが用意されており、それらを使用しなければなりません。

Java の例外オブジェクトは `Throwable` クラスとそのサブクラスのインスタンスである必要があり、以下のように細分化されています。

* `Throwable` : すべての例外オブジェクトのスーパークラス。サブクラスは `Error` および `Exception` だけであり、キャッチする意味を持たない。
  * `Error` : サブクラスを含め主に Java VM のエラーを表す。キャッチすべきではない (非チェック例外)。
  * `Exception` : サブクラスを含めプログラム内で発生したエラー一般を表す。キャッチしなければならない (チェック例外)。
    * `RuntimeException` : サブクラスを含めプログラム内で発生したエラーのうちリカバリ不能なものを表す。Java SE においては基本的にバグが起因であり、キャッチする必要はない (非チェック例外)。Java EE においてはキャッチ任意の一般例外を意味する場合もある。

## 4.2. 例外のスロー

処理中にエラーその他が発生する場合、throw 文を用いて例外をスローすることができます。例外をスローした場所で処理は中断されます。

```
throw <例外オブジェクト>;
```

(例) メソッド引数に不正な値が渡された場合で、IllegalArgumentException をスローする
```
throw new IllegalArgumentException();  // 引数に不正な値が渡された
```

アプリケーションで throw 文を記述する場合、その多くは引数の妥当性チェック (バリデーション) でのエラーであり、必然的に非チェック例外 (具体的には `RuntimeException` のサブクラス) が多くなりますが、ファイル操作時などでチェック例外 (`IOException`) をスローする場合もあります (`IOException` のスローについては、アプリケーションから呼び出す Java 標準 API がスローする場合が大半を占めます)。

以下にアプリケーションでよくスローする例外のクラスを示します。

|例外クラス|意味|
|----|----|
|`NullPointerException`|引数が null である (*1)|
|`IllegalArgumentException`|引数に不正な値が渡された (引数が null の場合にも用いる場合がある)|
|`NumberFormatException`|文字列が数値に変換できない書式になっている|
|`UnsupportedOperationException`|メソッドの実行はサポートされていない (未完成のメソッドで処理を仮置きする場合等にも使う)|
|`UncatchedIOException`|(後述の catch 節にて) IOException を非チェック例外に変換する|
|`WebApplicationException`|Java EE の RESTful Web サービス API (JAX-RS) でエラーを返す|

(*) `NullPointerException` は、`null` のインスタンスのメソッドを呼び出そうとした場合に Java VM がスローする例外でもあります。特にメソッドの戻り値が `null` になりうる場合において、`null` に対する処理 (`null` の場合はメソッドを呼び出さない、等) が漏れているときに発生しやすいバグです。戻り値の型を `Optional` クラスにして `null` のチェックを強制する仕組みも備わっていますが、`Optional` クラスであっても不適切な処理により `NullPointerException` がスローされてしまうため、やはり注意は必要です。

## 4.3. 例外処理

チェック例外はアプリケーションのどこかでキャッチしてリカバリ処理を行う必要があります。非チェック例外の場合はキャッチの対象外ですが (Java VM が処理します)、`RuntimeException` とそのサブクラスについては必要であればキャッチしても構いません。

例外の処理方法については、メソッド内で例外を処理する try-catch 文と、呼び出し元に例外処理をゆだねる throws 句の 2 通りがあります。try-catch 文の簡略表記として、ファイル等の処理に便利な try-with-resources 文もありますが、それについては後述します。

### 4.3.1. try-catch 文

try-catch 文は基本的に以下の形式になります。

```
try {
    // 例外がスローされる可能性がある処理
} catch (例外) {
    // 例外に対するリカバリ処理
} finally {
    // try-catch 構文の最後に必ず実行される処理
}
```

キャッチすべき例外が複数存在する場合は、以下の例のように `catch (例外) { ... }` が複数 (0 個以上) 存在します。また、`finally { ... }` はファイルやネットワークなど、終了処理を必ず行わなければならない処理のために用意されており、処理が不要な場合は省略できます。実は try-catch 文で必須なのは try 節だけで、catch 節と finally 節の両方を省略することもできますが、後述の try-with-resources 文を除き何らかの処理が存在しているはずです。

catch 節は上から順に例外オブジェクトと照合され、処理されるため、サブクラスの方から順に記述する必要があります。例えば、`FileNotFoundException`、`IOException`、`Exception` を処理する場合、`IOException` は `Exception` のサブクラス、`FileNotFoundException` は `IOException` のサブクラスであるため、`FileNotFoundException`、`IOException`、`Exception` の順に catch 節を記述します。

```
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

```
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

```
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

### 4.3.2. throws 句

throws 句は、スローする例外を呼び出し元で処理してほしい場合に使用します。具体的には、以下のようにメソッド `throws 例外クラス` を宣言します。

(例) メソッドに対する throws 句
```
public int read(byte[] buf) throws IOException {
    ...
    // エラー発生のため IOException をスローする
    throw new IOException();
    ...
}
```

メソッド同様に、コンストラクタに対しても throws 句を用いることができます。

(例) コンストラクタに対する throws 句
```
public Integer(String s) throws NumberFormatException {
    ...
    // エラー発生のため NumberFormatException をスローする
    throw new NumberFormatException();
}
```

例外は、それをスローしたメソッド内の try-catch 文で処理するのがベストですが、実際には例外をスローしたメソッド側ではどのように対処すべきか判断できない場合がほとんどで、基本的には throws 句を使用することになります。throws 句で宣言する例外は複数あっても良く、また実際にスローする例外クラスのスーパークラスであっても構いません。非チェック例外 (`RuntimeException` とそのサブクラス) は throws 句に宣言する必要はありませんが、禁止されているわけではなく、ドキュメンテーション的な意味合いで宣言する場合があります。なお、多くの統合開発環境では、try-catch 文を自動生成する際に throws 句を参照して catch 節を組み立てます。

### 4.3.3. 例外のマルチキャッチ

try-catch 文の catch 節では、複数の例外処理を 1 つにまとめることができます。これを例外のマルチキャッチといいます。マルチキャッチでは、対象となる例外クラスを `|` でつないで並べます。

(例) マルチキャッチを使用しない場合
```
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

(例) マルチキャッチを使用した場合
```
try {
    ...
} catch (IllegalAccessException | InstantiationException e) {
    // 例外処理; IllegalAccessException、InstantiationException で共通の処理
    ...
}
```

実は第 2 の例のようにマルチキャッチを行っても、コンパイル後には第 1 の例のようなマルチキャッチを使わないコードに変換されます。catch 節の宣言順序もマルチキャッチでの宣言順になるようです。

### 4.3.4. 例外の再スロー

catch 節でキャッチした例外は、throw 文を使用して再びスローすることができます。

(例) 例外の再スロー; キャッチした例外オブジェクトをそのままスローする場合
```
public int read(byte[] buf) throws IOException {
    ...
    try {
        ...
    } catch (IOException e) {
        ...
        // 
        throw e;
    }
    ...
}
```

例外の再スローは、例えば例外をキャッチした記録だけを取り、その他の処理を呼び出し元に委ねる場合等に使用します。

また、catch 節で例外をキャッチした後、別の例外オブジェクトを生成してスローすることもできます (例外の付け替え)。

(例) 例外の再スロー; 別の例外オブジェクトを生成してスローする場合 (例外の付け替え)
```
public int read(byte[] buf) throws Exception {
    ...
    try {
        ...
    } catch (IOException e) {
        ...
        // 
        throw new Exception();
    }
    ...
}
```

例外の付け替えは、チェック例外を非チェック例外に変える場合や、Java 標準の例外をアプリケーション独自の例外に変える場合などに使用します。特に非チェック例外への変換は、呼び出し元でのキャッチが不要になるため、特別なリカバリ処理を要しないアプリケーションに向いています。

>Java 標準 API は元々主要な例外クラスがチェック例外とされてきました。Java の最初のターゲットであった組み込み分野では、エラー発生時のリカバリ処理が欠かせなかったためと考えられます。しかし、Java EE が対象とする企業システムでは、例えばデータベースのアクセスエラーが発生した場合などは処理続行不可能となるため即座にシステムダウンとすることが一般的です。Java の古典的なデータベース操作 API である JDBC では、データベースのエラーが発生した場合にはチェック例外 `SQLException` をスローするため、データベースにアクセスするアプリケーションでは `SQLException` の例外処理が常に付きまとっていました。現在ではより新しい Java Persistence API が提供され、こちらではデータベースのエラーが発生した場合に非チェック例外 `PersistenceException` をスローするため、冗長な例外処理を回避できるようになっています。

### 4.3.5. 例外連鎖

前節の第 2 の例で例外オブジェクトの付け替えを紹介しましたが、付け替えた例外オブジェクトに元の例外オブジェクトを関連付けてスローすることができます。これを「例外連鎖」といい、元の例外オブジェクトを「原因例外」と呼びます。例外連鎖は原則として Java 標準 API に含まれる例外クラスにはすべて備わっており、例外クラスのコンストラクタの引数に原因例外を設定する書式が存在しています。例外連鎖を使用するか否かはプログラマの裁量に委ねられますが、より多くの情報を呼び出し元に伝えることができるため、可能な限り使用した方が良いでしょう (呼び出し元で原因例外の情報が不要であれば無視することもできます)。

>ただし、例外連鎖の実装は必須ではなく、特にユーザーが実装した例外クラスでは例外連鎖がサポートされていない場合があります。反対に、例外連鎖を必須とする例外クラス (コンストラクタの引数に原因例外を必ず設定しなければならない) も存在します。Java 標準 API の `InvocationTargetException` や `UncheckedIOException` は例外連鎖が必須となる代表例です。

```
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

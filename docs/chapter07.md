# 7. クラスの継承

## 7.1. 抽象クラスと抽象メソッド

これまで見てきたクラスはすべての定義が揃ったクラスで「具象クラス」と呼ばれます。一方で、継承されることを前提に一部未完成の状態で定義されたクラスを「抽象クラス」といいます。抽象クラスはそれ自身のインスタンスは生成できませんが、サブクラスの具象クラスに対して抽象クラスで宣言したメソッドでアクセスすることができます。これをポリモーフィズムといいます。ポリモーフィズムは具象クラスから具象クラスへの継承でも実現できますが、抽象クラスから具象クラスへの継承や、後述するインタフェースの実装による実現が多く用いられます。

抽象クラスは、以下のような書式で定義することができます。

```java
package package_name ;

[ import package_name . { ClassName | * } ; [ import ... ; ] ... ]

// ClassName : クラス名
[ public ] abstract class ClassName {
    // コンストラクタ定義
    [ constructor [ constructor ... ] ]

    // フィールド定義
    [ field [ field ... ] ]

    // メソッド定義
    [ method [ method ... ] ]

    // 抽象メソッド定義
    [ abstract_method [ abstract_method ... ] ]
}
```

抽象クラスの定義には `abstract` 宣言が必ず含まれます。これにより抽象クラス自身のインスタンスは生成できなくなります。コンストラクタ、フィールドおよびメソッドについては、具象クラスの書式と同様です。

抽象メソッドは以下のように定義できます。メソッドの定義から処理部分がそのまま抜け落ち、`abstract` 宣言が含まれる形となります。

```java
// methodName : メソッド名、argument : 仮引数、ReturnType : 戻り値の型
[ public | protected | private ] abstract ReturnType methodName ( [ argument [, argument ... ] ) ;
```

サブクラスとなる具象クラスでは、最終的にはすべての抽象メソッドを以下のようにオーバーライドする必要があります。

```java
// methodName : メソッド名、argument : 仮引数、ReturnType : 戻り値の型
@Override
[ public | protected | private ] [final] ReturnType methodName ( [ argument [, argument ... ] ) {
    // メソッドの処理記述
}
```

## 7.2. クラスの継承

スーパークラスを継承してサブクラスを定義するには、`extends` キーワードを用います。

```java
package package_name ;

[ import package_name . { ClassName | * } ; [ import ... ; ] ... ]

// ClassName : クラス名、SuperClassname : スーパークラス名
[ public ] abstract class ClassName extends SuperclassName {
    // コンストラクタ定義
    [ constructor [ constructor ... ] ]

    // フィールド定義
    [ field [ field ... ] ]

    // メソッド定義
    [ method [ method ... ] ]

    // 抽象メソッド定義
    [ abstract_method [ abstract_method ... ] ]
}
```

サブクラスは `abstract` を付加して抽象クラスにすることができます。スーパークラスとサブクラスの双方が抽象クラスの場合、必ずしもすべての抽象メソッドをオーバーライドする必要はありませんが、サブクラスを具象クラスとする場合にはすべての抽象メソッドをオーバーライドする必要があります。サブクラスが具象クラスの場合に限り `final` を付加することができます。従って、`abstract` と `final` を同時に付加することはできません。

また、サブクラスではスーパークラスのメソッドをオーバーライドすることができます (前述の書式を参照のこと)。ただし、`final` を付加したメソッドについてはオーバーライドすることができません。オーバーライドしたメソッドからスーパークラスの (オーバーライド元) メソッドを呼び出したい場合には、メソッド名の前に `super .` を付加します。`super .` はスーパークラスの他のメソッドの呼び出しやフィールド (`public` または `protected`) を参照する場合にも付加することができます。スーパークラスのメソッドやフィールドにアクセスする場合は先頭に `super .` を付加するルールにしても良いでしょう。

(例) オーバーライド元のメソッドの呼び出し

```java
@Override
public int getValue() {
    // オーバーライド元の getValue() メソッドを呼び出す
    return super.getValue() * 2;
}
```

なお、`super .` に対して、自クラスのメソッドやフィールドにアクセスする場合、先頭に `this .` を付加します。`this .` は主にメソッド内で定義したローカル変数名とフィールド名が重複した場合に、フィールドの先頭に付加して使用します。ローカル変数とフィールドで同じ名前を使用することは許可されており、特に setter メソッドの引数名とフィールド名を一致させて対応付けをわかりやすくするテクニックが用いられます。その際、フィールド側に `this .` を付加して区別します。その他、オーバーロードしたメソッドを呼び出す際にわかりやすさのため `this .` を付加する場合があります。

(例) フィールドと setter メソッドの定義

```java
private String name;

public void setName(String name) {
    // フィールド名と引数名は一致しても構わないが、フィールド側に this . を付けること
    this.name = name;
}
```

## 7.3. コンストラクタの詳細

コンストラクタについては、メソッドと異なりオーバーライドという仕組みがありません。代わりに、必要に応じてスーパークラスのコンストラクタを呼び出す必要があります。

### 7.3.1. スーパークラスのコンストラクタ呼び出し

スーパークラスのコンストラクタを呼び出す場合には、`super ( [ 実引数のリスト ] ) ;` を使用します。なお、スーパークラスのコンストラクタ呼び出しは、コンストラクタの処理の最初に実行しなければなりません。

```java
public class ZipEntry {  // 実際の ZipEntry は Cloneable インタフェースを実装しているが、ここでは割愛する
    ...
    public ZipEntry(String name) {
        ...
    }
    ...
}

public class JarEntry extends ZipEntry {
    ...
    public JarEntry(String name) {
        super(name);  // スーパークラス ZipEntry のコンストラクタを呼び出す
        ...
    }
    ...
}
```

サブクラスのコンストラクタと呼び出し先のスーパークラスのコンストラクタは、引数が一致していなくても構いません。また、スーパークラスのコンストラクタのスコープは `public` または `protected` (同じパッケージ内であればパッケージ・プライベートも可) であれば問題ありません。

```java
abstract class AbstractStringBuilder {
    // AbstractStringBuilder はパッケージ・プライベートなクラスとして定義される
    // 実際の AbstractStringBuilder は Appendable, CharSequence インタフェースを実装しているが、ここでは省略する
    
    AbstractStringBuilder(int capacity) {
        ...
    }
    ...
}

public final class StringBuilder extends AbstractStringBuilder {
    // StringBuilder は AbstractStringBuilder と同一パッケージ (java.lang)
    // 実際の StringBuilder は Serializable, CharSequence インタフェースを実装しているが、ここでは省略する

    public StringBuilder() {
        // 呼び出すスーパークラスのコンストラクタの引数は、サブクラスのコンストラクタと異なっていても構わない
        super(16);
        ...
    }

    public StringBuilder(int capacity) {
        // スーパークラスとサブクラスのコンストラクタ引数が一致しているパターン
        super(capacity);
        ...
    }
    ...
}
```

### 7.3.2. 他のコンストラクタの呼び出し

サブクラスが複数のコンストラクタを持っている場合、スーパークラスのコンストラクタ呼び出しに変えて、同じクラスの他のコンストラクタを呼び出すことができます。その場合は呼び出しに `this ( [ 実引数のリスト ] ) ;` を使用します。

```java
public final class URI {

    public URI(String scheme, String userInfo, String host, int port, String path, String query, String fragment)
            throws URISyntaxException {
        ...
    }
    
    public URI(String scheme, String host, String path, String fragment)
            throws URISyntaxException {
        // URI クラスの上記で定義されたコンストラクタを呼び出す
        this(scheme, null, host, -1, path, null, fragment);
    }
}
```

### 7.3.4. クラス継承におけるデフォルト・コンストラクタの扱い

クラス継承において、スーパークラスにデフォルト・コンストラクタが生成される (= コンストラクタを省略している) 場合、サブクラスのコンストラクタからは `super();` で呼び出すか、または呼び出し自体を省略することができます (省略時は暗黙的に `super();` の呼び出しを行います)。これは、デフォルト・コンストラクタが `public` スコープの引数なしコンストラクタであることを示しています。

一方、サブクラスにデフォルト・コンストラクタが生成される (= コンストラクタを省略できる) 条件は、スーパークラスにデフォルト・コンストラクタが存在する (= コンストラクタを省略している) か、またはスコープが `public`、`protected` (同一パッケージであればパッケージ・プライベートも可) の引数なしコンストラクタが存在している場合に限定されます。サブクラスのデフォルト・コンストラクタは暗黙的に `super();` の呼び出しを行います。

スーパークラスに `private` な引数なしコンストラクタが存在する場合は特殊な状況になります。まず、この状況ではスーパークラスの引数なしコンストラクタ
サブクラスから呼び出すことができません。そのため、暗黙的に `super();` の呼び出しを行うデフォルト・コンストラクタは生成することができません。コンストラクタを明示的に定義する場合でも、`super()` の呼び出しはできず、コンストラクタ呼び出しを省略することもできません。従って、スーパークラスのコンストラクタのうち、スコープが `public`、`protected` (同一パッケージであればパッケージ・プライベートも可) かつ引数ありコンストラクタを明示的に呼び出す必要があります。

>`private` な引数なしコンストラクタのみが定義されているクラスは、`final` が付加されていなくても、実質的に継承することができません。また、同じクラスの static メソッド以外からインスタンスを生成することもできません。これは、通常のメソッド呼び出しに必要なインスタンス生成自体も抑制する効果があるためです。

### 7.3.4. private コンストラクタの使い方

`private` コンストラクタは、前述のようにクラスの継承を制限する場合があることから、用途が限定されます。しかし、`private` コンストラクタならではの活用方法もあります。ここではそのうち、ユーティリティ・クラス、ファクトリ・メソッド、シングルトンの 3 つの例を挙げます。

`static` メソッドと定数フィールドのみを持つクラスをユーティリティ・クラスと呼びます。Java 標準 API に含まれる `Math` クラスや `Collections` クラスのように、アルゴリズムのみを定義するクラスが該当します。ユーティリティ・クラスはインスタンスを生成する必要がなく、また継承も行いません。そこでクラスには `final` を付加して継承しないことを明示し、引数なし `private` コンストラクタのみを定義して継承とインスタンス生成の両方を抑制します。以下に Java 標準 API の `Math` クラスの実装を抜粋します。

(例) ユーティリティ・クラス

```java
public final class Math {
    private Math() {

    }

    public static int max(int a, int b) {
        return (a >= b) ? a : b;
    }
    ...
}
```

インスタンスの生成において、コンストラクタによる初期化に加えて追加の処理を必要とする場合などは、ファクトリ・メソッドと呼ばれるメソッドが用意される場合があります。コンストラクタとファクトリ・メソッドを同一クラス上に定義する場合には、ファクトリ・メソッドを `public` な `static` メソッドとして定義し、コンストラクタを `private` (継承を許可する場合は `protected`) スコープで定義します。ファクトリ・メソッドにはインスタンス生成に関わる処理を隠ぺいするだけでなく、コンストラクタの表現力不足 (コンストラクタでは同じ引数リストを持つものを複数定義できないが、メソッドであれば名前を変えることで同じ引数リストを持つものを定義可能、等) を補う意味もあります。以下に Java 標準 API の `LocalDate` クラスの実装を一部簡略化して示します。

(例) ファクトリ・メソッド

```java
public final class LocalDate {

    private LocalDate(int year, int month, int dayOfMonth) {
        ...
    }

    public static LocalDate of(int year, int month, int dayOfMonth) {
        // 実際の LocalDate では複数のメソッドで処理しているが、ここでは省略する
        ...
        return new LocalDate(year, month, dayOfMonth);
    }
    ...
}
```

`LocalDate` クラスは日付を表しますが、日付の表現には様々な方法がある (年-月-日、年-月の名前-日、年-年始からの通算日、等) だけでなく、イミュータブルなクラスとして設計されているため、日付の加算・減算等でもインスタンスの生成が必要となります。これらをすべてコンストラクタで実現しようとすると無理が生じるため、コンストラクタは `private` スコープとしてクラスの内部に隠ぺいし、代わりにファクトリ・メソッドを豊富に用意することで対応してます。

なお、`Byte`、`Integer`、`Long`、`Float`、`Double` の各クラスは `public` なコンストラクタと `valueOf` ファクトリ・メソッドの両方を持つ特殊な例です。これらのクラスの `valueOf` ファクトリ・メソッドは値の全部または一部をキャッシュするため、コンストラクタでインスタンスを生成する場合と比較して効率が良いとされます。

>将来の Java では `Byte`、`Integer`、`Long`、`Float`、`Double` の `public` なコンストラクタは廃止される (`private` スコープに変更される) 予定になっています。

シングルトンは、インスタンスを 1 つしか生成しないクラスを実現するためのテクニックです。アプリケーション全体で共有しておきたいような情報を保持するために使用します。Java EE では CDI や EJB などのコンテナ (サーバー側機能) でインスタンスが 1 つだけになるように制御できますが、Java SE にはそのような機能が備わっていないため、代わりに `private` コンストラクタを中心に実装します。Java SE におけるシングルトンの実現方法は複数ありますが、最も簡単なものを以下に示します。

(例) シングルトン

```java
public final class Singleton {
    // private な static フィールドに唯一のインスタンスを設定する
    private static final Singleton instance = new Singleton();

    private Singleton() {
        ...
    }

    public static Singleton getInstance() {
        // instance の値は常に同じであるため、このメソッドの戻り値は常に同じ値となる
        return instance;
    }
    ...
}
```

この例では、コンストラクタを `private` スコープで定義していることから、その他のクラスや、このクラスの通常のメソッドではインスタンスを生成できません。また、`final` を付加した `static` フィールド `instance` の初期値としてインスタンスの生成 (コンストラクタの呼び出し) を行っていることから、`instance` の値は変更できず、また他の場所に同様の処理がないため、インスタンスは `static` フィールド `instance` に設定されたものが唯一となります。`static` メソッド `getInstance` は初期化済みの `instance` の値を返すだけで、これは常に同じ値となります。

## 7.4. java.lang.Object クラス

`java.lang.Object` クラスは、Java においてすべてのクラスのスーパークラスとなるものです。特にスーパークラスを指定しない場合には `Object` クラスがスーパークラスとして用いられます。例えば、

```java
public class SomeClass {
    ...
}
```

というクラス定義は、

```java
public class SomeClass extends Object {
    ...
}
```

と等価です。

`Object` クラスには `public` な引数なしコンストラクタと、いくつかのメソッドが用意されています。

|メソッド名 |スコープ    |説明|
|----------|:---------:|-----------------------------------------------------------------------------|
|`getClass`|`public`   |クラス (継承している場合はサブクラス) のクラス・リテラルを返す。オーバーライド不可。|
|`hashCode`|`public`   |インスタンスのハッシュ値を返す。`HashMap` 等で使用する場合にはオーバーライドすること。|
|`equals`  |`public`   |インスタンスを比較する。インスタンスの比較を行う場合にはオーバーライドすること。|
|`clone`   |`protected`|インスタンスを複製する。制約が多いので現在ではほとんど使われない。|
|`toString`|`public`   |インスタンスの文字列表現を返す。`System.out.println()` メソッド等の出力結果となるため必要に応じてオーバーライドする。|
|`notify`  |`public`   |マルチスレッド環境で Java VM が使用する。オーバーライド不可。|
|`notifyAll`|`public`  |マルチスレッド環境で Java VM が使用する。オーバーライド不可。|
|`wait`    |`public`   |マルチスレッド環境で Java VM が使用する。3 つのオーバーロードメソッドがある。オーバーライド不可。|
|`finalize`|`protected`|インスタンスの終了処理を行う。オーバーライド時に良くない副作用が確認されているため現在ではほとんど使われない。|

上記のうち実質的にオーバーライドの対象となるのは `hashCode`、`equals`、`toString` の 3 つです。データの格納を目的とするクラスでは、比較等の操作が必須となるため、`hashCode` と `equals` をセットでオーバーライドします。画面出力等も考慮するのであれば、`toString` をオーバーライドして人間が見てわかる形式の文字列表現を返すようにすると良いでしょう。逆にアルゴリズムの記述を目的とするクラスでは、これらのメソッドをオーバーライドする必要はありません。なお、最近の統合開発環境には `hashCode`、`equals`、`toString` の自動生成機能が備わっているため活用してください。

## 7.5. java.lang.Class クラス

Java アプリケーション実行中のクラスやインタフェースそのものを表すものが `Class` という特殊なクラスです。`Class` クラスには、それがどのクラスやインタフェースを表すかを示す「型パラメータ」というものが付加されます。一般には、型パラメータ `T` として `Class<T>` と表現します。例えば、`String` クラスそのものを表すものは `Class<String>` となります。特定のクラスまたはインタフェースを示さない場合には `Class<?>` と表します。

型パラメータは、[9 章](chapter09.md)で紹介するジェネリクス (総称型) で用いるものと同じものです。クラスに対して、それが関連するクラスを明示するために使用されます。型パラメータの実際については [9 章](chapter09.md)もあわせて参照してください。

`Class` クラスのインスタンスは通常の方法では生成できず、以下のいずれかの方法を用います。

- インスタンスの `getClass()` メソッドを呼び出す。
- クラス・リテラルを用いる。

インスタンスの `getClass()` メソッドは本来は `Object` クラスで定義されているものですが、戻り値は実際のインスタンスのクラスを表す `Class` のインスタンスとなります。

クラス・リテラルは以下の書式で表される特殊なリテラルで、いずれも `Class` のインスタンスとなります。

- 書式 (1): `TypeName . class` (`TypeName`: クラス名またはインタフェース名)
- 書式 (2): `type . class` (`type`: プリミティブ型)
- 書式 (3): `TypeName [ ] . class` (`TypeName`: クラス名またはインタフェース名)
- 書式 (4): `type [ ] . class` (`type`: プリミティブ型)
- 書式 (5): `void . class`

書式 (1) および (2) は通常のクラス・リテラル、書式 (3) および (4) は [9 章](chapter09.md) で取り上げる配列のクラス・リテラル、書式 (5) は特定のデータ型を表さない `void` のクラス・リテラルとなります。

なお、`Class` クラスにおいては、列挙型はクラスの一種、アノテーション (注釈) はインタフェースの一種とみなします。

`Class` クラスをアプリケーションで操作することはまずありませんが、Java API の一部に引数として `Class` のインタフェースを要求するものがあるため、この節で取り上げた程度の内容は押さえておきましょう。

## 7.6. 抽象クラス経由のアクセス

具象クラスのインスタンスに対しては、そのスーパークラスである抽象クラスを経由してアクセスすることができます。

(例 1) `OutputStream` を使用してファイルに書き込む

```java
byte[] buf = new byte[256];  // 256 バイトのバッファ (一時保存領域) を確保する
...
OutputStream out = new FileOutputStream("output.dat");  // 出力先 out としてファイル output.dat を指定する
out.write(buf);  // バッファの内容を out に出力する
...
```

(例 2) `OutputStream` を使用してバイト配列 (メモリ) に書き込む

```java
byte[] buf = new byte[256];  // 256 バイトのバッファ (一時保存領域) を確保する
...
OutputStream out = new ByteArrayOutputStream();  // 出力先 out としてバイト配列 (メモリ) を指定する
out.write(buf);  // バッファの内容を out に出力する
...
```

上記 2 例において、相違点が out に設定しているインスタンス (`FileOutputStream` か `ByteArrayOutputStream` かの違い) だけであることに注目してください。各具象クラス固有のメソッドは呼び出せませんが、`OutputStream` で共通に定義されているメソッド (上記の例では `write()` メソッド) はインスタンスのクラスに依存せず使用できます。


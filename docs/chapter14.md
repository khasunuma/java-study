# 14. 標準 API を活用しよう

## 14.1. Properties

`Properties` クラスは、Java VM やアプリケーションの設定情報を保持するために使用するクラスです。JDK 1.0 の頃から存在する最古参の API ですが、数度の改訂を重ねながら (現在開発中の Java SE 9 においても改訂の予定があります) 継続して使用されています。

### 14.1.1. Properties クラスの基本構造

`Properties` クラスは、各設定情報 (エントリ) について名称 (キー) とプロパティ (設定値) がペアとなっており、その集合体となっています。コレクションの `Map` とよく似ています (実際に `Map` インタフェースも実装しています) が、実際にはコレクションの登場によりあまり使用されなくなった `Hashtable` のサブクラスとして実装されています。`Hashtable` に対して、Java VM やアプリケーションの設定情報をファイルから読み込んだり書き出したりする (= 設定情報をファイルに退避させる) ための入出力機能と、若干のユーティリティ・メソッドを追加したものが `Properties` クラスとなります。

以下に `Properties` メソッドが用意しているユーティリティ・メソッドを示します。ただし、`Properties` は `Hashtable` のサブクラス (および `Map` の実装) であるため、これらが持つ汎用的なメソッドを使用することも可能です。

|メソッド名|引数|戻り値|説明|
|--------|--------|--------|--------|
|`getProperty`|`String`|`String`|指定された名称 (キー) のプロパティ (設定値) を取得する (ない場合は `null`)|
|`getProperty`|`String`, `String`|`String`|指定された名称 (キー) のプロパティ (設定値) を取得する (ない場合は第 2 引数で指定した値)|
|`setProperty`|`String, String`|`Object`|指定された名称 (キー) とプロパティ (設定値) を設定する (存在する場合は置き換える)|
|`stringPropertyNames`|N/A|`Set<String>`|すべての名称 (キー) を含む `Set` を取得する|
|`list`|`PrintStream`|N/A|指定された `PrintStream` (`System.out` 等) にプロパティの一覧を出力する|
|`list`|`PrintWriter`|N/A|指定された `PrintWriter` にプロパティの一覧を出力する|

### 14.1.2. システム・プロパティ

システム・プロパティは、Java VM が保持する各種設定情報です。システム・プロパティもまた、`Properties` クラスのインスタンスです。システム・プロパティは `System` クラス (`getProperties()` メソッド) から取得することが可能です。システム・プロパティ自体は読み書き可能となっていますが、Java VM の重要な設定情報も含まれているため、取り扱い (特にプロパティの変更) には注意をしてください。

システム・プロパティはおよそ以下の 4 種類に分類されます。

- Java VM の動作環境によって決められているもの (プラットフォーム間の移植性向上の目的で使用、アプリケーションで変更してはいけない)。
- Java VM の起動オプションによって決まってくるもの (いわゆるシステム情報、アプリケーションで変更してはいけない)。
- Java VM の起動時にユーザーによって設定されたもの (`java` の `-D` オプションで設定可能、アプリケーションで変更してもよい)。
- 実行時にアプリケーションによって追加されたもの。

`System` クラスにはシステム・プロパティを操作するためのユーティリティ・メソッドが用意されています。

|メソッド名|引数|戻り値|説明|
|--------|--------|--------|--------|
|`getProperties`|N/A|`Properties`|システム・プロパティ全体を表す `Properties` のインスタンスを取得する|
|`getProperty`|`String`|`String`|`getProperties().getProperty(String)` の省略形|
|`getProperty`|`String`, `String`|`String`|`getProperties().getProperty(String, String)` の省略形|
|`setProperty`|`String, String`|`Object`|`getProperties().setProperty(String, String)` の省略形|

### 14.1.3. Properties クラスとプロパティ・ファイル

|メソッド名|引数|戻り値|説明|
|--------|--------|--------|--------|
|`load`|`InputStream`|N/A|プロパティ一覧 (行指向形式、ISO-8859-1) を `InputStream` から読み取る|
|`load`|`Reader`|N/A|プロパティ一覧 (行指向形式、UTF-8) を `Reader` から読み取る|
|`loadFromXML`|`InputStream`|プロパティ一覧 (XML 形式、UTF-8) を `InputStream` から読み取る|
|`store`|`OutputStream, String`|プロパティ一覧 (行指向形式、ISO-8859-1) を `OutputStream` に書き込む、第 2 引数はコメント|
|`store`|`Writer, String`|プロパティ一覧 (行指向形式、UTF-8) を `Writer` に書き込む、第 2 引数はコメント|
|`storeToXML`|`OutputStream, String`|プロパティ一覧 (XML 形式、UTF-8) を `OutputStream` に書き込む、第 2 引数はコメント|

行指向形式

comments引数がnullでない場合は、ASCII文字の#、commentsの文字列、および行区切り文字が最初に出力ストリームに書き込まれます。このため、commentsは識別コメントとして使うことができます。改行(「\n」)、キャリッジ・リターン(「\r」)、改行が直後に続くキャリッジ・リターン、のいずれかがコメント内に現れると、それは、Writerによって生成された1個の行区切り文字で置き換えられます。そして、コメント内の次の文字が文字#でも文字!でもなかった場合、その行区切り文字のあとにASCII #が書き出されます。

次に、ASCII文字の#、現在の日時(DateのtoStringメソッドによって現在時刻が生成されるのと同様 'dow mon dd hh:mm:ss zzz yyyy')、およびWriterによって生成される行区切り文字からなるコメント行が書き込まれます。

続いて、このProperties表内のすべてのエントリが1行に1つずつ書き出されます。各エントリのキー文字列、ASCII文字の=、関連付けられた要素文字列が書き込まれます。キーの場合、すべての空白文字は、前に\文字を付けて書き込まれます。要素の場合、埋込み空白文字でも後書き空白文字でもない先行空白文字は、前に\を付けて書き込まれます。キーと要素の文字#、!、=、および: は、必ず正しくロードされるように、前にバックスラッシュを付けて書き込まれます。

XML 形式

```xml
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
    <?xml version="1.0" encoding="UTF-8"?>
    <!-- DTD for properties -->
    <!ELEMENT properties ( comment?, entry* ) >
    <!ATTLIST properties version CDATA #FIXED "1.0">
    <!ELEMENT comment (#PCDATA) >
    <!ELEMENT entry (#PCDATA) >
    <!ATTLIST entry key CDATA #REQUIRED>
```

## 14.2. JAXB

XML ドキュメントと Java のオブジェクト (クラスのインスタンス) の相互変換を実現するための API が JAXB です。Java には XML を扱う API が複数存在しますが、JAXB はその中でも異色の存在で、プログラマが XML パーサーの存在を意識することなく使用できるメリットがあります。変換速度が比較的高速である点も見逃せません (JAXB は内部で高速 XML パーサーである StAX を呼び出します)。

JAXB では変換操作のことをマーシャリング/アンマーシャリングと呼びます。それぞれの意味は以下の通りです。

- マーシャリング (Marshalling) : Java オブジェクトから XML ドキュメントを生成する
- アンマーシャリング (Unmarshalling) : XML ドキュメントから Java オブジェクトを生成する

JAXB には様々な機能が備わっており、ここではほんの一部しか紹介することはできません。JAXB の理解を深めるには以下の連載記事を参考にしてください。

**参考: Java 技術最前線「Java SE 6完全攻略」**

- [第73回 JAXB その1](http://itpro.nikkeibp.co.jp/article/COLUMN/20080530/305406/)
- [第74回 JAXB その2](http://itpro.nikkeibp.co.jp/article/COLUMN/20080606/306845/)
- [第75回 JAXB その3](http://itpro.nikkeibp.co.jp/article/COLUMN/20080613/307995/)
- [第76回 JAXB その4](http://itpro.nikkeibp.co.jp/article/COLUMN/20080620/308966/)
- [第77回 JAXB その5](http://itpro.nikkeibp.co.jp/article/COLUMN/20080627/309642/)
- [第78回 JAXB その6](http://itpro.nikkeibp.co.jp/article/COLUMN/20080704/310147/)
- [第79回 JAXB その7](http://itpro.nikkeibp.co.jp/article/COLUMN/20080711/310608/)
- [第80回 JAXB その8](http://itpro.nikkeibp.co.jp/article/COLUMN/20080711/310608/)

### 14.2.1. Java オブジェクトと XML ドキュメントのマッピング

まず、JAXB でのマーシャリングを前提とした `Person` クラスを用意します。JAXB では Java オブジェクトと XML ドキュメントのマッピングにいくつかの方法を用意していますが、ここではアノテーションを使用したマッピングを使用します。以下の `Person` クラスには既にマッピングのためのアノテーションが指定されていますが、それについては後述します。

```java
@XmlRootElement
public class Person {

    @XmlElement
    private String name;
    
    @XmlElement
    private String email;

    public String getName() { return name; }
    public String getEmail() { return email; }
    public void setName(String name) { this.name = name; }
    public void setEmail(String email) { this.email = email; }
}
```

`Person` クラスは以下のような XML と対応付けられます。

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<person>
  <name>John Doe</name>
  <email>johndoe@example.com</email>
</person>
```

以下、`Person` クラスに付加しているマッピングのためのアノテーションについて説明します。`Person` クラスと XML を見比べながら、両者がどのようにマッピングされているのかを確認してください。

`@XmlRootElement` はクラスに付加するアノテーションで、XML のルート要素とマッピングするために使用します。この例では、`Person` クラスを XML のルートである `person` 要素にマッピングしています。

`@XmlElement` はフィールド等に付加するアノテーションで、XML の要素とマッピングするために使用します。この例では、`name` フィールドを XML の `name` 要素、`email` フィールドを XML の `email` 要素にそれぞれマッピングしています。

### 14.2.2. マーシャリング

前節の `Person` クラスのインスタンスをマーシャリングして XML ファイルに出力するサンプルコードを示します。

```java
Person person = new Person();
person.setName("John Doe");
person.setEmail("johndoe@example.com");

try {
    // JAXBContext : マーシャラ―/アンマーシャラ―の取得に必要
    JAXBContext context = JAXBContext.newInstance(Person.class);

    // マーシャラ―を取得する
    Marshaller marshaller = context.createMarshaller();

    // Java オブジェクトをマーシャリングしてファイルに出力する
    marshaller.marshal(person, new File("johndoe.xml"));
} catch (JAXBException e) {
    // エラー時の処理
    ...
}
```

上記の通り、マーシャリングには `Marshaller` クラスの `marshal` メソッドで行います。`marshal` メソッドは第 2 引数 (出力先の指定) が異なるいくつかのオーバーラード・メソッドが用意されていますが、主に使用するのは下記の 3 つくらいでしょう。

|メソッド名|引数|説明|
|--------|--------|--------|
|`marshal`|`Object, File`|マーシャリングして結果をファイルに出力する|
|`marshal`|`Object, OutputStream`|マーシャリングして結果を `OutputStream` に出力する|
|`marshal`|`Object, Writer`|マーシャリングして結果を `Writer` に出力する|

### 14.2.3. アンマーシャリング

前節で示した XML ファイルをアンマーシャリングして `Person` クラスのインスタンスとして取得するサンプルコードを示します。

```java
try {
    // JAXBContext : マーシャラ―/アンマーシャラ―の取得に必要
    JAXBContext context = JAXBContext.newInstance(Person.class);

    // アンマーシャラ―を取得する
    Unmarshaller unmarshaller = context.createUnmarshaller();

    // XML ドキュメントをアンマーシャリングして Java オブジェクトを生成する
    Person person = (Person) unmarshaller.unmarshal(new File("johndoe.xml"));
} catch (JAXBException e) {
    // エラー時の処理
    ...
}
```

上記の通り、アンマーシャリングには `Unmarshaller` クラスの `unmarshal` メソッドで行います。`unmarshal` メソッドは引数 (入力元の指定) が異なるいくつかのオーバーラード・メソッドが用意されていますが、主に使用するのは下記の 4 つくらいでしょう。なお、戻り値は `Object` クラスになっているため、それぞれのクラスにキャストする必要があります。

|メソッド名|引数|説明|
|--------|--------|--------|
|`unmarshal`|`File`|ファイルからの入力をアンマーシャリングする|
|`unmarshal`|`InputStream`|`InputStream` からの入力をアンマーシャリングする|
|`unmarshal`|`Reader`|`Reader` からの入力をアンマーシャリングする|
|`unmarshal`|`URL`|`URL` からの入力をアンマーシャリングしてする|

### 14.2.4. JAXB ユーティリティ・クラス

ここまで JAXB のマーシャラ―とアンマーシャラ―の使い方について示しました。JAXB は Java オブジェクトと XML ドキュメントを簡単に相互変換してくれる優れものではありますが、同時に以下のような煩わしさも感じたことでしょう。

- 定型コードが多い : `marshal`/`unmarshal` メソッドを呼び出すまでに、`JAXBContext` と `Marshaller`/`Unmarshaller` のインスタンスを取得しなければならない。
- エラー処理が必須 : `JAXBException` はキャッチ例外のため、例外処理のコードが必要

大きく複雑なデータを扱う場合には、これらはそれほどのオーバーヘッドにはなりませんが、小さなデータを数多く扱う場合にはオーバーヘッドを無視できなくなります。こうした問題を解決するため、JAXB では `JAXB` ユーティリティ・クラスを用意しています。`JAXB` クラスは `static` メソッドとして `marshal`/`unmarshal` メソッドが用意されており、メソッド呼び出しだけでマーシャリングとアンマーシャリングを実現できます。これらのメソッドはエラー時にランタイム例外をスローするため、別途例外処理のコードも不要となります。

`JAXB` クラスが用意している主なメソッドを以下に示します

|メソッド名|引数|戻り値|説明|
|--------|--------|--------|--------|
|`marshal`|`Object, File`|N/A|マーシャリングした結果をファイルに出力する|
|`marshal`|`Object, OutputStream`|N/A|マーシャリングした結果を `OutputStream` に出力する|
|`marshal`|`Object, String`|N/A|マーシャリングした結果を文字列に出力する|
|`marshal`|`Object, URI`|N/A|マーシャリングした結果を `URI` に出力する|
|`marshal`|`Object, URL`|N/A|マーシャリングした結果を `URL` に出力する|
|`marshal`|`Object, Writer`|N/A|マーシャリングした結果を `Writer` に出力する|
|`unmarshal`|`File, Class<T>`|`T`|ファイルからの入力をアンマーシャリングする|
|`unmarshal`|`InputStream, Class<T>`|`T`|`InputStream` からの入力をアンマーシャリングする|
|`unmarshal`|`Reader, Class<T>`|`T`|`Reader` からの入力をアンマーシャリングする|
|`unmarshal`|`String, Class<T>`|`T`|文字列からの入力をアンマーシャリングする|
|`unmarshal`|`URI, Class<T>`|`T`|`URI` からの入力をアンマーシャリングする|
|`unmarshal`|`URL, Class<T>`|`T`|`URL` からの入力をアンマーシャリングする|

`JAXB` クラスを用いると、前述のサンプルコードは以下のように書き換えることができます。

```java
Person person = new Person();
person.setName("John Doe");
person.setEmail("johndoe@example.com");

// Java オブジェクトをマーシャリングしてファイルに出力する
JAXB.marshal(person, new File("johndoe.xml"));
```

```java
// XML ドキュメントをアンマーシャリングして Java オブジェクトを生成する
Person person = JAXB.unmarshal(new File("johndoe"), Person.class);
```

## 14.3. Pattern & Matcher

Java の正規表現実装です。正規表現そのものを表す `Pattern` クラスと、正規表現にマッチするかを判定する `Matcher` クラスから構成されます (`Matcher` クラスはマッチの判定だけでなく、マッチ箇所を他の文字列に置換する機能も持ちます)。`String` クラスなどにも正規表現を扱うメソッドが存在していますが、`Pattern` と `Matcher` はその裏方として働きます。ある意味当然のことですが、`Pattern` と `Matcher` を直接使用した方がパフォーマンス上優れています。

### 14.3.1. 簡単なパターンマッチング


### 14.3.2. 正規表現を用いた文字列置換


### 14.3.3. String クラスのメソッドとの対応


### 14.3.4. CSV ファイルを正規表現で解析するには


## 14.4. ネットワークで多用するユーティリティ・クラス

### 14.4.1. MessageDigest

`MessageDigest` クラスは、データのメッセージ・ダイジェスト (ハッシュ値) を算出するための API です。現在のシステムでは、パスワードを暗号化してデータベースなどに保存するのではなく、パスワードのメッセージ・ダイジェストを保存して、入力されたパスワードのメッセージ・ダイジェストと照合する方法が一般的に用いられています (パスワードの暗号化は情報量が大きくなるため)。これからアプリケーションを実装する上で、メッセージ・ダイジェストの使い方は是非抑えておきたいものです。

```java
MessageDigest md;
try {
    // SHA-256 の MessageDigest インスタンスを取得する
    // 一度取得すれば md.reset() を呼び出して何回でも使用できる
    md = MessageDigest.getInstance("SHA-256");
} catch (NoSuchAlgorithmException e) {
    // メッセージ・ダイジェストのアルゴリズムが存在しない
    ...
}

// データ (これのメッセージ・ダイジェストを取得したい)
byte[] src = ... ;

md.reset();  // Step 1 : md をリセットする
md.update(src);  // Step 2 : md にデータを設定する
byte[] digest = md.digest();  // Step 3 : md のメッセージ・ダイジェストを計算する
```

`MessageDigest.getInstance()` メソッドの引数には、メッセージ・ダイジェストのアルゴリズム名を指定します。API 仕様で必須とされているものは "MD5"、"SHA-1"、"SHA-256" の 3 種類ですが、実際には他に "MD2"、"SHA-384"、"SHA-512" が使用できる模様です。

>参考まで、MD2、MD5 および SHA-1 は脆弱性が指摘されており、セキュリティ関連製品での使用は非推奨となっています (製品によっては既に廃止されている場合もあります)。ただし、MD5 についてはファイルの破損有無をチェックする「チェックサム」としては引き続き使用されています。

### 14.4.2. Base64

`Base64` クラスは、電子メールの添付ファイル等で使用される BASE64 エンコーディングのエンコードとでコードを行うための API です。BASE64 エンコーディングは画像・音声・動画等のいわゆるバイナリデータをテキストデータとして送受信するための手段として開発されたものです。Web サーバーの BASIC 認証でも、パスワードをエンコード/デコードする手段として BASE64 を使用します。前節で取り上げたメッセージ・ダイジェストは実際にはバイナリデータとなるため、ネットワーク経由で送受信する場合には BASE64 でエンコードするケースが多く見られます。

サンプルコードで使い方を見てみましょう。まずはエンコードです。`Base64.getEncoder().encode()` メソッドを使用します。エンコード対象はバイナリデータとみなすため `byte` 配列とします。戻り値も `byte` 配列ですが、こちらの中身はテキストデータです。そのため、戻り値を文字列で返す `Base64.getEncoder().encodeToString()` メソッドも用意されています。

```java
// エンコード対象のデータ
byte[] src = ... ;

// src を BASE64 でエンコードする
byte[] encoded = Base64.getEncoder().encode(src);

// src を BASE64 でエンコードし、結果を文字列として出力する
String encodedString = Base64.getEncoder().encodeToString(src);
```

次にデコードを見てみましょう。デコード対象は BASE64 エンコーディングされたテキストデータですが、引数は `byte` 配列とします (元が文字列であれば `String.getBytes()` メソッドで `byte` 配列に変換しましょう)。デコードには `Base64.getDecoder().decode()` メソッドを使用します。デコード結果はバイナリデータとみなすため、戻り値は `byte` 配列となります。

```java
// デコード対象のデータ = BASE64 エンコード済
// new String(encoded, Charset.forName("ISO-8859-1")) で文字列に変換可能
byte[] encoded = ... ;

// src を BASE64 でデコードする
byte[] decoded = Base64.getDecoder().decode(encoded);
```

では、あるデータのメッセージ・ダイジェストを取得して、BASE64 でエンコードするサンプルコードを見てみましょう。簡潔にするため、例外処理は省略します。

```java
MessageDigest md = MessageDigest.getInstance("SHA-256");

// メッセージ・ダイジェストの算出
byte[] src = ... ;
md.reset();
md.update(src);
byte[] digest = md.digest();

// BASE64 エンコーディング
byte[] encoded = Base64.getEncoder().encode(digest);
```

## 14.5. Logger

Java の標準 API にはログ出力 API が含まれています。Java の黎明期にはログ出力ライブラリとして "Apache Log4J" が普及しており、現在でも標準ではないが高機能な "Logback" や "JBoss LogManager" などが支持されています。Java 標準のログ出力 API (パッケージ名から `java.util.logging` やその略称の "JUL" でも呼ばれます) は "Logback" や "JBoss LogManager" に比べると機能は限定されますが、必要最低限の機能は有しており、標準で使用できるというメリットがあります。

### 14.5.1. Logger の基礎知識


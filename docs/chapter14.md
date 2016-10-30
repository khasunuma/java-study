# 14. 標準 API を活用しよう

## 14.1. Properties

[`Properties`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/Properties.html) クラスは、Java VM やアプリケーションの設定情報を保持するために使用するクラスです。JDK 1.0 の頃から存在する最古参の API ですが、数度の改訂を重ねながら (現在開発中の Java SE 9 においても改訂の予定があります) 継続して使用されています。

### 14.1.1. Properties クラスの基本構造

`Properties` クラスは、各設定情報 (エントリ) についてキー (名称) とプロパティ (設定値) がペアとなっており、その集合体となっています。コレクションの `Map` とよく似ています (実際に `Map` インタフェースも実装しています) が、実際にはコレクションの登場によりあまり使用されなくなった `Hashtable`(http://docs.oracle.com/javase/jp/8/docs/api/java/util/Hashtable.html) のサブクラスとして実装されています。`Hashtable` に対して、Java VM やアプリケーションの設定情報をファイルから読み込んだり書き出したりする (= 設定情報をファイルに退避させる) ための入出力機能と、若干のユーティリティ・メソッドを追加したものが `Properties` クラスとなります。

以下に `Properties` メソッドが用意しているユーティリティ・メソッドを示します。ただし、`Properties` は `Hashtable` のサブクラス (および `Map` の実装) であるため、これらが持つ汎用的なメソッドを使用することも可能です。

|メソッド名|引数|戻り値|説明|
|--------|--------|--------|--------|
|`getProperty`|`String`|`String`|指定されたキーのプロパティを取得する (ない場合は `null`)|
|`getProperty`|`String`, `String`|`String`|指定されたキーのプロパティを取得する (ない場合は第 2 引数で指定した値)|
|`setProperty`|`String, String`|`Object`|指定されたキーとプロパティを設定する (存在する場合は置き換える)|
|`stringPropertyNames`|N/A|`Set<String>`|すべてのキーを含む `Set` を取得する|
|`list`|`PrintStream`|N/A|指定された `PrintStream` (`System.out` 等) にプロパティの一覧を出力する|
|`list`|`PrintWriter`|N/A|指定された `PrintWriter` にプロパティの一覧を出力する|

### 14.1.2. システム・プロパティ

システム・プロパティは、Java VM が保持する各種設定情報です。システム・プロパティもまた、`Properties` クラスのインスタンスです。システム・プロパティは `System` クラス (`getProperties()` メソッド) から取得することが可能です。システム・プロパティ自体は読み書き可能となっていますが、Java VM の重要な設定情報も含まれているため、取り扱い (特にプロパティの変更) には注意をしてください。

システム・プロパティはおよそ以下の 4 種類に分類されます。

- Java VM の動作環境によって決められているもの (プラットフォーム間の移植性向上の目的で使用、アプリケーションで変更してはいけない)。
- Java VM の起動オプションによって決まってくるもの (いわゆるシステム情報、アプリケーションで変更してはいけない)。
- Java VM の起動時にユーザーによって設定されたもの (`java` の `-D` オプションで設定可能、アプリケーションで変更してもよい)。
- 実行時にアプリケーションによって追加されたもの (`System.getProperty()` メソッドで設定)。

`System` クラスにはシステム・プロパティを操作するためのユーティリティ・メソッドが用意されています。

|メソッド名|引数|戻り値|説明|
|--------|--------|--------|--------|
|`getProperties`|N/A|`Properties`|システム・プロパティ全体を表す `Properties` のインスタンスを取得する|
|`getProperty`|`String`|`String`|`getProperties().getProperty(String)` の省略形|
|`getProperty`|`String`, `String`|`String`|`getProperties().getProperty(String, String)` の省略形|
|`setProperty`|`String, String`|`Object`|`getProperties().setProperty(String, String)` の省略形|

### 14.1.3. Properties クラスとプロパティ・ファイル

`Properties` が保持するプロパティは、ファイルから読み取む、あるいは書き込むことができます。プロパティの入出力の対象となるファイルをプロパティ・ファイルと呼びます。プロパティ・ファイルには、行指向形式と XML 形式の 2 種類のフォーマットから選択することが可能です (一般には単純な行指向形式を使用します)。

>プロパティの入出力には `InputStream`/`OutputStream` または `Reader`/`Writer` を使用するため、入出力の対象は必ずしもファイルである必要はありませんが、最も多く使用されるのがファイルであるため、ここではプロパティ・ファイルとしました。

|メソッド名|引数|戻り値|説明|
|--------|--------|--------|--------|
|`load`|`InputStream`|N/A|プロパティ一覧 (行指向形式、ISO-8859-1) を `InputStream` から読み取る|
|`load`|`Reader`|N/A|プロパティ一覧 (行指向形式、UTF-8) を `Reader` から読み取る|
|`loadFromXML`|`InputStream`|プロパティ一覧 (XML 形式、UTF-8) を `InputStream` から読み取る|
|`store`|`OutputStream, String`|プロパティ一覧 (行指向形式、ISO-8859-1) を `OutputStream` へ書き込む、第 2 引数はコメント|
|`store`|`Writer, String`|プロパティ一覧 (行指向形式、UTF-8) を `Writer` へ書き込む、第 2 引数はコメント|
|`storeToXML`|`OutputStream, String`|プロパティ一覧 (XML 形式、UTF-8) を `OutputStream` へ書き込む、第 2 引数はコメント|

#### (1) 行指向形式

- 行指向形式のプロパティ・ファイルの各行には、自然行 (物理行) と論理行があります。
- 自然行は、改行文字または終端で終わる 1 行の文字列として定義されます。
- 論理行は、キーと値のペアを保持し、キー=値 (区切り文字 '=' (イコール)) または キー:値 (区切り文字 ':' (コロン)) で表されます。論理行は複数の自然行からなる場合もあり、自然行の末尾に '\' (バックスラッシュ) を置くことで次の自然行にまたがることを示します。
- 空白文字 (' ' (スペース)、'\t' (タブ)、'\f' (フォームフィード) および改行) のみを含む自然行は、空白と見なされて無視されます。
- 先頭が '#' (シャープ) または '!' (エクスクラメーション) の自然行は、コメント行と見なされて無視されます。ただし、コメント行の目印である '#' と '!' は自然行のみに対して作用するため、複数の自然行からなる論理行をコメント行とするには、論理行を構成するすべての自然行の先頭に '#' または '!' を挿入する必要があります。
- キーの中で '#'、'!' または空白文字自体を使用する場合には、これらの先頭に '\' を付加してエスケープします。
- 値の中で '#'、'!'、'=' または ':' を使用する場合には、これらの先頭に '\' を付加してエスケープします。
- キーまたは値の中で '\' 自体を使用する場合には、'\\' で表してエスケープします。

行指向形式にはさらに ISO-8859-1 (Latin-1) エンコードと UTF-8 エンコードがあり、ISO-8859-1 の場合は非 ASCII 文字を直接記述することができないという制約があります。その場合は非 ASCII 文字を \u*nnnn* のように Unicode エスケープ形式の文字列に置き換える必要があります。これを行うものが [2 章](chapter02.md) で紹介した `native2ascii` ユーティリティです。ただし、`native2ascii` と同等の機能は統合開発環境に備わっていることが多く、また UTF-8 エンコードを選択した場合は直接 Unicode 文字を記述できるため処理自体が不要となります。

以下に行指向形式のプロパティ・ファイルの例を示します。

```
# store メソッドの第 2 引数で指定したコメント
# Wed Oct 26 10:30:45 JST 2016
key1=value-no1
key2=value \
     -no2
key3=value\=no3
#key4=value-no4
key5=\u30d7\u30ed\u30d1\u30c6\u30a3
```

`store` メソッドが出力するプロパティ・ファイルの場合、1 行目に `store` の第 2 引数で指定したコメント、2 行目に現在時刻 ('dow mon dd hh:mm:ss zzz yyyy' 形式) を挿入します。これらはいずれもコメント行であるため、`load` メソッドでは無視されます。

#### (2) XML 形式

XML 形式は以下の DTD 定義に従います。ファイルは基本的に UTF-8 エンコードとなります。XML 形式は現在ではあまり使用されていません。

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

上記のプロパティ・ファイルを XML 形式で書き直すと以下のようになります。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<properties version="1.0">
  <comment>store メソッドの第 2 引数で指定したコメント</comment>
  <entry key="key1">value-no1</entry>
  <entry key="key2">value-no2</entry>
  <entry key="key3">value=no3</entry>
  <!-- entry key="key4">value-no4</entry -->
  <entry key="key5">プロパティ</entry>
</properties>
```

## 14.2. JAXB

XML ドキュメントと Java のオブジェクト (クラスのインスタンス) の相互変換を実現するための API が JAXB です。Java には XML を扱う API が複数存在しますが、JAXB はその中でも異色の存在で、プログラマが XML パーサーの存在を意識することなく使用できるメリットがあります。変換速度が比較的高速である点も見逃せません (JAXB は内部で高速 XML パーサーである StAX を呼び出します)。JAXB に関連したクラスは `javax.xml.bind` パッケージに収録されています。

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

上記の通り、マーシャリングには [`Marshaller`](http://docs.oracle.com/javase/jp/8/docs/api/javax/xml/bind/Marshaller.html) クラスの `marshal` メソッドで行います (`Marshaller` のインスタンスは [`JAXBContext`](http://docs.oracle.com/javase/jp/8/docs/api/javax/xml/bind/JAXBContext.html) のインスタンスから取得します)。`marshal` メソッドは第 2 引数 (出力先の指定) が異なるいくつかのオーバーラード・メソッドが用意されていますが、主に使用するのは下記の 3 つくらいでしょう。

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

上記の通り、アンマーシャリングには [`Unmarshaller`](http://docs.oracle.com/javase/jp/8/docs/api/javax/xml/bind/Unmarshaller.html) クラスの `unmarshal` メソッドで行います (`Unmarshaller` のインスタンスも `JAXBContext` のインスタンスから取得します)。`unmarshal` メソッドは引数 (入力元の指定) が異なるいくつかのオーバーラード・メソッドが用意されていますが、主に使用するのは下記の 4 つくらいでしょう。なお、戻り値は `Object` クラスになっているため、それぞれのクラスにキャストする必要があります。

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

大きく複雑なデータを扱う場合には、これらはそれほどのオーバーヘッドにはなりませんが、小さなデータを数多く扱う場合にはオーバーヘッドを無視できなくなります。こうした問題を解決するため、JAXB では [`JAXB`](http://docs.oracle.com/javase/jp/8/docs/api/javax/xml/bind/JAXB.html) ユーティリティ・クラスを用意しています。`JAXB` クラスは `static` メソッドとして `marshal`/`unmarshal` メソッドが用意されており、メソッド呼び出しだけでマーシャリングとアンマーシャリングを実現できます。これらのメソッドはエラー時にランタイム例外をスローするため、別途例外処理のコードも不要となります。

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

Java の標準 API には正規表現ライブラリが付属しています。正規表現ライブラリは `java.util.regex` パッケージに収録され、正規表現そのものを表す [`Pattern`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/regex/Pattern.html) クラスと、正規表現にマッチするかを判定する [`Matcher`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/regex/Matcher.html) クラスで構成されます (`Matcher` クラスはマッチの判定だけでなく、マッチ箇所を他の文字列に置換する機能も持ちます)。

### 14.3.1. 簡単なパターンマッチング

正規表現によるパターンマッチングは、以下の手順で行います。

1. 正規表現を表す `Pattern` のインスタンスを生成する。
2. `Pattern` とマッチさせる文字列から `Matcher` のインスタンスを生成する。
3. `Matcher.matches()` メソッドで判定する。

```java
// A-Z で始まり、1 文字以上の a-z が続く正規表現 (不変) 
Pattern pattern = Pattern.compile("[A-Z][a-z]+");

// 正規表現を文字列 "Abc" とマッチさせる
Matcher matcher = pattern.matcher("Abc");

// matcher.matches() = 正規表現にマッチした場合 true
System.out.println(matcher.matches());

// matcher を新たに文字列 "4bc" とマッチングする (Matcher のインスタンスは再利用可能)
matcher.reset("4bc");

// matcher.matches() = 正規表現にマッチしない場合 false
System.out.println(matcher.matches());
```

### 14.3.2. 正規表現を用いた文字列置換

正規表現による文字列置換は、以下の手順で行います。

1. 正規表現を表す `Pattern` のインスタンスを生成する。
2. `Pattern` とマッチさせる文字列から `Matcher` のインスタンスを生成する。
3. `Matcher.replaceFirst()` または `Matcher.replaceAll()` メソッドでマッチした部分を置換する。

```java
// XXX を表す正規表現 (不変)
Pattern pattern = Pattern.compile("XXX");

// 正規表現を文字列 abcXXXdefXXXghiXXXjkl にマッチさせる
Matcher matcher = pattern.matcher("abcXXXdefXXXghiXXXjkl");

// 正規表現と最初にマッチした部分を "-" で置き換える → abc-defXXXghiXXXjkl
System.out.println(matcher.replaceFirst("abcXXXdefXXXghiXXXjkl"), "-")

// 正規表現とマッチしたすべての部分を "-" で置き換える → abc-def-ghi-jkl
System.out.println(matcher.replaceAll("abcXXXdefXXXghiXXXjkl", "-"))
```

### 14.3.3. String クラスのメソッドとの対応

`String` クラスにも正規表現マッチおよび置換を行うメソッドが用意されています。呼び出しのたびに内部で `Pattern` や `Matcher` のインスタンスを生成するため効率的ではありませんが、簡便な方法として使用できます。以下に `String` のインスタンス `s` に対する正規表現マッチおよび置換を行うメソッドと、`Pattern` および `Matcher` による書き換えの対応表を示します。

|`String` のメソッド|`Pattern` および `Matcher` による書き換え|
|------------------------|----------------------------------------|
|`s.matches(String regex)`|`Pattern.compile(regex).matcher(s).matches()`|
|`s.replaceFirst(String regex, String replacement)`|`Pattern(regex).matcher(s).replaceFirst(replacement)`|
|`s.replaceAll(String regex, String replacement)`|`Pattern(regex).matcher(s).replaceAll(replacement)`|

### 14.3.4. 正規表現の学習

ここでは正規表現そのものについては触れませんでしたが、参考書籍として以下を推薦します。

- Jeffrey E.F. Friedl : [詳説 正規表現 第3版](https://www.oreilly.co.jp/books/9784873113593/)、オライリー・ジャパン、2008 年、ISBN978-4-87311-359-3

この書籍は正規表現の入門から発展的な話題までを網羅した 1 冊です。Java を含む様々なプログラミング言語における正規表現を取り扱っており、数多くの有益なサンプルコードが掲載されています。その中には CSV ファイル (" " で囲む改行・特殊文字エスケープ処理を含む) を解析する正規表現パターンも含まれます。

## 14.4. ネットワークで多用するユーティリティ・クラス

### 14.4.1. MessageDigest

[`MessageDigest`](http://docs.oracle.com/javase/jp/8/docs/api/java/security/MessageDigest.html) クラスは、データのメッセージ・ダイジェスト (ハッシュ値) を算出するための API です。現在のシステムでは、パスワードを暗号化してデータベースなどに保存するのではなく、パスワードのメッセージ・ダイジェストを保存して、入力されたパスワードのメッセージ・ダイジェストと照合する方法が一般的に用いられています (パスワードの暗号化は情報量が大きくなるため)。これからアプリケーションを実装する上で、メッセージ・ダイジェストの使い方は是非抑えておきたいものです。

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

[`Base64`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/Base64.html) クラスは、電子メールの添付ファイル等で使用される BASE64 エンコーディングのエンコードとでコードを行うための API です。BASE64 エンコーディングは画像・音声・動画等のいわゆるバイナリデータをテキストデータとして送受信するための手段として開発されたものです。Web サーバーの BASIC 認証でも、パスワードをエンコード/デコードする手段として BASE64 を使用します。前節で取り上げたメッセージ・ダイジェストは実際にはバイナリデータとなるため、ネットワーク経由で送受信する場合には BASE64 でエンコードするケースが多く見られます。

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

## 14.5. ログ・サービス

Java の標準 API にはログ・サービスが含まれており、`java.util.logging` パッケージに収録されています。Java の黎明期にはログ出力ライブラリとして "Apache Log4J" が普及しており、現在でも標準ではないが高機能な "Logback" や "JBoss LogManager" などが支持されています。Java 標準のログ・サービス (パッケージ名の "java.util.logging" やその略称 "JUL" でも呼ばれます) は "Logback" や "JBoss LogManager" に比べると機能は限定されますが、必要最低限の機能は有しており、標準で使用できるというメリットがあります。

### 14.5.1. レベル

レベルは [`Level`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/logging/Level.html) クラスで表され、出力するログの重み付けを行います。レベルは整数値として表現され、あるレベルのログ出力を有効にすると、それよりも高いレベルのログ出力がすべて有効になります。`Level` クラスは `SEVERE`、`WARNING`、`INFO`、`CONFIG`、`FINE`、`FINER`、`FINEST` の 7 種類をレベル定数として事前定義しています。その他に特殊なレベル定数として、すべてを出力する `ALL` (レベル `Integer.MIN_VALUE`) と、ログを出力しない `OFF` (Integer.MAX_VALUE`) も定義されています。

レベル定数の詳細について以下にまとめます。

|レベル定数|レベル|説明|想定される用途|
|------|---:|------|----------------|
|`SEVERE`|1000|重大な障害を示す|致命的な状況やエラーについての情報。問題が発生しており、処理が続行不能な状況。|
|`WARNING`|900|潜在的な問題を示す|警告についての情報。問題が発生しているものの、処理は続行可能な状況。|
|`INFO`|800|メッセージを情報として提供する|正常系の情報。特に重要なポイントを通過した。|
|`CONFIG`|700|静的な構成メッセージ|設定情報に関する情報。|
|`FINE`|500|トレース情報|デバッグ情報。比較的重要だが運用時にロギングする必要のない情報。|
|`FINER`|400|かなり詳細なトレース情報|特定の処理についての開始および終了の情報。内部的に発生した例外に関する情報。|
|`FINEST`|300|非常に詳細なトレース情報|トレース情報。|

### 14.5.2. ロガー

ロガーは [`Logger`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/logging/Logger.html) クラスで表される、ログを記録するためのオブジェクトです。`Logger` のインスタンスはアプリケーション全体で共有の名前を付け、必要に応じて複数作成することが可能です。ログの記録はロガー単位で行います。すなわち、ロガー A に記録したログはロガー B には記録されません。

>高度な使い方のため割愛しますが、ロガーは名前の付け方によりグルーピングすることが可能で、グルーピングしたロガーに対しては同時にログを出力することができます。

`Logger` のインスタンスは、以下に示す static メソッド (ファクトリ・メソッド) で取得または作成します。

|メソッド名|引数|戻り値|説明|
|--------|--------|--------|--------|
|`getLogger`|`String`|`Logger`|指定された名前のロガーを取得する (なければ作成する)|
|`getGlobal`|N/A|`Logger`|`Logger.GLOBAL_LOGGER_NAME` という名前のロガー (グローバル・ロガー) を取得する|
|`getAnonymousLogger`|N/A|`Logger`|匿名ロガーを作成する|

ログの記録は、以下に示す `Logger` の各メソッドで行います。ここに挙げたものは基本的かつ代表的なもので、ほとんどの用途に適しています。この他にクラス名・メソッド名・例外を出力できるメソッド、詳細な形式で出力できるメソッド、ラムダ式と組み合わせて使用できるメソッドなどが備わっています。

|メソッド名|引数|説明|
|--------|--------|--------|
|`log`|`Level, String`|指定したレベルのログを記録する|
|`severe`|`String msg`|`SEVERE` レベルのログを記録する|
|`warning`|`String msg`|`WARNING` レベルのログを記録する|
|`info`|`String msg`|`INFO` レベルのログを記録する|
|`config`|`String msg`|`CONFIG` レベルのログを記録する|
|`fine`|`String msg`|`FINE` レベルのログを記録する|
|`finer`|`String msg`|`FINER` レベルのログを記録する|
|`finest`|`String msg`|`FINEST` レベルのログを記録する|

### 14.5.3. フォーマッタ

フォーマッタはログの出力形式を規定するクラスで、[`Formatter`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/logging/Formatter.html) クラスのサブクラスとなります。標準では簡単な形式 (1～2 行程度) で出力する [`SimpleFormatter`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/logging/SimpleFormatter.html) と XML 形式で出力する [`XMLFormatter`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/logging/XMLFormatter.html) が用意されています。

多くのアプリケーションでは `XMLFormatter` は使用せず、`SimpleFormatter` または独自に実装したフォーマッタを利用する傾向にあります。独自フォーマッタの例としては、[GlassFish Server](http://www.glassfish.org/)/[Payara Server](http://www.payara.fish/) が使用する [`com.sun.enterprise.server.logging.UniformLogFormatter`](https://github.com/payara/Payara/blob/master/nucleus/core/logging/src/main/java/com/sun/enterprise/server/logging/UniformLogFormatter.java) (Sun 形式) や [`com.sun.enterprise.server.logging.ODLLogFormatter`](https://github.com/payara/Payara/blob/master/nucleus/core/logging/src/main/java/com/sun/enterprise/server/logging/ODLLogFormatter.java) (Oracle Fusion Middleware 形式) 等があります。

### 14.5.4. ハンドラ

ログ出力先を指定するオブジェクトをハンドラと呼び、[`Handler`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/logging/Handler.html) クラスのサブクラスとなります。標準では以下の 5 種類が用意されていますが、主に使用するものは `ConsoleHandler` と `FileHandler` です。

|ハンドラ|ログ出力先|レベル|フォーマッタ|
|[`MemoryHandler`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/logging/MemoryHandler.html)|メモリ (特殊用途)|`Level.ALL`|N/A|
|[`StreamHandler`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/logging/StreamHandler.html)|`OutputStream` (汎用)|`Level.INFO`|`SimpleFormatter`|
|[`ConsoleHandler`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/logging/ConsoleHandler.html)|標準エラー出力|`Level.INFO`|`SimpleFormatter`|
|[`FileHandler`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/logging/FileHandler.html)|ファイル|`Level.ALL`|`XMLFormatter`|
|[`SocketHandler`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/logging/SocketHandler.html)|ネットワーク|`Level.ALL`|`XMLFormatter`|

標準のハンドラにはログ出力先の他、出力するログのレベルと使用するフォーマッタがあらかじめ決められています。これらはログ・システム固有のプロパティによって指定されている値であり、変更にはログ・マネージャを使用する必要があります。

### 15.5.5. ログ・サービスの管理

[`LogManager`](http://docs.oracle.com/javase/jp/8/docs/api/java/util/logging/LogManager.html) クラスは、ログ・サービス全体を管理するクラスで、アプリケーションの初期化時に唯一のインスタンスが作成されます。`LogManager` のインスタンスは `LogManager.getLogManager()` メソッドで取得します。

`LogManager` には様々なメソッドが用意されていますが、ハンドラの設定等を保持するプロパティを変更するには `readConfiguration(InputStream)` メソッドを使用します。このメソッドの引数は `InputStream` クラスとなっていますが、想定される書式は `Properties` ファイルで用いる行指向形式のプロパティ・ファイルとなっています。

以下に `ConsoleHandler` および `FileHandler` の動作を制御するプロパティを示します。`readConfiguration` メソッドで読み込むプロパティ・ファイルは以下を置き換えるための記述を行います。

|ハンドラ|キー|値の意味|値の既定値|
|--------|--------|--------|---------|
|(共通)|`handlers`|使用するハンドラ・クラスの完全名 (複数あるときはスペースで区切る)|`java.util.logging.ConsoleHandler`|
|(共通)|`.level`|出力レベル (すべてに優先する)|`Level.INFO`|
|`ConsoleHandler`|`java.util.logging.ConsoleHandler.level`|出力レベル|`Level.INFO`|
|`ConsoleHandler`|`java.util.logging.ConsoleHandler.filter`|`Filter` クラス名|N/A|
|`ConsoleHandler`|`java.util.logging.ConsoleHandler.formatter`|`Formatter` クラス名|`java.util.logging.SimpleFormatter`|
|`ConsoleHandler`|`java.util.logging.ConsoleHandler.encoding`|文字エンコード|プラットフォーム既定値|
|`FileHandler`|`java.util.logging.FileHandler.level`|出力レベル|`Level.ALL`|
|`FileHandler`|`java.util.logging.FileHandler.filter`|`Filter` クラス名|N/A|
|`FileHandler`|`java.util.logging.FileHandler.formatter`|`Formatter` クラス名|`java.util.logging.XMLFormatter`|
|`FileHandler`|`java.util.logging.FileHandler.encoding`|文字エンコード|プラットフォーム既定値|
|`FileHandler`|`java.util.logging.FileHandler.limit`|1 ファイルに書き込む最大量 (バイト)、`0` は無制限|`50000`|
|`FileHandler`|`java.util.logging.FileHandler.count`|循環する出力ファイル数|`1`|
|`FileHandler`|`java.util.logging.FileHandler.pattern`|出力ファイル名のパターン|%h/java%u.log|
|`FileHandler`|`java.util.logging.FileHandler.append`|`true`: 既存ファイルに追記、`false`: 既存ファイルを上書き)|`false`|

#### 15.5.5.1. クラス・パス上のプロパティ・ファイルからの読み取り

`ConsoleHandler` は既定では `INFO` レベル以上のログしか出力しないように設定されています。ここでは、`ConsoleHandler` ですべてのレベルのログを出力するように設定する手順を示します。変更するプロパティは、以下の 2 つです。

- `.level`
- `java.util.logging.ConsoleHandler.level`

これらの値を `ALL` に設定するようなプロパティ・ファイルを作成して、プロパティ・ファイルをオープンして作成した `InputStream` を `LogManager.readConfiguration()` メソッドで読み込めば良いわけです。ここではクラス・パス上 (つまりクラス・ファイルと同じ場所) に存在するプロパティ・ファイルを `Class.getResourceAsStream()` メソッドでオープンする例を示します。設定ファイル類はクラス・パス上に置くことも多いため、知っておくと便利な方法です。

まず、クラス・パス上に logging.properties という内容で以下のプロパティ・ファイルを作成しておきます。

```
# logging.properties
handlers=java.util.logging.ConsoleHandler
.level=ALL
java.util.logging.ConsoleHandler.level=ALL
java.util.logging.ConsoleHandler.formatter=java.util.logging.SimpleFormatter
```

次に、以下のようなコードで logging.properties をオープンして `InputStream` を作成し、`LogManager.readConfiguration()` メソッドに渡します。`static { }` というブロックは static ブロックと呼ばれる特殊なブロックで、`static` フィールド初期化と同じタイミングで内部のコードが実行されます (つまりコンストラクタよりも早い段階で実行されます)。

```java
public class LoggingSample {
    // プロパティ・ファイルの読み取り
    static {
        try (InputStream in = LoggingSample.class.getResourceAsStream("logging.properties")) {
            LogManager.getLogManager().readConfiguration(in);
        } catch (IOException e) {
            // プロパティ・ファイルの読み取りに失敗した
            ...
        }
    }
    
    // 以下省略
    ...
}
```

#### 15.5.5.2. メモリからのプロパティ・ファイルの読み取り

先に示したプロパティ・ファイルは非常に小さいため、ファイルとして作成せずに内容をフィールド (変数) として用意する方法も考えられます。以下のコードはプロパティ・ファイルを文字列のフィールドとして作成して、`byte` 配列に変換した上で `ByteArrayInputStream` で `InputStream` を作成します。実際のアプリケーションではファイルとして用意することが多いと思われますが、よりシンプルな別解として紹介しておきます。

```java
public class LoggingSample {
    // プロパティ・ファイルの内容
    private static final String LOGGING_PROPERTIES
        = "handlers=java.util.logging.ConsoleHandler\n"
        + ".level=ALL\n"
        + "java.util.logging.ConsoleHandler.level=ALL\n"
        + "java.util.logging.ConsoleHandler.formatter=java.util.logging.SimpleFormatter";

    // プロパティ・ファイルの読み取り
    static {
        try (InputStream in = new ByteArrayInputStream(LOGGING_PROPERTIES.getBytes())) {
            LogManager.getLogManager().readConfiguration(in);
        } catch (IOException e) {
            // プロパティ・ファイルの読み取りに失敗した
            ...
        }
    }
    
    // 以下省略
    ...
}
```

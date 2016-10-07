# 2. Java プログラミング環境

この章では、Java アプリケーションの開発に必要な JDK (Java Development Kit) とその他の開発ツールの使い方についてみてゆきます。Java のプログラミングに最低限必要なものは、JDK とソースファイルを作成するためのテキストエディタの 2 つだけですが、実際の Java アプリケーションは多数のソースファイルから構成されるため、ソースファイルをまとめてコンパイルするためのビルドツールの助けを借りるのが現実的な方法です。

最近では開発に必要なエディタ、コンパイラ、ビルドツール、デバッガなどが一体になった「統合開発環境 (IDE)」が普及し、統合開発環境だけでソースファイルの作成からアプリケーションの実行までのすべてを行うことができるようになりました。統合開発環境を使用すると JDK の存在を意識することなくスムーズに開発を行うことができる反面、その裏側で JDK やビルドツールが何をしているのかが見えなくなってしまいます。そこでこの章では、簡単なサンプルを通じて JDK とビルドツールの使い方を説明した上で、主要な統合開発環境について紹介してゆきます。

## 2.1. JRE と JDK

Java は実行環境である JRE (Java Runtime Environment) と開発環境である JDK (Java Development Kit) に分かれており、それぞれ無償で配布されています。JDK は JRE を含んでいますが、これはすべての Java 実行環境にコンパイラなどの開発ツールが必要とは限らないという考え方によります。標準的なものは Oracle から配布されている Oracle JRE と Oracle JDK ですが、IBM など他ベンダーからも JDK が提供されています。現在流通している JDK はそのほとんどがオープンソース・プロジェクトである OpenJDK をベースに作られています。OpenJDK 自体はソースコードのみでの配布となりますが、Red Hat より OpenJDK のインストーラ (Windows 版および Linux 版) が配布されています。

- Oracle JRE/JDK - http://www.oracle.com/technetwork/java/javase/downloads/index.html
- IBM Java - http://www.ibm.com/developerworks/java/jdk/java8/
- OpenJDK (Red Hat) - http://developers.redhat.com/products/openjdk/overview/

Oracle JRE/JDK と IBM Java はプロプライエタリ・ライセンスでの配布となっており、再配布や改変の禁止などの条件が課せられています。OpenJDK (Red Hat 版インストーラを含む) はクラスパス例外付き GPLv2 というオープンソース・ライセンスで配布されているため再配布・改変などに制限がありません。

>GPLv2 には再配布時に改変した箇所を含むすべてのソースコードを GPLv2 で公開しなければならないという条件があります (ソースコードの添付が望ましいが、別途ダウンロードやソースコードのありかを示すだけでも良いとされます)。「クラスパス例外付き」とは、JRE/JDK 上で動作するアプリケーションについては GPLv2 の条件は適用されない (ソースコードの公開は不要でライセンスも GPLv2 にする必要がない) ことを意味します。

以下、Oracle JRE/JDK を例にとり、それらに含まれる主なツールについて紹介します。他ベンダーの JDK ではツールの構成に一部相違があります。

### 2.1.1. JRE (Java Runtime Environment)

JRE は Java アプリケーションの実行に必要な最小限のツールを備えています。JRE に含まれるツールとしては `java`、`jjs`、`keytool` などがあります。

- [`java`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/java.html) : Java アプリケーションを起動します。一般に Java VM と呼ばれているものはこのツールを指します。JRE のツールでは最も使用頻度が高く、必ず押さえておきたいツールです。なお、Windows 版 JRE にはコマンドプロンプトを起動することなく実行するための `javaw.exe` が含まれています。
- [`jjs`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/jjs.html) : Nashorn エンジンを起動します。Nashorn とは Java VM 上で動作する JavaScript の実行環境です。Java アプリケーションでは Nashorn エンジン経由で JavaScript を実行可能 (その逆も可) ですが、`jjs` ツールで Nashorn 単体を起動することができます。
- [`keytool`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/keytool.html) : 暗号化鍵、X.509 証明書チェーン、および信頼できる証明書を含むキーストア (データベース) を管理します。SSL/TLS 通信のセットアップで使用することがあるかもしれません。

### 2.1.2. JDK (Java Development Kit)

JDK は Java アプリケーションの開発に必要なツールキットで、JRE が備えるすべてのツールに加え、`javac`、`javadoc`、`native2ascii`、`jar`、`javapackager` などが含まれます。

- [`javac`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/javac.html) : Java クラスとインタフェースの定義を読み取り、それらをバイト・コードとクラス・ファイルにコンパイルします。いわゆる「Java コンパイラ」です。言うまでもなく JDK の中核をなすツールです。
- [`javadoc`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/javadoc.html) : Javaソース・ファイルから、API ドキュメントの HTML ページを生成します。Java のソースコードにはドキュメンテーション・コメント (Javadoc コメント) と呼ばれる特殊なコメントを記述することが可能で、それをもとに API ドキュメントを生成するのが `javadoc` ツールです。Maven などのビルドツールや統合開発環境は `javadoc` ツールを自動で実行する機能を持っています。
- [`native2ascii`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/native2ascii.html) : サポートされている任意の文字エンコーディングの文字を含むファイルを、ASCII または Unicode (あるいはその両方) のエスケープ文字を含むファイルに変換すること、またはその逆を行うことで、ローカライズ可能なアプリケーションを作成します。`native2ascii` ツールはアプリケーションの設定などを記述する「プロパティファイル」で日本語などを使用するために用意されていますが、統合開発環境で同等の処理を肩代わりできること、さらにはプロパティファイルの UTF-8 エンコーディング対応により `native2ascii` ツールを介することなく直接日本語などを記述できるようになってきていることから、あまり使いどころはないかもしれません。
- [`jar`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/jar.html) : Java Archive (JAR) ファイルを操作します。コンパイル後に生成されたクラスファイルなどをメタ情報とともに ZIP アーカイブにする (またはその逆) ために使用します。直接使用する機会はあまりありませんが、Maven などのビルドツールや統合開発環境からは頻繁に `jar` ツールを起動するため、使用頻度自体は高いツールです。
- [`javapackager`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/javapackager.html) : Java アプリケーションのパッケージ化と署名に関連するタスクを実行します。Windows、Mac または Linux の実行可能パッケージ (Windows の場合は .exe) を生成し、インストーラまで作成してくれる優れものです。パッケージには JRE (の内部実装) が含まれるため、別途 JRE/JDK を用意する必要がなくなるメリットもあります。JavaFX で開発した GUI アプリケーションを配布する際によく使われます。
- [`jcmd`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/jcmd.html) : 実行中の Java VM に診断コマンド要求を送信します。実行中の Java VM の一覧表示、Java VM の状態や各種診断情報、Java Flight Recorder による Java VM の状態監視などを行います。JDK には古くから様々な監視・診断ツールが含まれていましたが、`jcmd` ツールはそれらを統合したような位置づけです。Java Flight Recorder は Oracle の有償サポート契約の下で使用可能となり、管理ツールである Java Mission Control と連携して動作します。
- [`jvisualvm`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/jvisualvm.html) : Java アプリケーションのモニタリング、トラブルシューティングおよびプロファイリングを視覚的に行います。JDK の中では数少ない GUI のツールであり実行負荷は大きいですが、その効果も絶大です。Oracle の有償サポート契約がある場合にはさらに高機能な Java Mission Control が使用可能となります。

## 2.2. Java アプリケーション開発の手順

ここからは、JDK と Maven を用いた Java アプリケーションの開発手順について説明します。はじめに JDK のみで開発する方法を示し、続いて JDK と Maven による開発を示します。この節では、開発/実行環境が Windows であるものと仮定し、ユーザーのホームディレクトリ (フォルダ) を %USERPROFILE% と表記します。

### 2.2.1. プロジェクトの作成

まず、%USERPROFILE% 以下に作業ディレクトリを用意します。ここでは `workspace` という名前のディレクトリにします (これは Eclipse の「ワークスペース」のデフォルトと同じ配置です)。

次にプロジェクトを作成します。プロジェクトとは、アプリケーションのソースファイル、設定ファイル、出力されたクラスファイルなどのリソースをまとめて配置するディレクトリで、ここでは `workspace` 以下の `hello` という名前のディレクトリにします (これは Eclipse や Maven の「プロジェクト」と同じ配置、同じ意味合いです)。

プロジェクト内ではソース・ファイルとクラス・ファイルは別のディレクトリに配置すると便利です (Java コンパイラのデフォルトでは、ソース・ファイルと同じディレクトリにクラス・ファイルを出力します)。ここではソースファイルを配置するソース・ディレクトリを `hello` 以下の `src\main\java`、クラス・ファイルを配置する出力ディレクトリを `hello` 以下の `target\classes` とします (これは Maven のデフォルトのレイアウトに合わせています)。

以下をまとめると次のようになります。

- 作業ディレクトリ - `%USERPROFILE%\workspace`
- プロジェクト - `%USERPROFILE%\workspace\hello`
- ソースディレクトリ - `%USERPROFILE%\workspace\src\main\java`
- 出力ディレクトリ - `%USERPROFILE%\workspace\target\classes`

最初に作業ディレクトリを準備し、続けてプロジェクト、ソースディレクトリを作成します。

```
CD %USERPROFILE%
MKDIR workspace

CD workspace
MKDIR hello

CD hello
MKDIR src\main\java
```

ここまでの段階で、ディレクトリ・ツリーは以下の通りとなっているはずです。

```
%USERPROFILE%
  +--workspace
       +--hello
            +--src
                 +--main
                      +--java
```

### 2.2.2. JDK によるビルド

#### 2.2.2.1. ソースファイルの作成

ソースディレクトリ以下に `app` という名前のディレクトリを作成し、そこに以下に示すソースファイルを `Hello.java` という名前で保存します。ソースファイルは UTF-8 エンコーディングで保存します。

```java
package app;

public class Hello {
    public static void main(String... args) {
        System.out.println("Hello, world");
    }
}
```

ソースファイル作成後のディレクトリ・ツリーは以下のようになります。

```
%USERPROFILE%
  +--workspace
       +--hello
            +--src
                 +--main
                      +--java
                           +--app
                                +--Hello.java
```

#### 2.2.2.2. コンパイル

`javac` ツールでソース・ファイルをコンパイルします。

```
CD %USERPROFILE%\workspace\hello
MKDIR target\classes
javac -cp . -d target\classes -encoding UTF-8 src\main\java\app\Hello.java
```

`javac` ツールの書式は以下の通りです。

```
javac [オプション] <ソース・ファイル>
```

`javac`ツール のオプションには以下のようなものがあります。

- `-cp` *classpath* - クラス・パスを指定します。複数存在する場合は、Windows では `;` (セミコロン)、Linux では `:` (コロン) で区切ります。省略した場合は `.` (カレント・ディレクトリ) のみになります。この例では省略時と同じ指定にしています。
- `-d` *outputDir* - 出力ディレクトリを指定します。省略した場合はソース・ファイルと同じディレクトリになります。この例では `target\classes` に出力するように指定しています。
- `-encoding` *encoding* - ソース・ファイルのエンコーディングをしています。省略時は環境に依存します。現在ではソース・ファイルのエンコーディングに UTF-8 を使用するのが一般的であり、この例でも `UTF-8` を指定しています。
- `-source` *release* - ソース・ファイルの Java バージョンを指定します。この値をもとにソース・ファイルの構文チェックが行われます。省略時は `1.8` (Java SE 8) となります。なお、`1.5` またはそれ以前のバージョンを指定すると警告が出ます。
- `-target` *version* - 出力するクラス・ファイルが対象とする Java バージョンを指定します。以前のバージョン向けのクラス・ファイルを出力する際に使用されます。省略時は `1.8` (Java SE 8) となります。なお、`1.5` またはそれ以前のバージョンを指定すると警告が出ます。

コンパイル完了後のディレクトリ・ツリーは以下のようになります。

```
%USERPROFILE%
  +--workspace
       +--hello
            +--src
            |    +--main
            |         +--java
            |              +--app
            |                   +--Hello.java
            +--target
                 +--classes
                      +--app
                           +--Hello.class
```

#### 2.2.2.3. 実行 (クラスファイル)

作成したアプリケーションを `java` ツールで実行するには、`-cp` オプションでクラス・パスに出力ディレクトリを設定し、main メソッドがあるクラスを指定します。

```
CD %USERPROFILE%\hello\target
java -cp classes app.Hello
```

`java` ツールの -cp オプションを省略するとカレント・ディレクトリがクラス・パスとみなされるため、上記の実行コマンドは以下のように置き換えることもできます。

```
CD %USERPROFILE%\hello\target
CD classes
java app.Hello
```

#### 2.2.2.4. JAR ファイルの作成

通常、Java アプリケーションでは多数のクラス・ファイルが出力されるため、JAR ファイルにまとめることが多いです。

JAR ファイルを作成するには、以下のように `jar` ツールを使用します。 

```
CD %USERPROFILE%\hello\target
jar cf hello.jar -C classes
```

一般に、JAR ファイルを作成する場合の `jar` ツールの書式は以下の通りです。

```
jar cf <JAR ファイル> -C <出力フォルダ>
```

JAR ファイル作成後のディレクトリ・ツリーは以下のようになります。

```
%USERPROFILE%
  +--workspace
       +--hello
            +--src
            |    +--main
            |         +--java
            |              +--app
            |                   +--Hello.java
            +--target
                 +--classes
                 |    +--app
                 |         +--Hello.class
                 +--hello.jar
```

#### 2.2.2.5. 実行 (JAR ファイル)

JAR ファイルにアプリケーションのクラスが存在する場合は、`java` ツールの `-cp` オプションでクラス・パスに JAR ファイルを設定し、main メソッドが存在するクラスを指定してアプリケーションを実行します。

```
CD %USERPROFILE%\hello\target
java -cp hello.jar app.Hello
```

#### 2.2.2.6. 実行可能 JAR ファイルの作成

JAR ファイルは、メタ情報に main メソッドが存在するクラスを設定することで、直接 JAR ファイルを指定して実行することができます。そのような JAR ファイルを実行可能 JAR ファイルといいます。

実行可能 JAR ファイルを作成するには、まずメタ情報を記述したファイルを用意する必要があります。以下のようなファイルを作成して、`manifest.mf` と名前を付けて保存します。これをマニフェスト・ファイルと呼びます。

```
Main-Class: app.Hello
```

次に、`jar` ツールでマニフェスト・ファイルを指定して JAR ファイルを作成します。

```
CD %USERPROFILE%\hello\target
jar cfm hello.jar manifest.mf -C classes
```

マニフェスト・ファイルを指定して JAR ファイルを作成する場合の `jar` ツールの書式は以下の通りです。

```
jar cfm <JAR ファイル> <マニフェスト・ファイル> -C <出力フォルダ>
```

実行可能 JAR ファイル作成後のディレクトリ・ツリーは以下のようになります。

```
%USERPROFILE%
  +--workspace
       +--hello
            +--src
            |    +--main
            |         +--java
            |              +--app
            |                   +--Hello.java
            +--target
                 +--classes
                 |    +--app
                 |         +--Hello.class
                 +--manifest.mf
                 +--hello.jar
```

#### 2.2.2.7. 実行 (実行可能 JAR)

実行可能 JAR ファイルは、以下のように `java` ツールの `-jar` オプションを用いて実行することができます。

```
CD %USERPROFILE%\hello\target
java -jar hello.jar
```

もちろん、通常の JAR ファイルと同じように実行することも可能です。

### 2.2.3. Maven によるビルド

#### 2.2.3.1. pom.xml の作成

このプロジェクトは Maven でそのまま使用できるディレクトリ構成になっています。上記と同じ手順てソースファイル (`Hello.java`) を作成し、さらに Maven プロジェクトの構成ファイルである `pom.xml` をプロジェクト `hello` 以下に作成します。

ここで作成する `pom.xml` は以下の通りとなります。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>app</groupId>
  <artifactId>hello</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>
</project>
```

ルート要素 `project` とその属性 (`xmlns`、`xmlns:xsi` および `xsi:schemaLocation`) は固定ですので、必ずこの通りに記述します。それ以外はプログラマが指定します。少なくとも以下の要素については必須です。

|要素名|設定値|
|--------|----------------|
|`modelVersion`|常に `4.0.0` を設定する|
|`groupId`|原則としてパッケージ名と一致させる (この例では `app`)|
|`artifactId`|原則としてプロジェクト名と一致させる (この例では `hello`)|
|`packaging`|Java SE のアプリケーションの場合は `jar` とする|
|`version`|バージョン番号 (この例では `1.0-SNAPSHOT`)|

`properties` 要素には、プロジェクトの各種設定値を指定します。ここでは以下の 3 種類の値を設定しています。

|プロパティ名|値|
|--------|--------|
|`project.build.sourceEncoding`|ソース・ファイルのエンコーディングを指定する (この例では `UTF-8`)|
|`maven.compiler.source`|`javac` の `-source` オプションに渡す値、デフォルトは `1.5` (この例では `1.8`)|
|`maven.compiler.target`|`javac` の `-target` オプションに渡す値、デフォルトは `1.5` (この例では `1.8`)|

`properties` 要素は本来任意の項目ですが、Maven のデフォルトが実態とかけ離れているため (`javac` の `-source`/`-target` オプションに非推奨の値 `1.5` を渡す、など)、ここで設定した 3 種類については常に設定する方が良いでしょう。

`pom.xml` 作成後のディレクトリ・ツリーは以下の通りとなります。

```
%USERPROFILE%
  +--workspace
       +--hello
            +--src
            |    +--main
            |         +--java
            |              +--app
            |                   +--Hello.java
            +--pom.xml
```

------------------------------------------------------------------------

実は、Maven にはプロジェクトを作成する機能も備わっています。以下のコマンドを実行すると `pom.xml` を含むプロジェクト `hello` が作業ディレクトリ以下に作成されます。

```
CD %USERPROFILE%\workspace
mvn archetype:generate -DgroupId=app -DartifactId=hello -DinteractiveMode=false
```

作成されたプロジェクトのディレクトリ・ツリーを以下に示します。`pom.xml` だけでなく、ソース・ディレクトリ、さらにサンプルの `App.java` とその単体テストである `AppTest.java` も出力されていることがわかります。

```
%USERPROFILE%
  +--workspace
       +--hello
            +--src
            |    +--main
            |    |    +--java
            |    |         +--app
            |    |              +--App.java
            |    +--test
            |         +--java
            |              +--app
            |                   +--AppTest.java
            +--pom.xml
```

Maven が出力した `pom.xml` の内容は以下の通りとなります。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>app</groupId>
  <artifactId>hello</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>hello</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

Maven 本来の使い方としてはこちらが正しいのですが、生成されたファイルを見る限り、適切ではない点がいくつか見受けられます。

- サンプル `App.java` と単体テスト `AppTest.java` はおそらく使われないものなので、出力されるだけ無駄になる。
- `pom.xml` の要素に過不足がある。`name` や `url` は不要である一方、エンコーディングや Java のバージョンを指定する項目がなく、ソースファイルが正しくコンパイルされない可能性がある。
- `dependencies` 要素以下に JUnit の組み込み指定がなされているものの、バージョンが現行の 4.x ではなく、時代遅れとなった 3.8.1 になっている。

Maven によって作成されるプロジェクトは、基にするテンプレート (Archetype といいます) によって決定されるため、優れたテンプレートから作成されたプロジェクトを利用すれば開発生産性は大きく向上します。今回のようにテンプレートを指定しない場合には Java SE 向けのもっとも簡単なサンプルのテンプレートが使用されるのですが、上記のように適切でない点がいくつか存在するため、そのまま使用するのは難しいでしょう。

今回は以上を考慮してプロジェクトと `pom.xml` を手動で作成しました。なお、Maven プロジェクトは統合開発環境でも作成することが可能で、実際にはそちらの手段の方が多く用いられます。

#### 2.2.3.2. コンパイル

Maven でプロジェクトのコンパイルを行うには、以下のように `mvn compile` コマンドを使用します。`mvn compile` はソース・ディレクトリ以下のすべてのソース・ファイルをコンパイル対象とします。

```
CD %USERPROFILE%\workspace\hello
mvn compile
```

コンパイル完了後のディレクトリ・ツリーは以下のようになります。

```
%USERPROFILE%
  +--workspace
       +--hello
            +--src
            |    +--main
            |         +--java
            |              +--app
            |                   +--Hello.java
            +--target
            |    +--classes
            |         +--app
            |              +--Hello.class
            +--pom.xml
```

#### 2.2.3.3. 実行 (クラスファイル)

作成したアプリケーションを `java` ツールで実行するには、`-cp` オプションでクラス・パスに出力ディレクトリを設定し、main メソッドがあるクラスを指定します。

```
CD %USERPROFILE%\hello\target
java -cp classes app.Hello
```

`java` ツールの -cp オプションを省略するとカレント・ディレクトリがクラス・パスとみなされるため、上記の実行コマンドは以下のように置き換えることもできます。

```
CD %USERPROFILE%\hello\target
CD classes
java app.Hello
```

#### 2.2.3.4. JAR ファイルの作成

Maven で JAR ファイルを作成するには、`mvn package` コマンドを使用します。コンパイルが実行されていないか、もしくはソースファイルが変更されて再コンパイルが必要な場合には、自動的に `mvn compile` コマンドが実行されます。

```
CD %USERPROFILE%\workspace\hello
mvn package
```

JAR ファイル作成後のディレクトリ・ツリーは以下のようになります。JAR ファイル名が artifactId + version となっている点に注目してください。Maven ではこのような命名規則を用いることで JAR ファイルのバージョン管理を行う仕組みになっています。

```
%USERPROFILE%
  +--workspace
       +--hello
            +--src
            |    +--main
            |         +--java
            |              +--app
            |                   +--Hello.java
            +--target
            |    +--classes
            |    |    +--app
            |    |         +--Hello.class
            |    +--hello-1.0-SNAPSHOT.jar
            +--pom.xml
```

#### 2.2.3.5. 実行 (JAR ファイル)

JAR ファイルに格納したアプリケーションを `java` ツールで実行するには、`-cp` オプションでクラス・パスに JAR ファイルを設定し、main メソッドがあるクラスを指定します。

```
CD %USERPROFILE%\hello\target
java -cp hello-1.0-SNAPSHOT.jar app.Hello
```

#### 2.2.3.6. 実行可能 JAR ファイルの作成

Maven で実行可能 JAR ファイルを作成するには、`pom.xml` に必要事項の追記が必要になります。記述量は多いですが、ほぼ定型句のため使い回しが効きます。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>app</groupId>
  <artifactId>hello</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>
  <!-- ここから追加 -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
        <configuration>
          <archive>
            <manifest>
              <mainClass>app.Hello</mainClass><!-- main メソッドが存在するクラス -->
            </manifest>
          </archive>
        </configuration>
      </plugin>
    </plugins>
  </build>
  <!-- ここまで追加 -->
</project>
```

`pom.xml` の記述が異なることを除いて、ビルド手順は通常の JAR ファイルの作成と全く同じです。

#### 2.2.3.7. 実行 (実行可能 JAR)

実行可能 JAR ファイルは、以下のように `java` ツールの `-jar` オプションを用いて実行することができます。

```
CD %USERPROFILE%\hello\target
java -jar hello-1.0-SNAPSHOT.jar
```

もちろん、通常の JAR ファイルと同じように実行することも可能です。

## 2.3. 統合開発環境 (IDE)

統合開発環境 (IDE) は、開発に必要なエディタ、コンパイラ、ビルド・ツール、デバッガなどが一体になったツールです。統合開発環境は以下のような特徴を持っており、Java アプリケーション開発には欠かせないツールとなっています。

- エディタがプログラミング向けに最適化されている。
  - カット/コピー & ペースト、正規表現に対応した検索・置換、選択範囲指定、カーソルのジャンプ、などの基本機能は一通り備わっている。
  - 構文ハイライト表示が行われるため、ソースコードが読みやすい。
  - 構文チェック機能が備わっており、文法エラーをリアルタイムに通知することができる。
  - 途中まで入力されたキーワードを補完したり、候補となるものを一覧から選択できる。
- 逐次コンパイルがサポートされ、ソース・ファイルを保存されると即座にクラス・ファイルの出力が行われる。
  - コンパイル・エラーが発生した場合はすぐにエディタに通知し、エラー個所などを知らせることができる。
  - 特別なビルドを行う必要がなければ、すぐにアプリケーションを実行することが可能である。
- Ant、Maven などのビルド・ツールが組み込まれている。
  - ビルド・ツールの初期化 (プロジェクトの作成など) を統合開発環境のウィザード画面から行うことができる。
  - エディタからビルド・ツールを起動することができる。
  - エディタでビルド・ツールの設定ファイルを編集可能で、構文チェックも行われる。
- 高機能なデバッガを持ち、プログラムに特別なデバッグ・コードを埋め込まなくても、リアルタイムでデバッグが可能である。
- バージョン管理システム (Git、Subversion) との連携がサポートされており、エディタからバージョン管理システムの操作 (チェックアウト、コミット、差分比較など) を行うことができる。

現在主に利用されている Java の統合開発環境には、Eclipse、NetBeans、IntelliJ IDEA があります。

- [Eclipse](http://www.eclipse.org/) は IBM 製の統合開発環境をオープンソース化したもので、現在は Eclipse Foundation 配下で開発が行われています。モジュール化が徹底されており、ほとんどの機能がプラグインとして実現されているのが大きな特徴です。現在では開発対象ごとに公式プラグインを整理した形で配布が行われています。GUI の一部にプラットフォーム・ネイティブのコードを含み、登場当初は他の Pure Java の統合開発環境と比較して非常に軽快な動作でも注目を浴びました。
- [NetBeans](https://ja.netbeans.org/) は Apache Foundation 配下で開発されているオープンソースの統合開発環境で、かつて Sun や Oracle が開発主体であったことから、実質的に Java のオフィシャルな統合開発環境として認知されています。Pure Java のツールで、操作性に癖もなく、プラットフォームを問わず多くのユーザーに利用されています。標準で日本語化されているのも大きな特徴です。Oracle SQL Developer の最新版は NetBeans がベースとなっています。
- [IntelliJ IDEA](https://www.jetbrains.com/idea/) は JetBrains が販売している統合開発環境です。機能限定版はオープンソース化されています。先進的で多機能な統合開発環境であり、大規模アプリケーションのビルドも問題なくこなすヘビーデューティーなツールでもあります。一癖あると言われる操作性も使い込むほど手に馴染み、有償でありながら多くのユーザーが Eclipse から移行しているようです。Pepper の開発環境として追加される Android Studio は IntelliJ IDEA がベースとなっています。

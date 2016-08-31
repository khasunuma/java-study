# 2. Java プログラミング環境

## 2.1. JDK と JRE

### 2.1.1. JRE (Java Runtime Environment)

- [`java`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/java.html)
- [`jjs`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/jjs.html)
- [`keytool`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/keytool.html)

### 2.1.2. JDK (Java Development Kit)

- [`javac`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/javac.html)
- [`javadoc`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/javadoc.html)
- [`native2ascii`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/native2ascii.html)
- [`jar`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/jar.html)
- [`javapackager`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/javapackager.html)
- [`jcmd`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/jcmd.html)
- [`jvisualvm`](https://docs.oracle.com/javase/jp/8/docs/technotes/tools/windows/jvisualvm.html)

## 2.2. Java アプリケーション開発の手順

- 作業ディレクトリ %USERPROFILE%\workspace
- プロジェクト - %USERPROFILE%\workspace\hello
- ソースディレクトリ - %USERPROFILE%\workspace\src\main\java
- 出力ディレクトリ - %USERPROFILE%\workspace\target\classes

### 2.2.1. プロジェクトの作成

```
CD %USERPROFILE%\workspace
MKDIR hello
```

### 2.2.2. JDK によるビルド

#### 2.2.2.1. ソースディレクトリの作成

```
CD hello
MKDIR src\main\java
```

#### 2.2.2.2. ソースファイルの作成

```java
package app;

public class Hello {
    public static void main(String... args) {
        System.out.println("Hello, world");
    }
}
```

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

#### 2.2.2.3. コンパイル

```
CD %USERPROFILE%\hello
MKDIR target\classes
javac -cp . -d target\classes -encoding UTF-8 src\main\java\app\Hello.java
```

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

#### 2.2.2.4. 実行 (クラスファイル)

```
CD %USERPROFILE%\hello\target
CD classes
java app.Hello
```

```
CD %USERPROFILE%\hello\target
java -cp classes app.Hello
```

#### 2.2.2.5. JAR ファイルの作成

```
CD %USERPROFILE%\hello\target
jar cf hello.jar -C classes
```

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

#### 2.2.2.6. 実行 (JAR ファイル内のクラス)

```
CD %USERPROFILE%\hello\target
java -classpath hello.jar app.Hello
```

#### 2.2.2.7. 実行可能 JAR ファイルの作成

```
Main-Class: app.Hello
```

```
CD %USERPROFILE%\hello\target
jar cfm hello.jar manifest.mf -C classes
```

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

#### 2.2.2.8. 実行 (実行可能 JAR)

```
CD %USERPROFILE%\hello\target
java -jar hello.jar
```

### 2.2.3. Maven によるビルド

#### 2.2.3.1. pom.xml の作成

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
    <java.version>1.8</java.version>
  </properties>
</project>
```

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

#### 2.2.3.2. コンパイル

```
CD %USERPROFILE%\workspace\hello
mvn compile
```

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

```
CD %USERPROFILE%\hello\target
CD classes
java app.Hello
```

#### 2.2.3.4. JAR ファイルの作成

```
CD %USERPROFILE%\workspace\hello
mvn package
```

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

#### 2.2.3.5. 実行 (JAR ファイル内のクラス)

```
CD %USERPROFILE%\hello\target
java -classpath hello-1.0-SNAPSHOT.jar app.Hello
```

#### 2.2.3.6. 実行可能 JAR ファイルの作成

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
    <java.version>1.8</java.version>
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
              <mainClass>app.Hello</mainClass>
            </manifest>
          </archive>
        </configuration>
      </plugin>
    </plugins>
  </build>
  <!-- ここまで追加 -->
</project>
```
        
#### 2.2.3.7. 実行 (実行可能 JAR)

```
CD %USERPROFILE%\hello\target
java -jar hello-1.0-SNAPSHOT.jar
```

## 2.3. 統合開発環境 (IDE)

統合開発環境 (IDE) は、開発に必要なエディタ、コンパイラ、デバッガなどが一体になったツールです。

現在主に利用されている Java の統合開発環境には、Eclipse、NetBeans、IntelliJ IDEA があります。

* [Eclipse](http://www.eclipse.org/) は IBM 製の統合開発環境をオープンソース化したものです。モジュール化が徹底されており、ほとんどの機能がプラグインとして実現されているのが大きな特徴です。現在では開発対象ごとに公式プラグインを整理した形で配布が行われています。GUI の一部にプラットフォーム・ネイティブのコードを含み、登場当初は他の Pure Java の統合開発環境と比較して非常に軽快な動作でも注目を浴びました (現在は Java 自体の高速化によりそのメリットはなくなっていると言われます)。
* [NetBeans](https://ja.netbeans.org/) は現在 Oracle から配布されているオープンソースの統合開発環境で、実質的に Java のオフィシャルな統合開発環境として認知されています。Pure Java のツールで、操作性に癖もなく、プラットフォームを問わず多くのユーザーに利用されています。標準で日本語化されているのも大きな特徴です。Oracle SQL Developer の最新版は NetBeans がベースとなっています。
* [IntelliJ IDEA](https://www.jetbrains.com/idea/) は JetBrains が販売している統合開発環境です。機能限定版はオープンソース化されています。先進的で多機能な統合開発環境であり、大規模アプリケーションのビルドも問題なくこなすヘビーデューティーなツールでもあります。一癖あると言われる操作性も使い込むほど手に馴染み、有償でありながら多くのユーザーが Eclipse から移行しているようです。Android Studio はこの IntelliJ IDEA がベースとなっています。

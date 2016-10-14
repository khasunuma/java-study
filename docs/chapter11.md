# 11. ファイル操作の基本

## 11.1. Java のファイル操作

Java のファイル操作は、入出力の単位 (バイト単位 or 文字単位) と入力/出力の種類によって大きく 4 つのクラスに分かれます。

|入出力単位|I/O|クラス|
|------|----|--------|
|バイト|入力|`InputStream`|
|バイト|出力|`OutputStream`|
|文字  |入力|`Reader`|
|文字  |出力|`Writer`|

4 つのクラスはいずれも抽象クラスであり、入出力の対象 (ファイル、メモリ等) に応じてサブクラスが用意されています。これらのクラス群を「(入出力) ストリーム」といい、バイト単位で処理するものを「バイト・ストリーム」、文字単位で処理するものを「文字ストリーム」と呼んで区別しています。

>Java のファイル操作にはストリームの他に「チャネル」と呼ばれる手段も用意されています。チャネルを用いた方がはるかに高速ですが、操作が煩雑で汎用性にも欠けるため、このドキュメントでは説明を割愛します。

Java ではファイル操作を簡略化するために "NIO.2" と呼ばれる API が用意されています (Java SE 7 以降)。この章ではまず入出力ストリームの概要について紹介し、続いて NIO.2 を用いたファイル操作について説明します (NIO.2 を用いないファイル操作については取り上げません)。

### 11.1.1. バイト・ストリーム

バイト・ストリームの中心となるのが、入力用の `InputStream` クラスと出力用の `OutputStream` クラスです。これらのクラスには入出力の対象に依存しない共通のメソッドが用意されています。`InputStream`、`OutputStream` とも、データのやり取りの対象となるのは原則として `byte` 型の配列です (これがバイト・ストリームと呼ばれる所以です)。

ファイル入出力におけるこれらの具象クラスは `FileInputStream` クラスと `FileOutputStream` クラスになります。これらは主にバイナリファイルの入出力を行うために用意されています。主なメソッドを以下に示しますが、読み取り用メソッドと書き込み用メソッドが対になっていることに注目してください。

|操作                     |`InputStream`           |`OutputStream`          |
|-------------------------|------------------------|------------------------|
|ファイルのオープン         |`new FileInputStream()`|`new FileOutputStream()`|
|1 バイトの読み取り/書き込み|`read()`               |`write(int)`|
|配列の要素数だけ読み取り/書き込み|`read(byte[], int, int)`|`write(byte[], int, int)`|
|指定した長さだけ読み取り/書き込み|`read(byte[], int, int)`|`write(byte[], int, int)`|
|ファイルのフラッシュ       | N/A                    |`flush()`               |
|ファイルのクローズ         |`close()`              |`close()`                |

バイト・ストリームを使用する際には、以下の点に注意してください。

- コンストラクタおよびメソッドは `IOException` (またはそのサブクラス) をスローする可能性があります。したがって、例外処理が必須となります。
- 処理の最後に必ずファイルのクローズが必要です。原則として try-catch 文の finally 句でクローズします。

### 11.1.2. 文字ストリーム

文字ストリームの中心となるのが、入力用の `Reader` クラスと出力用の `Writer` クラスです。これらのクラスには入出力の対象に依存しない共通のメソッドが用意されています。`Reader`、`Writer` とも、データのやり取りの対象になるのは原則として `char` 型の配列です (これが文字ストリームと呼ばれる所以です)。

ファイル入出力におけるこれらの具象クラスは `FileReader` クラスと `FileWriter` クラスになります。これらは主にテキストファイルの入出力を行うために用意されています。主なメソッドを以下に示しますが、読み取り用メソッドと書き込み用メソッドが対になっていることに注目してください。

|操作                     |`Reader`                |`Writer`|
|-------------------------|------------------------|------------------------|
|ファイルのオープン        |`new FileReader()`       |`new FileWriter()`|
|1 文字の読み取り/書き込み  |`read()`                |`write(int)`|
|配列の要素数だけ読み取り/書き込み|`read(char[], int, int)`|`write(char[], int, int)`|
|指定した長さだけ読み取り/書き込み|`read(char[], int, int)`|`write(char[], int, int)`|
|文字列の読み取り/書き込み  |N/A                     |`write(String)`|
|ファイルのフラッシュ       |N/A                    |`flush()`|
|ファイルのクローズ        |`close( )`              |`close()`|

文字ストリームを使用する際には、以下の点に注意してください。

- コンストラクタおよびメソッドは `IOException` (またはそのサブクラス) をスローする可能性があります。したがって、例外処理が必須となります。
- 処理の最後に必ずファイルのクローズが必要です。原則として try-catch 文の finally 句でクローズします。

### 11.1.3. 入出力ストリームの変換

バイト・ストリームは文字ストリームに変換することができます。`InputStream` クラスはインスタンスを `InputStreamReader` クラス (`Reader` のサブクラス) のコンストラクタ引数として、`OutputStream` クラスはインスタンスを `OutputStreamWriter` クラス (`Writer` のサブクラス) のコンストラクタ引数としてそれぞれ渡すことで、文字ストリーム (`Reader` および `Writer`) として扱うことができるようになります。`InputStreamWriter` と `OutputStreamWriter` は文字コード変換を伴うテキストファイルの入出力を行うために用意されています。

`InputStreamWriter`/`OutputStreamWriter` をクローズすると、それらに渡されていた `InputStream`/`OutputStream` も自動的にクローズされます。

>NIO.2 を用いると、文字コードを指定してファイルをオープンできるようになるため、`InputStreamReader` および `OutputStreamWriter` は実質的に不要となります。

### 11.1.4. 入出力ストリームのバッファリング

一般に、入出力ストリームは `read`/`write` のメソッドが呼ばれる度に入出力を行います。そのため、入出力対象がメモリ等の高速なものでない限り、大きな負荷が掛かりやすくなります。そこで、こうした処理の効率化のためにバッファリングを行う入出力ストリームが用意されています。ファイル入出力ではバッファリングを行った方が良いでしょう。

|バッファリングを行う入出力ストリーム|対応する入出力ストリーム|
|`BufferedInputStream`            |`InputStream`|
|`BufferedOutputStream`           |`OutputStream`|
|`BufferedReader`                 |`Reader`|
|`BufferedWriter`                 |`Writer`|

バッファリングを行う入出力ストリームは、コンストラクタ引数に対応する入出力ストリームのインスタンスを渡すことで生成します。`BufferedReader` および `BufferedWriter` ではさらに行単位での読み取り/書き込みを行うためのメソッドが追加されます。バッファリングを行う入出力ストリームをクローズすると、対応する入出力ストリームも自動的にクローズされます。

|操作                    |`BufferedReader`|`BufferedWriter`|
|-----------------------|----------------|----------------|
|バッファの作成          |`new BufferedReader()`|`new BufferedWriter()`|
|1 文字の読み取り/書き込み|`read()`        |`write(int c)`|
|配列の要素数だけ読み取り/書き込み|`read(char[], int, int)`|`write(char[], int, int)`|
|指定した長さだけ読み取り/書き込み|`read(char[], int, int)`|`write(char[], int, int)`|
|1 行読み取り (*)        |`readLine()`    | N/A |
|改行の書き込み (*)      | N/A             |`newLine()`|
|文字列の書き込み (*)     | N/A            |`write(String)`|
|バッファのフラッシュ     | N/A            |`flush()`|
|バッファのクローズ      |`close()`        |`close()`|

(*) 行単位での入出力を行うためのメソッド。改行コードは OS によって異なるため、その差違を吸収するようなメソッドが用意されている。

>NIO.2 では常にバッファリングを行う入出力ストリームを用いてテキストファイルを扱います。

## 11.2. NIO.2

### 11.2.1. NIO.2

Java は最初のバージョンから入出力ストリームをはじめとする豊富な入出力 API を備えていました。それらは現在でも十分に通用する優秀な API ですが、Java が普及するとともに入出力 API に対する改善要望が多数寄せられるようになりました。それらの要望を吸収し入出力 API の近代化を図ったものが NIO (New I/O) および NIO.2 (New I/O 2) です。ファイル操作に関連する改善は NIO.2 によって行われています。この章ではファイル操作に NIO.2 を利用する前提で話を進めます。

NIO プロジェクトは Java の I/O そのものを刷新する大規模なプロジェクトであり、すべてを同時リリースすることができませんでした。そこでまず `InputStream`/`OutputStream` の高速化 (チャネルの導入) を中心とした NIO を J2SE 1.4 でリリースし、その後ファイル操作の簡略化を中心とした NIO.2 を Java SE 7 でリリースする形を採りました。

### 11.2.2. FileSystem クラスと Path インタフェース

NIO.2 によるファイル操作は、操作対象を示すための `FileSystem` クラスと `Path` インタフェース、実際の操作を提供するユーティリティである `Files` クラスが中心となります。ここでは `FileSystem` クラスと `Path` インタフェースについて取り上げます。

`FileSystem` クラスはファイルシステムそのものを表します。既定のファイルシステムは Java VM が動作する OS のファイルシステムですが、NIO.2 ではそれ以外のファイルシステムもサポートできるように設計されています。例えば、Java SE 7 (またはそれ以降) では試験的にですが ZIP アーカイブをファイルシステムとして扱うことができます。

`FileSystem` のインスタンスは `FileSystems` クラスから取得することができます。`FileSystems` クラスでは様々なファイルシステム (先に挙げた ZIP アーカイブを含む) に対応した `FileSystem` のインスタンスを取得できますが、既定のファイルシステムに対応した `FileSystem` のインスタンスを取得するには `FileSystems.getDefault()` メソッドを用います。

`Path` インタフェースはファイルのパスを表します。既定のファイルシステムでは、OS 上のファイルやディレクトリなどを表します。`Path` のインスタンスは `FileSystem.getPath()` メソッドで取得できます。`getPath` メソッドの引数にはファイルまたはディレクトリのパスを指定します。

```java
FileSystem fileSystem = FileSystems.getDefault();

// デリミタを含む fileSystem.getPath("C:\\eclipse\\eclipse.ini") でも OK
Path path = fileSystem.getPath("C", "eclipse", "eclipse.ini");
```

>`FileSystem` クラスには `close()` メソッドが存在しており、**既定ではない**ファイルシステムを操作する場合には必ずクローズしなければなりません。ただし、既定のファイルシステムではクローズの必要がなく、`close()` メソッドを呼び出しても `UnsupportedOperationException` 例外がスローされてしまいます。

なお、上記の操作は `Paths` クラスを用いて以下のように書き換えることができます。`Paths` クラスを用いた方が簡潔に記述できるため、通常はこちらの方法を用います。

```java
// デリミタを含む Paths.get("C:\\eclipse\\eclipse.ini") でも OK
Path path = Paths.get("C", "eclipse", "eclipse.ini");
```

`Path` のインスタンスで表されるファイルまたはディレクトリのパスは、`FileSystem.getPath()` や `Paths.get()` メソッドの引数に相対パスを指定すると相対パスになります。相対パスで表される `Path` を絶対パスに変換する場合には `Path.toAbsolutePath()` メソッドを使用します。

その他、`Path` インタフェースにはパス操作を行うためのメソッドが用意されています。主なものを以下に示します。

|メソッド名       |説明|
|----------------|--------------------------------|
|`getFileName`   |ファイルまたはディレクトリ名そのもの (ディレクトリ構造を含まない) を取得する|
|`getParent`     |親ディレクトリのパスを取得する (親を持たない場合は `null`)|
|`resolve`       |指定されたパスをこのパスに対して解決する (例: パスの子要素を取得する)|
|`resolveSibling`|指定されたパスをこのパスの親パスに対して解決する (例: ファイル名を変更する)|
|`toAbsolutePath`|絶対パスを取得する|
|`toString`      |文字列表現を取得する (書式は OS に依存する)|

### 11.2.3. Files クラス

`Files` クラスはファイル操作およびファイル・システム操作を取りまとめたユーティリティ・クラスです。NIO.2 によるファイル操作の大半は、この `Files` クラスが起点となります。

`Files` クラスが提供するメソッドのうちファイル・システム操作に関するものには、以下のようなものが用意されています。具体的なメソッド名については API ドキュメントを参照してください。

- ファイルのコピー (※NIO.2 以前、実はファイルのコピー機能も提供されていなかった)
- ファイルの移動 (リネーム)
- ディレクトリの作成
- 空ファイルの作成
- ハードリンク/シンボリックリンクの作成
- 一時ディレクトリの作成
- 空の一時ファイルの作成
- ファイル (ディレクトリ) の削除
- ファイル (ディレクトリ) の存在チェック
- ファイル (ディレクトリ) のサイズ・属性・更新日時・所有者等の取得
- ディレクトリ・ツリーのスキャン

`Files` クラスが提供するメソッドのうち、ファイル操作に関するものには、以下のようなものが用意されています (チャネルが関連するもの等は省略します)。参考までに、具体的なメソッド名を [ ] 内に示します。詳細については API ドキュメントを参照してください。

|メソッド名          |説明|
|-------------------|----------------|
|`newBufferedReader`|ファイルを読み取り用に開き BufferedReader を返す|
|`newBufferedWriter`|ファイルを書き込み用に開き BufferedWriter を返す|
|`newInputStream`   |ファイルを読み取り用に開き InputStream を返す|
|`newOutputStream`  |ファイルを書き込み用に開き OutputStream を返す|
|`readAllBytes`     |ファイルからすべてのバイトを読み取り byte[] で返す|
|`readAllLines`     |ファイルからすべての行を読み取り List<String> で返す|
|`lines`            |ファイルからすべての行を読み取り Stream<String> で返す (Java SE 8 以降)|
|`write`            |すべてのバイトまたは行をファイルに書き込む (一部の書式は Java SE 8 以降)|

※`readAllBytes()`、`readAllLines()`、`lines()` および `write()` は、完了後にファイルがクローズされる。

## 11.3. ファイル操作の実際

### 11.3.1. try-catch 文を用いたファイルの読み込み

テキストファイルを 1 行単位で読み込み、何らかの処理を行うメソッドの例を以下に示します。これが Java におけるファイル読み込みの基本形となりますが、現在では次節に示すように try-with-resources 文を使用したほうがよいでしょう。

```java
public void readFile(Path path) throws IOException {
    BufferedReader reader = Files.newBufferedReader(path, Charset.forName("UTF-8"));
    try {
        String line = reader.readLine();
        while (line != null) {
            // ここで何か処理を行う
            ...
            line = reader.readLine();
        }
    } catch (IOException e) {
        ...
        // IOException の再スロー、その他の処理がなければ catch 節ごと省略可能
        throw e;
    } finally {
        reader.close();
    }
}
```

### 11.3.2. try-with-resources 文を用いたファイルの読み込み

前節のサンプルを try-with-resources 文を用いて書き換えたものを以下に示します。ファイルのオープン・クローズに関わる記述がシンプルになっていることに注目してください。

```java
public void readFile(Path path) throws IOException {
    // リソース BufferedReader reader = Files.newBufferedReader(path) をオープンする
    // ここでオープンしたリソースは try-catch を抜ける際に自動的にクローズされる 
    try (BufferedReader reader = Files.newBufferdReader(path, Charset.forName("UTF-8"))) {
        String line = reader.readLine();
        while (line != null) {
            // ここで何か処理を行う
            ...
            line = reader.readLine();
        }
    } catch (IOException e) {
        ...
        // IOException の再スロー、その他の処理がなければ catch 節ごと省略可能
        throw e;
    } // close() が不要となるので、finally 節も省略できる
}
```

### 11.3.2. Files クラスの readAllLines メソッドと write メソッドの利用

`Files` クラスにはファイルのすべての行/バイトを読み取る `readAllLines`/`readAllBytes` メソッドと、すべての行/バイトをファイルに書き込む `write` メソッドが用意されています。単純にファイルの全内容を読み書きするのであれば、この方法が最も簡潔な表現となります。

```java
public void readFile(Path path) throws IOException {
    try {
        for (String line : Files.readAllLines(path, Charset.forName("UTF-8"))) {
            // ここで何か処理を行う
            ...
        }
    } catch (IOException e) {
        ...
        // IOException の再スロー、その他の処理がなければ catch 節ごと省略可能
        throw e;
    }
}
```

```java
public void writeFile(Path path, List<String> lines) throws IOException {
    try {
        Files.write(path, lines, Charset.forName("UTF-8"));
    } catch (IOException e) {
        ...
        // IOException の再スロー、その他の処理がなければ catch 節ごと省略可能
        throw e;
    }
}
```

`readAllLines`/`readAllBytes` および `write` メソッドは大きなファイルの読み書きを想定したものではありませんが、`List` または `byte` 配列を用いて一括でファイルの読み書きができるため、小さなファイルを数多く処理しなければならない場合に威力を発揮します。

【補足】 `Files` クラスのメソッドを用いてファイルの読み書きを行う際には `Charset` で文字エンコードをするようになっています。Windows 環境では Shift_JIS (Microsoft CP 932) : `Charset.forName("Windows-31J")` または UTF-8 : `Charset.forName("UTF-8")` を指定することが多いでしょう。文字エンコードの指定は Java SE 7 では必須ですが、Java SE 8 では省略可能です (省略時は UTF-8 とみなされます)。


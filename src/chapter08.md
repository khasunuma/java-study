# 8. ファイル操作の基本

## 8.1. Java のファイル操作

Java のファイル操作は、入出力の単位 (バイト単位 or 文字単位) と入力/出力の種類によって大きく 4 つのクラスに分かれます。

|入出力単位|入力/出力|クラス|
|----|----|----|
|バイト|入力|`InputStream`|
|バイト|出力|`OutputStream`|
|文字|入力|`Reader`|
|文字|出力|`Writer`|

4 つのクラスはいずれも抽象クラスであり、入出力の対象 (ファイル、メモリ等) に応じてサブクラスが用意されています。これらのクラス群を「(入出力) ストリーム」といい、バイト単位で処理するものを「バイト・ストリーム」、文字単位で処理するものを「文字ストリーム」と呼んで区別しています。

>Java のファイル操作にはストリームの他に「チャネル」と呼ばれる手段も用意されています。チャネルを用いた方がはるかに高速ですが、操作が煩雑で汎用性にも欠けるため、このドキュメントでは説明を割愛します。

Java ではファイル操作を簡略化するために "NIO.2" と呼ばれる API が用意されています (Java SE 7 以降)。この章ではまず入出力ストリームの概要について紹介し、続いて NIO.2 を用いたファイル操作について説明します (NIO.2 を用いないファイル操作については取り上げません)。

### 8.1.1. バイト・ストリーム

バイト・ストリームの中心となるのが、入力用の `InputStream` クラスと出力用の `OutputStream` クラスです。これらのクラスには入出力の対象に依存しない共通のメソッドが用意されています。`InputStream`、`OutputStream` とも、データのやり取りの対象となるのは原則として `byte` 型の配列です (これがバイト・ストリームと呼ばれる所以です)。

ファイル入出力におけるこれらの具象クラスは `FileInputStream` クラスと `FileOutputStream` クラスになります。これらは主にバイナリファイルの入出力を行うために用意されています。主なメソッドを以下に示しますが、読み取り用メソッドと書き込み用メソッドが対になっていることに注目してください。

|操作|`InputStream`|`OutputStream`|
|----|----|----|
|ファイルのオープン|`new FileInputStream()`|`new FileOutputStream()`|
|1 バイトの読み取り/書き込み|`read()`|`write(int b)`|
|配列の要素数だけ読み取り/書き込み|`read(byte[] b, int off, int len)`|`write(byte[] b, int off, int len)`|
|指定した長さだけ読み取り/書き込み|`read(byte[] b, int off, int len)`|`write(byte[] b, int off, int len)`|
|ファイルのフラッシュ| - |`flush()`|
|ファイルのクローズ|`close()`|`close()`|

バイト・ストリームを使用する際には、以下の点に注意してください。

* コンストラクタおよびメソッドは `IOException` (またはそのサブクラス) をスローする可能性があります。したがって、例外処理が必須となります。
* 処理の最後に必ずファイルのクローズが必要です。原則として try-catch 文の finally 句でクローズします。

### 8.1.2. 文字ストリーム

文字ストリームの中心となるのが、入力用の `Reader` クラスと出力用の `Writer` クラスです。これらのクラスには入出力の対象に依存しない共通のメソッドが用意されています。`Reader`、`Writer` とも、データのやり取りの対象になるのは原則として `char` 型の配列です (これが文字ストリームと呼ばれる所以です)。

ファイル入出力におけるこれらの具象クラスは `FileReader` クラスと `FileWriter` クラスになります。これらは主にテキストファイルの入出力を行うために用意されています。主なメソッドを以下に示しますが、読み取り用メソッドと書き込み用メソッドが対になっていることに注目してください。

|操作|`Reader`|`Writer`|
|----|----|----|
|ファイルのオープン|`new FileReader()`|`new FileWriter()`|
|1 文字の読み取り/書き込み|`read()`|`write(int c)`|
|配列の要素数だけ読み取り/書き込み|`read(char[] cbuf, int off, int len)`|`write(char[] cbuf, int off, int len)`|
|指定した長さだけ読み取り/書き込み|`read(char[] cbuf, int off, int len)`|`write(char[] cbuf, int off, int len)`|
|文字列の読み取り/書き込み| - |`write(String str)`|
|ファイルのフラッシュ| - |`flush()`|
|ファイルのクローズ|`close()`|`close()`|

文字ストリームを使用する際には、以下の点に注意してください。

* コンストラクタおよびメソッドは `IOException` (またはそのサブクラス) をスローする可能性があります。したがって、例外処理が必須となります。
* 処理の最後に必ずファイルのクローズが必要です。原則として try-catch 文の finally 句でクローズします。

### 8.1.3. 入出力ストリームの変換 (参考)

バイト・ストリームは文字ストリームに変換することができます。`InputStream` クラスはインスタンスを `InputStreamReader` クラス (`Reader` のサブクラス) のコンストラクタ引数として、`OutputStream` クラスはインスタンスを `OutputStreamWriter` クラス (`Writer` のサブクラス) のコンストラクタ引数としてそれぞれ渡すことで、文字ストリーム (`Reader` および `Writer`) として扱うことができるようになります。`InputStreamWriter` と `OutputStreamWriter` は文字コード変換を伴うテキストファイルの入出力を行うために用意されています。

`InputStreamWriter`/`OutputStreamWriter` をクローズすると、それらに渡されていた `InputStream`/`OutputStream` も自動的にクローズされます。

>NIO.2 を用いると、文字コードを指定してファイルをオープンできるようになるため、`InputStreamReader` および `OutputStreamWriter` は実質的に不要となります。

### 8.1.4. 入出力ストリームのバッファリング

一般に、入出力ストリームは `read`/`write` のメソッドが呼ばれる度に入出力を行います。そのため、入出力対象がメモリ等の高速なものでない限り、大きな負荷が掛かりやすくなります。そこで、こうした処理の効率化のためにバッファリングを行う入出力ストリームが用意されています。ファイル入出力ではバッファリングを行った方が良いでしょう。

|バッファリングを行う入出力ストリーム|対応する入出力ストリーム|
|`BufferedInputStream`|`InputStream`|
|`BufferedOutputStream|OutputStream`|
|`BufferedReader`|`Reader`|
|`BufferedWriter`|`Writer`|

バッファリングを行う入出力ストリームは、コンストラクタ引数に対応する入出力ストリームのインスタンスを渡すことで生成します。`BufferedReader` および `BufferedWriter` ではさらに行単位での読み取り/書き込みを行うためのメソッドが追加されます。バッファリングを行う入出力ストリームをクローズすると、対応する入出力ストリームも自動的にクローズされます。

|操作|`BufferedReader`|`BufferedWriter`|
|----|----|----|
|バッファの作成|`new BufferedReader()`|`new BufferedWriter()`|
|1 文字の読み取り/書き込み|`read()`|`write(int c)`|
|配列の要素数だけ読み取り/書き込み|`read(char[] cbuf, int off, int len)`|`write(char[] cbuf, int off, int len)`|
|指定した長さだけ読み取り/書き込み|`read(char[] cbuf, int off, int len)`|`write(char[] cbuf, int off, int len)`|
|1 行読み取り (*)|`readLine()`| - |
|改行の書き込み (*)| - |`newLine()`|
|文字列の書き込み (*)| - |`write(String str)`|
|バッファのフラッシュ| - |`flush()`|
|バッファのクローズ|`close()`|`close()`|
(*) 行単位での入出力を行うためのメソッド。改行コードは OS によって異なるため、その差違を吸収するようなメソッドが用意されている。

>NIO.2 では常にバッファリングを行う入出力ストリームを用いてテキストファイルを扱います。

## 8.2. NIO.2

### 8.2.1. NIO.2

Java は最初のバージョンから入出力ストリームをはじめとする豊富な入出力 API を備えていました。それらは現在でも十分に通用する優秀な API ですが、Java が普及するとともに入出力 API に対する改善要望が多数寄せられるようになりました。それらの要望を吸収し入出力 API の近代化を図ったものが NIO (New I/O) および NIO.2 (New I/O 2) です。ファイル操作に関連する改善は NIO.2 によって行われています。この章ではファイル操作に NIO.2 を利用する前提で話を進めます。

>NIO と NIO.2 は大規模な API 刷新プロジェクトであり、すべてを同時リリースすることができませんでした。そこでまず `InputStream`/`OutputStream` の高速化 (チャネルの導入) を中心とした NIO を J2SE 1.4 でリリースし、その後ファイル操作の簡略化を中心とした NIO.2 を Java SE 7 でリリースする形を採りました。

### 8.2.2. Path インタフェース

`Path` インタフェースはファイルのパスを表します。

### 8.2.3. Files クラス

`Files` クラスはファイル操作およびファイル・システム操作を取りまとめたユーティリティ・クラスです。NIO.2 によるファイル操作の大半は、この `Files` クラスが起点となります。

`Files` クラスが提供するメソッドのうちファイル・システム操作に関するものには、以下のようなものが用意されています。具体的なメソッド名については API ドキュメントを参照してください。

* ファイルのコピー (※NIO.2 以前、実はファイルのコピー機能も提供されていなかった)
* ファイルの移動 (リネーム)
* ディレクトリの作成
* 空ファイルの作成
* ハードリンク/シンボリックリンクの作成
* 一時ディレクトリの作成
* 空の一時ファイルの作成
* ファイル (ディレクトリ) の削除
* ファイル (ディレクトリ) の存在チェック
* ファイル (ディレクトリ) のサイズ・属性・更新日時・所有者等の取得
* ディレクトリ・ツリーのスキャン

`Files` クラスが提供するメソッドのうち、ファイル操作に関するものには、以下のようなものが用意されています (チャネルが関連するもの等は省略します)。参考までに、具体的なメソッド名を [ ] 内に示します。詳細については API ドキュメントを参照してください。

* ファイルを読み取り用に開き BufferedReader を返す [`newBufferedReader()`]
* ファイルを書き込み用に開き BufferedWriter を返す [`newBufferedWriter()`]
* ファイルを読み取り用に開き InputStream を返す [`newInputStream()`]
* ファイルを書き込み用に開き OutputStream を返す [`newOutputStream()`]
* ファイルからすべてのバイトを読み取り byte[] で返す [`readAllBytes()`]
* ファイルからすべての行を読み取り List<String> で返す [`readAllLines()`]
* ファイルからすべての行を読み取り Stream<String> で返す [`lines()`]  (Java SE 8 以降)
* すべてのバイトまたは行をファイルに書き込む [`write()`]  (一部の書式は Java SE 8 以降)

※`readAllBytes()`、`readAllLines()`、`lines()` および `write()` は、完了後にファイルがクローズされる。

## 8.3. ファイル操作の実際

### 8.3.1. try-with-resources 文

```
public void readFile(Path path) throws IOException {
    BufferedReader reader = Files.newBufferedReader(path);
    try {
        String line = reader.readLine();
        while (line != null) {
            ...
            line = reader.readLine();
        }
    } catch (IOException e) {
        ...
        // IOException の再スロー、場合によっては catch 節ごと省略可能
        throw e;
    } finally {
        reader.close();
    }
}
```

```
public void readFile(Path path) throws IOException {
    try (BufferedReader reader = Files.newBufferdReader(path)) {
        String line = reader.readLine();
        while (line != null) {
            ...
            line = reader.readLine();
        }
    } catch (IOException e) {
        ...
        // IOException の再スロー、場合によっては catch 節ごと省略可能
        throw e;
    } // close() が不要となるので、finally 節も省略できる
}
```

```
try (リソースの宣言) {
    ...
} catch (例外) {
    ...
} finally {
    // close() は不要
    ...
}
```

### 8.3.2. Files クラスの readAllLines メソッドと write メソッドの利用


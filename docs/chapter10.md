# 10. マルチスレッド

この章では Java が持つ並行処理プログラミングの基礎について示します。C、JavaScript および Visual Basic では並行処理という概念は存在しませんが、Java では言語仕様と標準 API の双方で並行処理を強力にサポートしています。Java が採用している並行処理プログラミングのモデルは「マルチスレッド」と呼ばれているもので、現在のコンピュータ環境では広くサポートされている概念でもあります。

なお、Python では Java 同様にマルチスレッドによる並行処理がサポートされています。C は各 OS に固有のライブラリを使用することで並行処理をサポートします。C++ には標準ライブラリのマルチスレッド・サポートを使用する方法と、C 向けのライブラリを使用する方法の 2 通りが用意されていますが、現時点では C 向けのライブラリを使用するケースが多いようです。JavaScript は並行処理をサポートしませんが、擬似的に並行処理を行うライブラリの開発が進められています。これらに対して、Visual Basic は並行処理を全くサポートしません。

Java を学習し始めてしばらくのうちは、並行処理について考慮しなくても大きな問題は発生しません。しかし、Java は実行環境全体で並行処理を活用しているため、本格的にプログラムを記述する場合には並行処理を (それを自発的に使用するか否かに関わらず) 意識する必要があります。始めに並行処理を意識せずにアプリケーションを開発して後から並行処理を考慮した形に作り直す方法もありますが、それよりは初めから並行処理を意識してアプリケーションを開発した方が効率的です。

## 10.1. マルチタスクとマルチスレッド

コンピュータの CPU にかかる負荷は、実際には一様ではありません。複雑な数値演算を行っている場合などは非常に高い負荷で動作していますが、一方で I/O などのリソースへのアクセス時は、リソースからの応答が返ってくるまで基本的にアイドル状態となっています。CPU がアイドル状態となっている時間を別のプログラムの処理に充てることで CPU の稼働率を上げようという発想で生まれたのが「マルチタスク」という考え方です。

マルチタスクは 1960 年代後半から研究が進められていた「タイムシェアリング・システム (TSS)」が生んだ 1 つの成果です。タイムシェアリング・システムは当時同時に 1 人しかコンピュータにアクセスできなかった環境を、複数人で同時にアクセスするための手法として開発されたものです。タイムシェアリング・システムとして最も有名なものは Unix でしょう (Unix は初めてファイル・システムを実用化した OS としても知られています)。Unix が採用したアプローチは、アプリケーションの実行単位を「プロセス」とし、各プロセスに CPU、メモリ、I/O やネットワークなどのリソースを割り当てた上で、OS である Unix がプロセス同士でリソースの競合が起こらないように調停を行うというものです。これは後発の Windows、Linux、Mac OS などにも引き継がれている考え方です。

ここでプロセスに注目すると、実はプロセス自体も CPU にかかる負荷が一様ではないことが分かります。特にプロセスが I/O の応答待ちをしている間、そのプロセスだけに注目するとやはりアイドル状態になっています。そこでプロセスをさらに「スレッド」という単位に分割し、プロセス内部でもリソースを有効活用できる仕組みを作り上げたわけです。最も原始的なプロセスではスレッドは 1 つしか存在しませんが、一定規模のプロセスになると複数のスレッドを活用してリソースを上手く活用するようになります。これを「マルチスレッド」といいます。

プロセスとスレッドを比べると、プロセスは OS から各種リソースを割り当てられ、プロセス間でのリソースの競合については OS が調停を行います。一方でスレッドはプロセスが OS から割り当てられた各種リソースを共用するため、スレッド間でリソース競合対策を行わなければなりません (OS はスレッド内のリソース配分にまで介入しないため)。ここがマルチスレッドの難しいところではあるのですが、コンピュータの性能を最大限発揮するための仕組みとしてマルチスレッドは欠かせない存在となっています。

>なお、Java VM でアプリケーションを実行する際には、アプリケーションを担当する main スレッドが必ず起動しますが、実際にはガベージ・コレクションなどを担当する裏方のスレッドも同時に起動します。無数のシステムでガベージ・コレクションの多発によるアプリケーションの大幅なパフォーマンス低下が発生していますが、これはガベージ・コレクションのスレッドの処理が忙しくなり、main スレッドへのリソース配分が削られる (最悪の場合、ガベージ・コレクション最優先のために main スレッドが一時停止に追い込まれる「Stop the World」が発生する) のが原因です。Java VM のチューニングに問題がある場合もありますが、多くの場合はアプリケーションの設計が不適切であることに起因します。

Java では言語仕様と標準 API の双方でマルチスレッドを強力にサポートしています。初期のマルチスレッド・サポートはお世辞にも優れたものとは言えないものでしたが、バージョンを重ねることで使い勝手が大幅に向上しています。

## 10.2. マルチスレッドの基礎

スレッドには以下のような利点があります。

- マルチ・プロセッサ (マルチ・コア) を有効活用できる。
- 設計を単純化できる (単一の複雑な処理を、複数の単純な逐次処理に置き換えることができる)。
- 非同期処理イベントの処理を単純化できる (非同期処理を複数の同期処理で置き換えることができる)。
- 応答性の良いユーザー・インタフェースを実現できる。

Java に限っては、GUI ライブラリである AWT、Swing および JavaFX は、いずれもイベント・ハンドラの記述にスレッドを用いています。Java の最初のバージョンから備わっている AWT は、当時の他の GUI ライブラリが複雑な非同期処理を用いていたのに対して、スレッドを用いたシンプルで確実な方法を採用した点で画期的でした。

いいことずくめのようですが、一方で、スレッドは以下のようなリスクも抱えています。

- 同期化が不適切の場合、複数のスレッドの実行順が予測不可能となり、意図しない実行結果となる。
- ハング状態を生むエラーが様々で (デッドロックなど)、かつそれらは開発時や試験時に検出困難である。
- 実行するスレッドが多すぎるとスレッドの管理に CPU リソースを割かれ、逆に実行効率が低下する。

一例として、同期化が不適切な場合を挙げてみましょう。同期化とは、複数のスレッドが同じリソースにアクセスする際の交通整理のようなものです。以下に挙げる `UnsafeSequence` クラスは `getNext` メソッドを呼び出すことで重複のない順序数を取得するクラスです。一見すると正しいプログラムに思えますが、実は大きな落とし穴が潜んでいます。

```java
/**
 * 順序数を返すメソッド getNext を持つクラスです。
 * このクラスはスレッドセーフではありません。
 */
public class UnsafeSequence {

    private int nextValue;
    
    /**
     * 重複のない数を返します。
     * @return 順序数
     */
    public int getNext() {
        return nextValue++;
    }
}
```

仮に `UnsafeSequence.getNext()` メソッドが 2 つのスレッド ThreadA と ThreadB から同時にアクセスされたとしましょう。この時、ThreadA が `UnsafeSequence.getNext()` を呼び出した結果と、ThreadB が `UnsafeSequence.getNext()` を呼び出した結果は、実はわかりません。ThreadA が `0`、ThreadB が `1` をそれぞれ取得する場合もあれば、その逆の場合もあります。さらに ThreadA、ThreadB ともに `0` を取得してしまう場合さえあります。これは `getNext` メソッドが同期化を行っておらず、ThreadA と ThreadB による競合状態を許してしまうからです。その結果、`nextValue` フィールドのインクリメントがプログラマの意図した通りに行われなくなります。

これに対する処方箋は、`getNext` メソッドを同期化することです。メソッドの同期化は、以下に示すように `synchronized` を付加するだけです。

```java
/**
 * 順序数を返すメソッド getNext を持つクラスです。
 * このクラスはスレッドセーフです。
 */
public class Sequence {

    private int nextValue;
    
    /**
     * 重複のない数を返します。
     * @return 順序数
     */
    public synchronized int getNext() {
        return nextValue++;
    }
}
```

上記の同期化を行った結果、一方のスレッドが `getNext` を呼び出している間に他のスレッドは待機状態となり、`nextValue` フィールドのインクリメントはプログラマの意図した通りに行われるようになります。修正後の `Sequence` クラスのように、複数のスレッドから同時にアクセスされたときに、実行順やタイミングに関わらず、また呼び出し側で特別な配慮をしなくても正しく振る舞う状態を「スレッドセーフ」といいます。

>すべてのクラスがスレッドセーフである必要はありません。スレッドセーフを保つためには相応のコストがかかるためです。例えば、Java Collections Framework ([9 章](chapter09.md) を参照) はスレッドセーフではありません。ただし、Java のクラスやメソッドが複数のスレッドから呼び出される可能性があることだけは常に意識しておく必要があります。そのためにも、ドキュメンテーション・コメント (Javadoc) にそのクラスの同期化に対するポリシー (最低限スレッドセーフか否か) は明示しておきましょう。

さて、先に挙げた `UnsafeSequence` クラスのように、複数のスレッドが同じ可変なフィールドに対して同期化なしでアクセスしている場合には、そのプログラムは欠陥プログラムです。修正する方法は 3 つあります。

- そのフィールドを複数のスレッドが**共有しないようにする**。
- そのフィールドを**変更不能**にする。
- そのフィールドへのアクセスを常に**同期化**する。

ただし、これらの修正を既存のクラスに対して行うのは決して容易ではありません。そのため、クラスの設計段階でスレッドセーフにしてしまう方が簡単です。

----

### 【コラム】 Servlet/JSP はマルチスレッドで動作している

サーバーサイド Java の入門でよく用いられている Servlet と JSP (Java Server Pages) ですが、これらが実はマルチスレッドで動作していることにお気づきでしょうか？

Servlet は複数のリクエストを処理するに当たって、レスポンス向上のためにプロセスではなくスレッドで動作しています。これは同時に、Servlet で定義したフィールドを同期化しないと意図しない結果が得られる可能性を示唆しています。さらに、Servlet ではブラウザを開いている限り状態を保持するための「セッション・オブジェクト (`HttpSession`)」がサポートされていますが、これも複数スレッドから同時にアクセスされる可能性があるためスレッドセーフにしておくべきです。これらの問題は負荷試験でも検出できず見過ごされがちですが、アプリケーションが実運用に入ってから**忘れた頃にやってくる**類のトラブルであるため、十分に注意をして設計を行いましょう。

JSP は HTML にコードを埋め込む「PHP スタイル」のコーディングで簡単にサーバーサイド Java アプリケーションを構築可能な技術ですが、実行時には同等の Servlet に変換する仕組みとなっています。そのため、JSP も最終的には Servlet に変換されマルチスレッドで動作することとなり、上記の問題点がそのまますべて当てはまることになります。

## 10.3. Thread クラス

### 10.3.1. Thread と Runnable を用いたスレッドの生成と実行

Java のプログラムでマルチスレッドを実現する最も原始的な方法は `Thread` クラスと `Runnable` インタフェースを使用することです。`Thread` クラスはスレッドそのものを表し、`Runnable` インタフェースはスレッドで実行すべきタスクを表します。`Thread` と `Runnable` を用いたスレッドの生成と実行の例を以下に示します。

```java
Runnable task = new Runnable() {
    // タスクの処理
    ...
};

// タスク task を実行するスレッド thread を生成する
Thread thread = new Thread(task);

// スレッド thread を開始する (内部的には thread.run() が実行される)
thread.start();
```

上記の `thread.start()` 呼び出し後は制御がすぐに呼び出し元のメソッドに戻り、タスクはバックグラウンドで処理されます。ただし、タスクの実行が完了したという通知は呼び出し元メソッドには行われません。つまり、**呼び出し元メソッドでは別スレッドで実行中のタスクの進捗状況を把握することができない**のです。

なお、`Runnable` インタフェースは引数も戻り値も持たず、例外をスローすることもできないとても単純なインタフェースです。後述の `Callable` インタフェースは `Runnable` をより柔軟に扱いたいという要望から追加されたものです。

```java
@FunctionalInterface
public interface Runnable {
    void run();
}
```

### 10.3.2. スレッドの待機

前節では**呼び出し元のスレッドでは別スレッドで実行中のタスクの進捗状況を把握することができない**と説明しましたが、タスクの完了を把握できないことはしばしば問題を引き起こします。そこで `Thread` クラスには、実行中のスレッドが終了するまで呼び出し元のメソッドを待機する `join` メソッドが用意されています。

```java
Thread thread = new Thread(task);
thread.start();

// task の実行が終了するまでここで待機する
thread.join();
```

上記の書き換え例では、`thread.join()` でタスクの実行が完了するまで呼び出し元のメソッドの実行がブロックされてしまうという難点があります。さらに悪いことに、`Thread.join()` にはスレッドの実行結果を返す手段がありません。しかし、`Thread` メソッドでできることと言えばこのくらいなのです。

>実際には呼び出し元やタスクを複雑に連携させて擬似的にタスクの進捗状況を把握することは可能ですが、ソース・コードが複雑化してバグの温床となる (特にマルチスレッドのバグは発見が困難である) ことから、避けた方が無難でしょう。

なお、`join` メソッドの制約として、チェック例外 `InterruptedException` をスローすることが挙げられます。`join` メソッドは完了まで長時間かかる場合もあり、他のスレッドからの割り込み要求を受け付けるようにするためです。`InterruptedException` の簡単でかつ安全な処理方法は以下の 2 通りです。

- `throws` 句に `InterruptedException` を追加して、呼び出し元に処理を委ねる。
- `InterruptedException` をキャッチして、`Thread.currentThread().interrupt()` を実行する。

最初の方法は呼び出し元に対して `InterruptedException` をスローするだけです。`throws` 句に `InterruptedException` を追加できる状況にあるのなら、この方法が一番シンプルです。

```java
/**
 * InterruptedException の処理 (1) -- throws 句に InterruptedException を追加して、呼び出し元に処理を委ねる。
 */
public Integer execute() throws InterruptedException {
    ...
    Thread thread = new Thread(task);
    thread.start();

    // InterruptedException の処理は呼び出し元に委ねる
    thread.join();
    ...
} 
```

2 番目の方法は `InterruptedException` をキャッチして、`Thread.currentThread().interrupt()` を実行します。`Runnable.run()` メソッドのように `throws` 句を使用できない場合は必然的にこの対応となります。

>2 番目の方法は、現在のスレッドに対して割り込み要求を再発行します。放っておくと割り込み要求はなかったことにされてしまうため、それを防ぐための措置です。

```java
/**
 * InterruptedException の処理 (2) -- 例外をキャッチして Thread.currentThread().interrupt() を実行する。
 */
public void run() {
    ...
    Thread thread = new Thread(task);
    thread.start();

    try {
        ...
        thread.join();
        ...
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}
```

ただし、`InterruptedException` をキャッチした後、例外処理を全く行わない (例外を握りつぶす) ことは良くない方法であるため避けてください。

```java
/**
 * InterruptedException の処理としてやってはいけない -- InterruptedException を握りつぶす。
 * ただし、実務ではこのようなコードを頻繁に見かけるので、決して真似をしないように。
 */
public void run() {
    ...
    Thread thread = new Thread(task);
    thread.start();

    try {
        ...
        thread.join();
        ...
    } catch (InterruptedException e) {
        // やってはいけない : InterruptedException を握りつぶしている
    }
}
```

### 10.3.3. スレッドのスリープ

`Thread` クラスには、現在のスレッドをスリープ (一定時間だけ待機) するための `sleep` メソッドがあります。`sleep` メソッドは `static` メソッドです。`Thread.sleep` メソッドはマルチスレッドとは直接関係なく、現在のメソッドで一定の待ち時間を設けたい場合にもよく使われます。実は `sleep` も `InterruptedException` をスローするため、`join` と同様の注意が必要です。

```java
/**
 * InterruptedException の処理例 -- 例外をキャッチして Thread.currentThread().interrupt() を実行する。
 */
public void run() {
    ...
    try {
        // 現在のスレッド = main メソッドを 5000 ミリ秒スリープする
        Thread.sleep(5000);
    } catch (InterruptedException e) {
        // InterruptedException を握りつぶしてはいけない
        Thread.currentThread().interrupt();
    }
    ...
}
```

### 10.3.4. Thread クラスの問題点

実のところ、`Thread` クラスにはいくつかの問題点があります。これは Java のマルチスレッドが抱える宿命的な問題点と言い換えることもできます。

- `Thread` クラスはスレッドを開始することはできるが、実行中のスレッドを強制終了させることができない。スレッドの実行を強制的に制御するための `Thread.stop`、`Thread.suspend`、`Thread.resume` の各メソッドが初期から実装されているが、設計者の思い通りに動かなかった (ちなこに `Thread.stop` は現在使用できなくなっている)。
- `Thread` はスレッドの作成、タスクの割り当て、スレッド (タスク) の実行など、スレッドに関わる一切を引き受けている。マルチスレッドという複雑な問題に対して `Thread` クラスだけに任せるのはさすがに無理があった。
- `Thread` クラスを支えるクラスとインタフェースは初期の段階から整備されていたものの、お世辞にも使いやすいものとは言えなかった。

これらの問題点は次節で取り上げる Concurrency Utilities (Java SE 5.0 から導入) によって多くが解消されています。そのため、Java のマルチスレッド・プログラミングでは Concurrency Utilities を利用するようにしましょう。

## 10.4. Concurrency Utilities

Concurrency Utilities は Java のマルチスレッドに関する扱いを大幅に改善するためのフレームワークです。並行処理プログラミングの権威である Brian Goetz が中心となって設計・開発した API です。大きな特徴は、スレッドの作成、タスクの割り当て、スレッド (タスク) の実行をそれぞれ専用のクラスとインタフェースに分離して、マルチスレッドの複雑性を整理したところにあります。

### 10.4.1. Executor インタフェース

`Executor` インタフェースは、タスクを割り当ててスレッドを実行するだけのものです。タスクがいつ実行されるのかについては、インタフェースのため規定はしていません。`Executor` の実装は非常に簡単なものです。

```java
public Executor {
    void execute(Runnable command);
}
```

`Executor` の具体的な使い方は、以下のようになります。

```java
Runnable task1 = ... ;  // タスク 1 の記述
Runnable task2 = ... ;  // タスク 2 の記述

Executor executor = ... ;  // Executor の実装が別途必要
executor.execute(task1);  // タスク 1 の実行
executor.execute(task2);  // タスク 2 の実行
```

`Executor` の真価は、それがインタフェースであり、アプリケーションの都合に合わせて自由に実装できることにあります。幸い、Concurrency Utilities では `Executors` クラスにすぐに使える `Executor` の実装が定義されているため、これを使うのが手っ取り早いでしょう (そして、ほとんどのケースで事足りるはずです)。

`Executors` クラスが用意している `Executor` 実装のうち、主なものを下記に示します。`static` メソッドで簡単にインスタンスを取得できるため、扱いは非常に簡単です。これらはルールに基づいてあらかじめスレッドを生成して保持することから「スレッド・プール」と呼ばれています。

|`static` メソッド名       |説明                                               |
|-------------------------|---------------------------------------------------|
|`newFixedThreadPool`     |固定サイズのスレッド・プールを作り、プールサイズが一定になるよう努める|
|`newChachedThreadPool`   |処理要求に応じてスレッド・プールのサイズを動的に変更する|
|`newSingleThreadExecutor`|必ず 1 つのスレッドを提供する (当然、プール・サイズは固定)|
|`newScheduledThreadPool` |スレッド実行のスケジューリングを考慮、プール・サイズは固定|

上記のコードは、`Executors` が提供するスレッド・プールを使用すると、例えば以下のように書き換えられます。

```java
Runnable task1 = ... ;  // タスク 1 の記述
Runnable task2 = ... ;  // タスク 2 の記述

// Executor の取得 = スレッドプールの作成
Executor executor = Executors.newFixedThreadPool(20);

executor.execute(task1);  // タスク 1 の実行
executor.execute(task2);  // タスク 2 の実行
```

>スレッド・プールのサイズを超えてタスクにスレッドを割り当てようとすると待ちが発生します。それによりプログラムの挙動がおかしくなることはありませんが、パフォーマンスは明らかに低下するため、適切なサイズのスレッド・プールとなるよう種類やサイズを検討してください。

### 10.4.2. ExecutorService インタフェース

`Executor` を使用するだけでは、`Thread` を使用する場合と変化を感じないかもしれません。しかし、`Executor` のサブインタフェースである　`ExecutorService` はスレッドの実行を細かく制御できるメソッドが多数追加されています。前節で取り上げた `Executors` クラスが提供するメソッドの戻り値は、`Executor` ではなく実は `ExecutorService` となっています。

`ExecutorService` には、タスクの実行に関わるメソッドと、スレッドの制御に関わるメソッドが用意されています。

#### 10.4.2.1. タスクの実行に関するメソッド

タスクの実行に関わるメソッドは、`Thread` クラスよりも柔軟に設計されています。単一のスレッドを実行する `submit`、複数のスレッドを同時に実行してすべての完了を待つ `invokeAll` メソッド、複数のスレッドを同時に実行するがどれか 1 つの完了を待つ `invokeAny` メソッドがあります。タスクの表現にも、`Runnable` インタフェースの他に、戻り値や例外スローを可能にした `Callable` インタフェースが追加されており、必要に応じて使い分けることができるようになっています。

|メソッド名  |引数                                               |戻り値                |説明|
|-----------|---------------------------------------------------|---------------------|----|
|`submit`   |`Callable<T>`                                      |`<T> Future<T>`      |タスクを実行する。例外スロー可|
|`submit`   |`Runnable`                                         |`Future<?>`          |タスクを実行する。|
|`submit`   |`Runnable, T`                                      |`<T> Future<T>`      |タスクを実行する。戻り値の型は `T`|
|`invokeAll`|`Collection<? extends Callable<T>>`                |`<T> List<Future<T>>`|タスクをすべて実行して結果を返す。
|`invokeAll`|`Collection<? extends Callable<T>>, long, TimeUnit`|`<T> List<Future<T>>`|タスクをすべて実行して結果を返す (タイムアウトあり)|
|`invokeAny`|`Collection<? extends Callable<T>>`                |`<T> T`              |タスクを実行して最初に完了したときその結果を返す。|
|`invokeAny`|`Collection<? extends Callable<T>>, long, TimeUnit`|`<T> T`              |タスクを実行して最初に実行したときその結果を返す (タイムアウトあり)。|

`Callable` インタフェースは `Runnable` インタフェース同様、非常にシンプルなインタフェースです。

```java
@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;
}
```

参考まで、`Callable` インタフェースでも戻り値を返さず例外もスローしない方法があります。

```java
@FunctionalInterface
public interface MyCallable extends Callable<Void> {
    Void call();
}
```

なぜこのような表記が可能なのかについては、各自の宿題としておきます (対して難しいことではありません)。

#### 10.4.2.2. スレッド状態に関わるメソッド

`ExecutorService` では、実はスレッドのシャットダウンがサポートされています。ただし、思い出してください。

>`Thread` クラスはスレッドを開始することはできるが、実行中のスレッドを強制終了させることができない。

Concurrency Utilities でもこの束縛から完全に逃れられる訳ではありません。ただし、`Thread` クラスに比べるとスレッドのシャットダウン処理が洗練化され、より安全な形でスレッドを終了させられるようにはなっています (ただし、完全ではありません)。

|メソッド名         |引数            |戻り値           |説明|
|------------------|----------------|----------------|------------------------------------------------------------|
|`shutdown`        |N/A             |`boolean`       |スレッドのシャットダウン。既に実行要求済みのスレッドは実行される。|
|`shutdownNow`     |N/A             |`List<Runnable>`|スレッドのシャットダウン。既に要求済みのスレッドは破棄される。|
|`isShutdown`      |N/A             |`boolean`       |このexecutorがシャットダウンしていた場合、trueを返す。|
|`isTerminated`    |N/A             |`boolean`       |シャットダウンに続いてすべてのタスクが完了していた場合、trueを返す。|
|`awaitTermination`|`long, TimeUnit`|`boolean`       |タスク完了、タイムアウト、割り込みのいずれかが発生するまで待機する。|

### 10.4.3. Future インタフェース

前節で取り上げた `ExecutorService` クラスの `submit`、`invokeAll` の各メソッドは、呼び出し後すぐに制御を返し、その際に `Future` インタフェースのインスタンス (`invokeAll` の場合はそのリスト) を返します。この `Future` インタフェースは実行中のスレッドと紐付いていて、スレッドの状態を取得する、実行完了まで待機する、スレッドの実行をキャンセルする、などの操作を行うことができます。

|メソッド名|引数|戻り値|説明|
|-----------|----|------|----|
|`get`|N/A|`V`|スレッド実行が完了するまで待機、タスクが `Callable` の場合は値を取得する。|
|`get`|`long, TimeUnit`|`V`|スレッド実行が完了するまで待機、タスクが `Callable` の場合は値を取得する (タイムアウトあり)。|
|`cancel`|`boolean`|`boolean`|スレッドの実行をキャンセルする。|
|`isCancelled`|N/A|`boolean`|スレッドの実行がキャンセルされた場合に `true` を返す。|
|`isDone`|N/A|`boolean`|スレッドが停止している場合に `true` を返す。|

ここで Concurrency Utilities の中核となる `ExecutorService` と `Future` が揃いました。これらを使った例を見てみましょう。

```java
// ExecutorService にスレッドプールを割り当てる
ExecutorService executor = Executors.newFixedThreadPool(20);

// Callable を使用してタスクを作成する
Callable<Integer> task = new Callable<>() {
    Integer execute() throws Exception {
        // タスクの処理
        ...
    }
};

// タスクをスレッドに割り当てる
Future<Integer> future = executor.submit(task);

try {
    // タスクを実行し、完了するまで待機する
    // このタスクは Integer (int) を返すので、戻り値を取得する
    int value = future.get();
} catch (InterruptedException e) {
    // スレッドが割り込まれた場合
    // 基本的に Thread.join() や Thread.sleep() と同じように処理すること
    ...
} catch (ExecutionException e) {
    // タスク (Callable) が例外をスローした場合
    // 基本的に例外連鎖となっているはずなので、原因例外を取得して処理すること
    ...
} finally {
    // 例外スロー時、スレッドはキャンセルしておくと安全
    // スレッドが正常に終了した場合は特に何も起こらない
    future.cancel(true);
}
```

`Thread` クラスの場合に比べるとコードそのものは長くなっていますが、各クラスの役割が明確になり、読みやすいコードになりました。タスクに `Callable` を使用できるため、スレッドの実行結果を返すことも (さらにはスレッド実行中にスローされた例外をキャッチすることも) できるようになっています。

参考まで、`Future` インタフェースの標準的な実装に `FutureTask` クラスがあります。主に `ExecutorService` の `submit`、`invokeAny` メソッドが返す `Future` の実装として用いられますが、実は単独で使用することもできます。スレッドプールが必要なければ、`FutureTask` を使用する方法もあります。

```java
// Callable を使用してタスクを作成する
Callable<Integer> task = ... ;

// FutureTask でタスクにスレッドを割り当てる
Future<Integer> future = FutureTask<>(task);

// 以下、省略
...
```

`Future` インタフェースの実装には、非常に強力な機能を持つ `CompleteableFuture` クラスが用意されています。これについては後述します。

### 10.4.4. 同期化コレクションと並行コレクション

[9 章](chapter09.md) で取り上げたコレクションは同期化されないため、コレクションをそのままフィールドに用いると先に述べたような問題が発生します。最悪の場合、コレクションが保持するデータが破壊されてしまうことさえあります。コレクションへのアクセスをすべて同期化する方法も考えられますが、`Collections` クラスにコレクション自体をラップして同期化するメソッド `synchronizedList`、`synchronizedSet`、`synchronizedMap` が用意されているため、これらを使うのが得策です。これらを同期化コレクションと呼びます。

```java
// 同期化された List
private List<String> list = Collections.synchronizedList(new ArrayList<>());

// 同期化された Set
private Set<Integer> set = Collections.synchronizedSet(new HashSet<>());

// 同期化された Map
private Map<Integer, String> map = Collections.synchronizedMap(new HashMap<>());
```

ただし、同期化コレクションを使用しているからといって、すべての操作が上手くいくとは限りません。以下に示すコードは同期化された List に対して末尾の要素の取得と削除を同時に行う場合です。このコードの結果は保証されません。

```java
// getLast メソッドは
// 1. deleteLast と同時に呼ばれるかもしれない
// 2. deleteLast と同じ list インスタンスが設定されるかもしれない
public static String getLast(List<String> list) {
    int lastIndex = list.size() - 1;
    return list.get(lastIndex);
}

// deleteLast メソッドは
// 1. getLast と同時に呼ばれるかもしれない
// 2. getLast と同じ list インスタンスが設定されるかもしれない
public static void deleteLast(List<String> list) {
    int lastIndex = list.size() - 1;
    list.remove(lastIndex);
}
```

同期化コレクションには、コレクションが持つイテレータが同期化されないという欠点があります。そのため、直接的あるいは間接的にイテレータを使用する操作については、同期化コレクションであっても追加で同期化が必要になります。

先にメソッドの同期化をご紹介しましたが、上記でメソッドの同期化を行うとメソッドの待機時間が長くなり過ぎる可能性があります。同期化における待機時間が長くなると、処理が複雑になるにつれてデッドロックなどの問題が発生しがちです。そのため、Java ではメソッドの同期化の他に、ブロックの同期化も可能になっています。

- 書式: `synchronized (object) { ... }` (`object`: ロックするインスタンス、`{ ... }`: ブロック)

それでは、上記のコードをブロックの同期化を用いて書き換えてみましょう。

```java
// getLast メソッドは
// 1. deleteLast と同時に呼ばれるかもしれない
// 2. deleteLast と同じ list インスタンスが設定されるかもしれない
public static String getLast(List<String> list) {
    // イテレータ操作のため追加の同期化
    synchronized (list) {
        int lastIndex = list.size() - 1;
        return list.get(lastIndex);
    }
}

// deleteLast メソッドは
// 1. getLast と同時に呼ばれるかもしれない
// 2. getLast と同じ list インスタンスが設定されるかもしれない
public static void deleteLast(List<String> list) {
    // イテレータ操作のため追加の同期化
    synchronized (list) {
        int lastIndex = list.size() - 1;
        list.remove(lastIndex);
    }
}
```

同期化コレクションがあるとはいえ、同期化を完全にするためにはさらなる同期化が必要になることがあります。同期化コレクション自体が元のコレクションよりパフォーマンスで不利になるにもかかわらず、さらにパフォーマンスを犠牲にしなければならない場合もあるということです。こうした同期化コレクションの欠点をカバーするため、Concurrency Utilities では新たにスレッドセーフなコレクションを提供します。それが並行コレクションです。

並行コレクションはいくつか用意されていますが、基本となるのは以下の 3 種類です。

|インタフェース|並行コレクションクラス|
|----|----|
|`Set`|`CopyOnWriteArraySet`|
|`List`|`CopyOnWriteArrayList`|
|`Map`|`ConcurrentHashMap`|

>`CopyOnWriteArrayList` と `CopyOnWriteArraySet` は要素を変更不可の形で保持し、書き込み操作が発生する都度コピーを作成することで同期化を完全なものとしています。その性質から要素数の少ないデータや読み取りが多いデータの保持を得意としますが、要素数が多く書き込みの多いデータはそれほど得意ではありません。`ConcurrentHashMap` は同期化された `HashMap` に比べて小さな粒度のロックを行うことで全体のパフォーマンスを向上させていますが、`Map.size()` メソッドの精度は低下します。ただし、これらのデメリットを差し引いても、同期化コレクションより並行コレクションを使用した方が良いでしょう。

### 10.5. アトミック変数クラス

アトミック変数とは、ロックを行わなくても並行処理が可能な変数をいいます。Concurrency Utilities には専用のアトミック変数クラスが用意されており、手軽に使用することができます。アトミック変数クラスには様々なものが用意されていますが、最も簡単なものとして `AtomicInteger`、`AtomicLong`、`AtomicBoolean` があります。

|プリミティブ型|アトミック変数への変換|プリミティブ型への変換|
|----|----|----|
|`boolean`|`AtomicBoolean.set()`|`AtomicBoolean.get()`|
|`int`|`AtomicInteger.set()`|`AtomicInteger.get()`|
|`long`|`AtomicLong.set()`|`AtomicLong.get()`|

`byte`、`short` は `int` へキャストして対応します。また、`float` は `Float.floatToIntBits()`/`Float.intBitsToFloat()` で `int` と相互変換、`Double` は `Double.doubleToLongBits()`/`Double.longBitsToDouble()` で `long` と相互変換することで対応します。

以下に `AtomicInteger` が持つメソッド (主に算術演算) を抜粋します。`AtomicLong` にも対応するプリミティブ型が `long` 型である同一のメソッドが備わっています。

|メソッド|引数|戻り値|説明|
|----|----|----|----|
|`addAndGet`|`int`|`int`|値を加算して、更新後の値を返す (引数が負数の場合は減算)|
|`getAndAdd`|`int`|`int`|値を加算して、更新前の値を返す (引数が負数の場合は減算)|
|`incrementAndGet`|N/A|`int`|値をインクリメントして、更新後の値を返す (`++value` に相当)|
|`decrementAndGet`|N/A|`int`|値をデクリメントして、更新後の値を返す (`--value` に相当)|
|`getAndIncrement`|N/A|`int`|値をインクリメントして、更新前の値を返す (`value++` に相当)|
|`getAndDecrement`|N/A|`int`|値をデクリメントして、更新前の値を返す (`value--` に相当)|
|`get`|`int`|`int`|現在の値を返す|
|`set`|`int`|N/A|値を更新する|
|`getAndSet`|`int`|`int`|値を更新して、更新前の値を返す|
|`compareAndSet`|`int, int`|`boolean`|現在の値==予想される値の場合、値を更新して `true` を返す|

アトミック変数は並行処理が可能なだけでなく、パフォーマンスも良好であるため、様々な用途に適用可能です (最も多く使用される用途はカウンタです)。アトミック変数クラスの詳細については、[Java SE API ドキュメント](http://docs.oracle.com/javase/jp/8/docs/api/)を参照してください。

## 10.6. CompleteableFuture クラス

前述の `FutureTask` クラス (あるいは `Future` インタフェース) には、タスクにスレッドを割り当てる方法として以下の 3 種類がありました。

- `submit` メソッド : 1 つのタスクをスレッドに割り当て、スレッドの完了を待つ。
- `invokeAll` メソッド : 複数のタスクをスレッドに割り当て、すべてのスレッドの完了を待つ。
- `invokeAny` メソッド : 複数のタスクをスレッドに割り当て、一番早いスレッドの完了を待つ。

スレッド実行のバリエーションは様々で、`FutureTask` では実現が難しい処理も存在します。その中でもわかりやすい例が「複数のタスクをスレッドに割り当て、順次実行して完了を待つ」処理です。`FutureTask` は ThreadA、ThreadB、ThreadC を同時に実行することはできても、順番に実行することができないのです。このような問題を解消するために Java SE 8 で追加された API が `CompleteableFuture` クラスです。

`CompleteableFuture` クラスを使用すれば、以下のように複数のタスクをマルチスレッドで順次実行することが可能になります。

```java
Runnable task1 = ... ;  // タスク 1 の記述
Runnable task2 = ... ;  // タスク 2 の記述
Runnable task3 = ... ;  // タスク 3 の記述

// CompleteableFuture インスタンスの取得
CompleteableFuture<Void> future = CompleteableFuture.runAsync(task1).andThen(task2).andThen(task3);

// CompleteableFuture 実行の待機; future.join() でも可
future.get();
```

`CompleteableFuture` の実行には、既定では Java SE 7 から追加された Fork/Join Framework が使用されます。Fork/Join Framework は小さなデータを「分割統治法」を用いて効率よく処理することに特化した仕組みで、これまで紹介したスレッド・プールとは動作特性が異なります。`CompleteableFuture` でスレッド・プールを使用したい場合には、上記のコードを以下のように書き換えてください。

```java
Runnable task1 = ... ;  // タスク 1 の記述
Runnable task2 = ... ;  // タスク 2 の記述
Runnable task3 = ... ;  // タスク 3 の記述

// スレッドプールの作成
ExecutorService executor = Executors.newCachedThreadPool();

// CompleteableFuture インスタンスの取得
CompleteableFuture<Void> future = CompleteableFuture.runAsync(task1, executor).andThen(task2).andThen(task3);

// CompleteableFuture 実行の待機; future.join() でも可
future.get();
```

Concurrency Utilities にはスレッド間で歩調を合わせる機能―ラッチ、セマフォ、バリア―が備わっています。また、`BlockingQueue` をはじめとするスレッドのスケジューリングに役立つ機能も持っています。`CompleteableFuture` はこれらの機能で実現可能なバリエーションのうち使用頻度が高いものを、非常に簡単な操作だけで実現してしまうクラスです。`CompleteableFuture` は [12 章](chapter12.md)で取り上げるラムダ式とも相性がよく、Java の並行処理プログラミングの切り札として今後普及していくことでしょう。

`CompleteableFuture` の詳細については、以下の記事を参考にしてください。

**参考: Java 技術最前線「Java SE 6完全攻略」**

- [詳解 Java SE 8 第19回](http://itpro.nikkeibp.co.jp/atcl/column/14/224071/010400014/)
- [詳解 Java SE 8 第20回](http://itpro.nikkeibp.co.jp/atcl/column/14/224071/010400015/)
- [詳解 Java SE 8 第21回](http://itpro.nikkeibp.co.jp/atcl/column/14/224071/012900016/)

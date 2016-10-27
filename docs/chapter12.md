# 12. ラムダ式と Stream API

## 12.1. 概要

ラムダ式と Stream API は、Java に関数型プログラミングの長所を取り込むために導入された機能です。関数型プログラミングの具体的な説明については割愛しますが、複数のコアを持つ現在の CPU の性能を生かす上で関数型プログラミングの考え方は都合が良いとされています。

>ラムダ式は C++ と Python でサポートされています。C と JavaScript では直接ラムダ式を扱うことはできませんが、C であれば関数ポインタ、JavaScript であれば関数オブジェクトといった言語仕様を活用することで似たような処理を実現することは可能です。Visual Basic についてはラムダ式もしくはその代用となる言語仕様はありません。

かつて、CPU の処理を高速化するにはもっぱら動作周波数を引き上げることで対応してきました。しかし、動作周波数の引き上げは消費電力の増大をもたらし、この方法では限界があることが分かってきました。身近な例では、Intel Pentium 4 プロセッサの NetBurst アーキテクチャが消費電力の急増により予想よりも早く性能限界に達してしまったことが挙げられます。それに対して近年では、CPU の動作周波数を抑える代わりに CPU のコアを複数持たせ並列処理をさせることで高速化するアプローチが主流となってきました。Intel Core 2 Duo プロセッサ以降が採用している Core アーキテクチャがその代表例です。そこで必要となってくるのが、複数の CPU コアを最大限に活用する手段であり、そのためにラムダ式と Stream API が導入されたのです。

関数型プログラミングの特徴の 1 つとして、コレクションを外部イテレータ (ループ) ではなく内部イテレータ (関数) で操作することが挙げられます。コレクションの各要素を並列処理可能だったとしても、外部イテレータでは要素の処理順序が明示されているため (拡張 for 文を思い出してください)、各要素の処理を CPU の複数のコアに分配するのは困難です。一方で内部イテレータは要素の処理順序を隠蔽しているため、可能であれば各要素の処理を CPU の複数のコアに対して容易に分配することができます。内部イテレータを使用したところで、必ずしも処理が複数のコアに分配されるとは限りませんが、外部イテレータと比較すると分配しやいと言えます。

Java では `Iterable` インタフェースに外部イテレータを返す `iterator()` メソッドと内部イテレータを提供する `forEach()` メソッドが定義されています。`Iterable` インタフェースの定義を以下に示します。

```java
public interface Iterable<T> {
    Iterator<T> iterator();  // 外部イテレータ
    default void forEach(Consumer<? super T> action);  // 内部イテレータ
    ...
}
```

まず、外部イテレータによる繰り返し処理を記述してみましょう (`List` が `Collection`、そして `Iterable` のサブインタフェースであることを思い出してください)。ここではわかりやすさのために拡張 for 文を使用しますが、while 文でも記述できます。

```java
List<String> list = new CopyOnWriteArrayList<>();
...
for (String s : list) {
    System.out.println(s);
}
```

次に、上記と同じ処理を内部イテレータで置き換えてみます。まず、内部イテレータの引数のクラスである `Consumer` について見てみましょう。

```java
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);  // 処理を記述する
    ...
}
```

`Consumer` はインタフェースで、引数を受け取り処理を行う (そして戻り値を返さない) メソッド `accept()` を持ちます。そして、`Consumer` インタフェースを `Iterable.forEach()` メソッドで使用するには、以下のように匿名クラスを使用します。

```java
List<String> list = new CopyOnWriteArrayList<>();
...
list.forEach(new Consumer<>() {
    void accept(String t) {
        System.out.println(t);
    }
});
```

このように記述すると、`list` の各要素に対応する `Consumer` の匿名クラスのインスタンスが生成され、要素がインスタンスに渡されます。そして、`forEach()` メソッド内で `Consumer.accept()` メソッドが呼び出され、要素に対する処理が行われます。ここでは `list` の要素をバラバラにして、それぞれ別個のインスタンスで処理させていますが、それによって次の効果が得られます。

- 要素の順序にこだわる必要がなくなる。
- 複数のインスタンスで独立して処理できるようになる。

これらにより、`forEach()` メソッド内では `Consumer.accept()` メソッドを複数のスレッドで動作させることができるようになります。さらに一歩進めると、各スレッドを CPU の複数のコアに割り当てることが可能となり、処理の高速化が図れます。先に内部イテレータを使うと CPU の複数のコアに処理を分配することができると言ったのは、具体的にはこのことを指しています。

>実際のところ、`Iterator.forEach()` のデフォルト実装は内部で拡張 for 文を呼び出すだけで、マルチスレッドで動作しているわけではありません。また、`CopyOnWriteArrayList` クラスでオーバーライドしているものの、スレッドセーフとなるように処理を書き換えているだけです。ただし、考え方としては内部イテレータそのものであり、実装クラスにてマルチスレッド化する余地は残されています。

実際のところ、匿名クラスのインスタンス生成とメソッド実行には相応のコストがかかるため、折角の処理高速化をそれで相殺されては困ります。また、匿名クラスは単純な処理を記述するのにも冗長な書き方をしなければならない面倒さがあります。そこで、匿名クラスよりも軽量で、かつ、同様の処理を簡潔に書けるような文法が追加されました。それが「ラムダ式」です。上記の内部イテレータの使用例をラムダ式で書き換えると以下のようになります。

```java
List<String> list = new CopyOnWriteArrayList<>();
...
list.forEach(t -> System.out.println(t));
```

ラムダ式の詳細については次節にて説明します。

## 12.2. ラムダ式

### 12.2.1. 関数インタフェース

インタフェースのうち、抽象メソッド (デフォルト実装は除く) を 1 つしか持たないものを「関数インタフェース」といいます。ラムダ式は関数インタフェースの匿名クラスの代わりとして書くことができます。

|関数インタフェース|メソッド|主な用途など|
|------------|------------|------------|
|`Runnable`|`void run()`|スレッドの処理 (戻り値は返さない)|
|`Callable<V>`|`V call()`|スレッドの処理 (戻り値を返す)|
|`Comparator<T>`|`int compare(T o1, T o2)`|コレクション (比較結果を数値で返す)|
|`Supplier<T>`|`T get()`|Stream API (戻り値を返し、引数は受け取らない)|
|`Consumer<T>`|`void accept(T t)`|Stream API (引数を受け取り、戻り値は返さない)|
|`Function<T, R>`|`R accept(T t)`|Stream API (引数を受け取り、戻り値を返す)|
|`Predicate<T>`|`boolean test(T t)`|Stream API (引数を受け取り、条件により `true` or `false` を返す)|

関数インタフェースは独自に定義することもできます。その際はインタフェース定義の前に `@FunctionalInterface` アノテーションを付加しておくと、コンパイラが関数インタフェースの要件を満たしているかチェックしてくれます (コンパイラによるチェックが不要であれば `@FunctionalInterface` アノテーションは不要です)。

### 12.2.2. ラムダ式の書き方

`List.sort()` メソッドに渡す `Comparator` の匿名クラスを例に、匿名クラスからラムダ式への書き換えについて説明します。まず、匿名クラスによる記述を以下に示します。

```java
List<String> list = ...;
list.sort(new Comparator<String>() {
    @Override
    public int compare(String s1, String s2) {
        return s1.length() - s2.length();
    }
});
```

`Comparator` インタフェースを用いることは `List.sort()` メソッドの宣言から自明なので、外側の匿名クラス定義部分を削除します。

```java
List<String> list = ...;
list.sort(
    public int compare(String s1, String s2) {
        return s1.length() - s2.length();
    }
);
```

次に、関数インタフェースの匿名クラスには `public` のメソッドが 1 つだけ存在することが明らかであるため、メソッド名とスコープを削除します。さらに戻り値の型も `return` 文から `int` 型であると類推できるため不要となります。そして、ラムダ式であることを示すために引数のリストと処理のブロックを `->` で繋ぎます。

```java
List<String> list = ...;
list.sort(
    (String s1, String s2) -> {
        return s1.length() - s2.length();
    }
);
```

さらに引数の型は `Comparator` インタフェースの定義から明らかとなるため不要です。

```java
List<String> list = ...;
list.sort(
    (s1, s2) -> {
        return s1.length() - s2.length();
    }
);
```

これで、匿名クラスからラムダ式への書き換えが完了しました。

さらに、処理を１行で記述できる場合には、`return` 文と文末のセミコロン、そして `{ }` の 3 点セットを省略可能です。

```java
List<String> list = ...;
list.sort(
    (s1, s2) -> s1.length() - s2.length()
);
```

整形すると以下の通りになります。

```java
List<String> list = ...;
list.sort((s1, s2) -> s1.length() - s2.length());
```

もし、引数が 1 つだけの場合には、引数を囲む `( )` も省略できます (多くの場合は省略します)。ただし、引数がない場合には `( )` の省略はできません。

### 12.2.3. ラムダ式の注意点

ラムダ式の処理は比較的自由に記述できますが、いくつかの注意点があります。

- 外側の変数を参照することは可能ですが、変更することはできません。これは匿名クラスと同じ仕様です (実質的 `final`)。
- ラムダ式の内部で変数を宣言することは可能ですが、外側の変数と同じ名前を使うことはできません。一方で、匿名クラスでは外側の変数と同じ名前を使うことができます。これは、ラムダ式はクラスではないためです。

ラムダ式は代入演算式と並んで、最も低い優先順位で評価される式です。

### 12.2.4. メソッド参照

ラムダ式には「メソッド参照」という略記が存在します。ラムダ式の引数をそのままメソッド、`static` メソッド、コンストラクタの引数として渡す場合には、下表に示すようにインスタンス名/クラス名とメソッド名 (コンストラクタの場合は `new`) を `::` でつなぎ、引数の記述を省略することができます。

|参照する対象|ラムダ式|メソッド参照|
|--------|--------|--------|
|インスタンスのメソッド|`e -> list.add(e)`|`list::add`|
|クラスの `static` メソッド|`s -> System.out.println(s)`|`System.out::println`|
|クラスのコンストラクタ|`() -> new ArrayList()`|`ArrayList::new`|

メソッド参照と Stream API を組み合わせて使用することにより処理記述を大幅に簡略化できる場合があります。メソッド参照はラムダ式本体と異なり必ず覚えなければいけないものではありませんが、知っていると便利な記法です。

## 12.3. Stream API

### 12.3.1. Stream API の概要

Stream API は、`Stream` と呼ばれるデータの集合に対して、内部イテレータを用いて様々な操作を行うための API です。Stream API は Java に対して関数型プログラミング言語の機能を取り込むためのものです。Stream API には以下のような特徴があります。

- 配列、コレクション、乱数、ファイルなど様々なデータから `Stream` を生成することができる。
- `Stream` 単位でフィルタリング、変換、重複排除、ソート、検索などを連続して実行することができる。
- `Stream` の各要素について並列で各種操作を行うことができる。
- `Stream` を配列やコレクションに変換したり、SQL のような集合演算で集計したりすることができる。

### 12.3.2. Stream の生成

`Stream` は様々なデータから生成することができます。主な生成メソッドを下表に示します。

|ソース|クラス|生成するメソッド|生成されるストリーム|
|----|----|----|----|
|配列|`Arrays`|`<T>stream(T[])`|`Stream<T>`|
| |`Arrays`|`stream(int[])`|`IntStream`|
|コレクション|`Collection<T>`|`stream()`|`Stream<T>`|
|任意の要素|`Stream`|`<T>of(T...)`|`Stream<T>`|
| |`IntStream`|`of(int...)`|`IntStream`|
|n から m|`IntStream`|`rangeClosed(int n, int m)`|`IntStream`|
|n から m - 1|`IntStream`|`range(int n, int m)`|`IntStream`|
|文字列|`String`|`chars()`|`IntStream`|
|ランダムな整数|`Random`|`ints()`|`IntStream`|
|ファイルの各行|`BufferedReader`|`lines()`|`Stream<String>`|
| |`Files`|`lines(Path)`|`Stream<String>`|
|ディレクトリ内の要素|`Files`|`list(Path)`|`Stream<Path>`|
| |`Files`|`walk(Path, int, FileVisitOption...)`|`Stream<Path>`|

`Stream` 生成の中で最も使用頻度が高いと思われるのは、`Collection.stream()` メソッドを用いたコレクションからの生成と、`Arrays.stream()` メソッドを用いた配列からの生成でしょう。これらはコレクション・配列から `Stream` への変換操作と捉えることもできます。

```java
// コレクション (List) から Stream への変換
List<String> list = new ArrayList<>();
...
Stream<String> stream1 = list.stream();

// 配列から Stream への変換
String[] array = new String[] { ... };
...
Stream<String> stream2 = Arrays.stream(array);
```

コレクション・配列以外からの生成方法としては、`Stream.of()` メソッドで直接 `Stream` を生成する方法、`IntStream.range()` メソッドまたは `IntStream.rangeClosed()` メソッドで指定範囲の `IntStream` を生成する方法が良く使用されます。これらより使用頻度は下がりますが、`Random.ints()` メソッドでランダムな `IntStream` を生成する、`Files.lines()` メソッドでファイルの各行から `Stream` を生成する、といった方法も比較的よく用いられる `Stream` の生成方法です。 

### 12.3.3. 中間操作

生成した `Stream` に対しては様々な操作を行うことができます。これらを中間操作と呼びます。中間操作の結果は `Stream` となるため、複数の操作を連続して行うことが可能です。主な中間操作を以下に示します。

|メソッド名|引数|概要|
|----|----|----|
|`filter()`|`T -> boolean`|フィルタリングする|
|`map()`|`T -> U`|`Stream<U>` へ変換する|
|`flatMap()`|`T -> Stream<U>`|`Stream<U>` へ変換する|
|`distinct()`|なし|同一の要素を除外する|
|`sorted()`|なし|自然順序で並び替える|
|`sorted()`|`(T, T) -> int`|並び替える|
|`peek()`|`T -> void`|デバッグ用途の `forEach()`|
|`limit()`|`long`|引数の件数に要素数を制限する|
|`skip()`|`long`|引数の件数文を読み飛ばす|

特に使用頻度の高いものは if 文に相当する `filter` とメソッド呼び出しに相当する `map` です。`distinct` や `sorted` は SQL の `DISTINCT` や `ORDER BY` に相当する操作です。あまり直感的でないのは `flatMap` ですが、これはネストした `Stream` をシンプルな `Stream` に変換する場合などに用います。

### 12.3.4. 直列処理と並列処理

`Stream` は、要素を先頭から順に処理する直列処理と、要素を同時に処理する並列処理の双方をサポートします。また、両者を混在させることもできます。`Stream` の以降の処理を直列処理する場合は `sequential()` メソッドを、並列処理にする場合は `parallel()` メソッドをそれぞれ呼び出し、直列処理と並列処理の変換を行います。初期状態の `Stream` は直列処理となります。

>Stream API の並列処理には Fork/Join Framework が使用されます ([10 章](chapter10.md) を参照)。元々 Fork/Join Framework は Stream API を実装するために開発されたもので、Stream API よりも一足早く Java SE 7 から搭載されています (正確にはラムダ式と Stream API のリリースが Java SE 8 まで延期されました)。

非常に多くのデータを多数の CPU コアを持つコンピュータで処理する場合は並列処理にすると処理の高速化を図ることができる場合があります。一方でデータがそれほど多くない場合やコンピュータの CPU コア数がそれほど多くない場合には並列処理の効果を得にくいとされています。実際のところ、この判断を机上で行うのは極めて困難とされています。直列処理と並列処理のどちらを選択すべきかは、同じ操作を直列処理と並列処理でそれぞれ計測して、その結果を持って判断するのが妥当でしょう。

`Stream` が何らかの順序をもっている場合、並列処理を行うと順序が失われてしまいます (適切な設定をすることで順序を維持できる場合もあります)。その点も注意が必要です。

### 12.3.5. 終端操作

`Stream` は一連の操作を終えたのちにクローズする必要があります。単純に `Stream.close()` メソッドでクローズすることもできますが、ほとんどの場合は操作した結果を残す形でクローズします。これらをまとめて終端操作と呼びます。主な終端操作を以下に示します。

|メソッド名|引数|戻り値|概要|
|----|----|----|----|
|`forEach()`|`T -> void`|`void`|要素ごとに何らかの処理を行う|
|`findAny()`|なし|`Optional<T>`|任意の要素を 1 つだけ取得する|
|`findFirst()`|なし|`Optional<T>`|最初の要素を取得する|
|`anyMatch()`|`T -> boolean`|`boolean`|いずれかの要素が条件に沿うか|
|`allMatch()`|`T -> boolean`|`boolean`|すべての要素が条件に沿うか|
|`noneMatch()`|`T -> boolean`|`boolean`|すべての要素が条件に沿わないか|
|`reduce()`|`(T, T) -> T`|`Optional<T>`|各要素から 1 つの要素に畳み込む|
|`collect()`|`Collector<? super T, A, R>`|`R`|各要素から 1 つのオブジェクトに集計する|
|`toArray()`|`int -> A[]`|`A[]`|すべての要素を含む配列を生成する|
|`count()`|なし|`int`|要素数を取得する|

`findFirst` および `findAny` は該当する要素が見つからない場合があるため、戻り値が `Optional` になります。`Optional` については後ほど取り上げます。また、`reduce` は汎用の終端操作であり、他では実現できない特殊な操作を実装する際に使用します。
 
コレクション・配列から `Stream` を生成する場合が多いのと同様に、`Stream` による操作結果をコレクション・配列の形で残す場合が多いです。これらは `Stream` からコレクション・配列への変換操作と捉えることもできます。

```java
Stream<String> stream = ... ;

// コレクション (List) への変換
List<String> list = stream.collect(Collectors.toList());

// 配列への変換
// String[]::new は i -> new String[i] と同じ
String[] array = stream.toArray(String[]::new);
```

なお、`Collectors` クラスには `List` への変換の他に `Set` や `Map` への変換、文字列結合、最大・最小・平均の算出、グルーピングなど様々な変換や集合演算を行うメソッドが用意されています。SQL に匹敵する豊富な集合演算を Java の強力な型チェックの下で行うことが可能です。

## 12.4. Optional

`Optional` は何らかの値を保持できるクラスです (必ずしも保持しなくてよい)。このクラスにはいくつかのメソッドが定義されており、それらを用いて値の `null` チェックやそれに関連する操作を行うことができます。

### 12.4.1. Optional の生成

`Optional` のインスタンスは、`of`、`ofNullable`、`empty` のいずれかのメソッドで生成することができます。

|メソッド名|引数|説明|
|--------|----|----------------|
|`of`|`T`|値をラップして `Optional` のインスタンスを生成する。値が `null` の場合は `NullPointerException` をスローする。|
|`ofNullable`|`T`|値をラップして `Optional` のインスタンスを生成する。値が `null` の場合は保持しない。|
|`empty`|なし|値を保持しない `Optional` のインスタンスを生成する。`Optional.ofNullable(null)` と同じ。|

### 12.4.2. Optional が保持する値の取得

`Optional` が保持する値を取得するには、4 種類のメソッドのいずれかを使用します。`get` は単純に値を取得しようとするメソッドで、それ以外は値を取得できない (値を保持していない) 場合に何らかの代替手段を提供するメソッドです。

|メソッド名|引数|説明|
|--------|--------|----------------|
|`get`|なし|値を取得する。値を保持しない場合は `NoSuchElementException` をスローする。|
|`orElse`|`T`|値を取得する。値を保持しない場合は引数の値を返す。|
|`orElseGet`|`Supplier<? extends T>`|値を取得する。値を保持しない場合は引数 (ラムダ式) で生成される値を返す。|
|`orElseThrow`|`Supplier<? extends Throwable>`|値を取得する。値を保持しない場合は引数 (ラムダ式) で生成される例外をスローする。|

以下に例を示します。

```java
// 値 (null の可能性がある)
String original = ... ;

// original をラップして Optional のインスタンスを生成する → value
Optional<String> value = Optional.ofNullable(original);

// get : 
// 値を保持する場合は値を s1 に代入し、保持しない場合は NoSuchElementException をスローする
String s1 = value.get();

// orElse : 
// 値を保持する場合は値を s2 に代入し、保持しない場合は引数の値を s2 に代入する
String s2 = value.orElse("default value");

// orElseGet : 
// 値を保持する場合は値に s3 を代入し、保持しない場合は引数 (ラムダ式) で生成される値を s3 に代入する
String s3 = value.orElseGet(() -> "default value");

// orElseThrow : 
// 値を保持する場合は値を s4 に代入し、保持しない場合は引数 (ラムダ式) で生成される例外をスローする
String s4 = value.orElseThrow(() -> new IllegalArgumentException());
```

### 12.4.3. Optional の状態判定

`Optional` が値を保持しているかどうかを判定するメソッドとして `isPresent` と `ifPresent` が用意されています。

|メソッド名|引数|戻り値|説明|
|--------|----|--------|--------|
|isPresent|なし|`boolean`|値を保持する場合は `true`、そうでない場合は `false` を返す|
|ifPresent|Consumer<? super T>|`void`|値を保持する場合は引数 (ラムダ式) を実行し、そうでない場合は何もしない|

### 12.4.4. Optinal と Stream API

`Optional` では Stream API の中間操作の一部をサポートします。サポートする中間操作は `filter`、`map` および `flatMap` です。

`Optional` は要素数が 1 または 0 の `Stream` のように扱われ、値を取得する操作を行うことなく Stream API によるデータ操作に使用することができます。

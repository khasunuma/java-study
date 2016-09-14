# 12. ラムダ式と Stream API

>【バージョン】 ラムダ式と Stream API はともに Java SE 8 で追加された機能です。Stream API は入出力ストリーム (I/O Stream) と名前こそ似ていますが全く別の API のため注意してください。

## 12.1. 概要

ラムダ式と Stream API は、Java に関数型プログラミングの長所を取り込むために導入された機能です。関数型プログラミングの具体的な説明については割愛しますが、複数のコアを持つ現在の CPU の性能を生かす上で関数型プログラミングの考え方は都合が良いとされています。

>かつて、CPU の処理を高速化するにはもっぱら動作周波数を引き上げることで対応してきました。しかし、動作周波数の引き上げは消費電力の増大をもたらし、この方法では限界があることが分かってきました。身近な例では、Intel Pentium 4 プロセッサの NetBurst アーキテクチャが消費電力の急増により予想よりも早く性能限界に達してしまったことが挙げられます。それに対して近年では、CPU の動作周波数を抑える代わりに CPU のコアを複数持たせ並列処理をさせることで高速化するアプローチが主流となってきました。Intel Core 2 Duo プロセッサ以降が採用している Core アーキテクチャがその代表例です。

関数型プログラミングの特徴の 1 つとして、コレクションを外部イテレータ (ループ) ではなく内部イテレータ (関数) で操作することが挙げられます。コレクションの各要素を並列処理可能だったとしても、外部イテレータでは要素の処理順序が明示されているため (拡張 for 文を思い出してください)、各要素の処理を CPU の複数のコアに分配するのは困難です。一方で内部イテレータは要素の処理順序を隠蔽しているため、可能であれば各要素の処理を CPU の複数のコアに対して容易に分配することができます。内部イテレータを使用したところで、必ずしも処理が複数のコアに分配されるとは限りませんが、外部イテレータと比較すると分配しやいと言えます。

Java では `Iterable` インタフェースに外部イテレータを返す `iterator()` メソッドと内部イテレータを提供する `forEach()` メソッドが定義されています。`Iterable` インタフェースの定義を以下に示します。

```
public interface Iterable<T> {
    Iterator<T> iterator();  // 外部イテレータ
    default void forEach(Consumer<? super T> action);  // 内部イテレータ
    default Spliterator<T> spliterator();  // ここでは使用しないので無視する
}
```

まず、外部イテレータによる繰り返し処理を記述してみましょう (`List` が `Collection`、そして `Iterable` のサブインタフェースであることを思い出してください)。ここではわかりやすさのために拡張 for 文を使用しますが、while 文でも記述できます。

```
List<String> list = new CopyOnWriteArrayList<>();
...
for (String s : list) {
    System.out.println(s);
}
```

次に、上記と同じ処理を内部イテレータで置き換えてみます。まず、内部イテレータの引数のクラスである `Consumer` について見てみましょう。

```
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);  // 処理を記述する
    default Consumer<T> andThen(Consumer<? super T> after);  // ここでは使わないので無視する
}
```

`Consumer` はインタフェースで、引数を受け取り処理を行う (そして戻り値を返さない) メソッド `accept()` を持ちます。そして、`Consumer` インタフェースを `Iterable.forEach()` メソッドで使用するには、以下のように匿名クラスを使用します。

```
List<String> list = new CopyOnWriteArrayList<>();
...
list.forEach(new Consumer<>() {
    void accept(String t) {
        System.out.println(t);
    }
});
```

このように記述すると、`list` の各要素に対応する `Consumer` の匿名クラスのインスタンスが生成され、要素がインスタンスに渡されます。そして、`forEach()` メソッド内で `Consumer.accept()` メソッドが呼び出され、要素に対する処理が行われます。ここでは `list` の要素をバラバラにして、それぞれ別個のインスタンスで処理させていますが、それによって次の効果が得られます。

* 要素の順序にこだわる必要がなくなる。
* 複数のインスタンスで独立して処理できるようになる。

これらにより、`forEach()` メソッド内では `Consumer.accept()` メソッドを複数のスレッドで動作させることができるようになります。さらに一歩進めると、各スレッドを CPU の複数のコアに割り当てることが可能となり、処理の高速化が図れます。先に内部イテレータを使うと CPU の複数のコアに処理を分配することができると言ったのは、具体的にはこのことを指しています。

>実際のところ、`Iterator.forEach()` のデフォルト実装は内部で拡張 for 文を呼び出すだけで、マルチスレッドで動作しているわけではありません。また、`CopyOnWriteArrayList` クラスでオーバーライドしているものの、スレッドセーフとなるように処理を書き換えているだけです。ただし、考え方としては内部イテレータそのものであり、実装クラスにてマルチスレッド化する余地は残されています。

実際のところ、匿名クラスのインスタンス生成とメソッド実行には相応のコストがかかるため、折角の処理高速化をそれで相殺されては困ります。また、匿名クラスは単純な処理を記述するのにも冗長な書き方をしなければなりません。そこで、匿名クラスよりも軽量で、かつ、同様の処理を簡潔に書けるような文法が追加されました。それが「ラムダ式」です。上記の内部イテレータの使用例をラムダ式で書き換えると以下のようになります。

```
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

```
List<String> list = ...;
list.sort(new Comparator<String>() {
    @Override
    public int compare(String s1, String s2) {
        return s1.length() - s2.length();
    }
});
```

`Comparator` インタフェースを用いることは `List.sort()` メソッドの宣言から自明なので、外側の匿名クラス定義部分を削除します。

```
List<String> list = ...;
list.sort(
    public int compare(String s1, String s2) {
        return s1.length() - s2.length();
    }
);
```

次に、関数インタフェースの匿名クラスには `public` のメソッドが 1 つだけ存在することが明らかであるため、メソッド名とスコープを削除します。さらに戻り値の型も `return` 文から `int` 型であると類推できるため不要となります。そして、ラムダ式であることを示すために引数リストと処理のブロックを `->` で繋ぎます。

```
List<String> list = ...;
list.sort(
    (String s1, String s2) -> {
        return s1.length() - s2.length();
    }
);
```

以上でラムダ式として成立しますが、まだ冗長さが残ります。引数の型は `Comparator` インタフェースの定義から明らかなので省略が可能です。

```
List<String> list = ...;
list.sort(
    (s1, s2) -> {
        return s1.length() - s2.length();
    }
);
```

さらに、処理を１行で記述できる場合には、`return` 文と文末のセミコロン、そして `{ }` を省略可能です。

```
List<String> list = ...;
list.sort(
    (s1, s2) -> s1.length() - s2.length()
);
```

整形すると以下の通りになります。

```
List<String> list = ...;
list.sort((s1, s2) -> s1.length() - s2.length());
```

もし、引数が 1 つだけの場合には、引数を囲む `( )` も省略できます (引数がない場合は `( )` の省略はできません)。

### 12.2.3. ラムダ式の注意点

ラムダ式の処理は比較的自由に記述できますが、いくつかの注意点があります。

- 外側の変数を参照することは可能ですが、変更することはできません。これは匿名クラスと同じ仕様です。Java SE 7 までは、匿名クラスが外側の変数を参照する場合、参照する変数に `final` を付加して読み取り専用であることを明示する必要がありました。Java SE 8 では `final` 自体は省略可能です (実質的 `final`) が、読み取り専用でなければならない点は変わりません。
- ラムダ式の内部で変数を宣言することは可能ですが、外側の変数と同じ名前を使うことはできません。一方で、匿名クラスでは外側の変数と同じ名前を使うことができます。これは、ラムダ式はクラスではないためです。

ラムダ式は代入演算式と並んで、最も低い優先順位で評価される式です。

Expression:
    LambdaExpression 
    AssignmentExpression
    
AssignmentExpression:
    ConditionalExpression 
    Assignment

Assignment:
    LeftHandSide AssignmentOperator Expression

LeftHandSide:
    ExpressionName 
    FieldAccess 
    ArrayAccess

AssignmentOperator:
    (one of) 
    =  *=  /=  %=  +=  -=  <<=  >>=  >>>=  &=  ^=  |=

LambdaExpression:
    LambdaParameters -> LambdaBody

LambdaParameters:
    Identifier 
    ( [FormalParameterList] ) 
    ( InferredFormalParameterList )

InferredFormalParameterList:
    Identifier {, Identifier}

FormalParameterList:
    ReceiverParameter 
    FormalParameters , LastFormalParameter 
    LastFormalParameter

FormalParameters:
    FormalParameter {, FormalParameter} 
    ReceiverParameter {, FormalParameter}

FormalParameter:
    {VariableModifier} UnannType VariableDeclaratorId

LastFormalParameter:
    {VariableModifier} UnannType {Annotation} ... VariableDeclaratorId 
    FormalParameter

VariableModifier:
    (one of) 
    Annotation final

VariableDeclaratorId:
    Identifier [Dims]

Dims:
    {Annotation} [ ] {{Annotation} [ ]}

LambdaBody:
    Expression 
    Block

MethodReference:
    ExpressionName :: [TypeArguments] Identifier 
    ReferenceType :: [TypeArguments] Identifier 
    Primary :: [TypeArguments] Identifier 
    super :: [TypeArguments] Identifier 
    TypeName . super :: [TypeArguments] Identifier 
    ClassType :: [TypeArguments] new 
    ArrayType :: new


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

### 12.3.2. Stream の生成

|ソース|クラス|生成するメソッド|生成されるストリーム|
|----|----|----|----|
|配列|`Arrays`|`<T>stream(T[])`|`Stream<T>`|
| |`Arrays`|`stream(int[])`|`IntStream`|
|コレクション|`Collection<T>`|`stream()`|`Stream<T>`|
|任意の要素|Stream|<T>of(T...)|Stream<T>|
| |IntStream|of(int...)|IntStream|
|n から m|IntStream|rangeClosed(int n, int m)|IntStream|
|n から m - 1|IntStream|range(int n, int m)|IntStream|
|文字列|String|chars()|IntStream|
|ランダムな整数|Random|ints()|IntStream|
|ファイルの各行|BufferedReader|lines()|Stream<String>|
| |Files|lines(Path)|Stream<String>|
|ディレクトリ内の要素|Files|list(Path)|Stream<Path>|
| |Files|walk(Path, TODO)|Stream<Path>|

### 12.3.3. 中間操作

|メソッド名|引数|概要|プリミティブ|
|----|----|----|----|
|filter()|T -> boolean|フィルタリングする|○|
|map()|T -> U|Stream<U>へ変換する|○|
|flatMap()|T -> Stream<U>|Stream<U>へ変換する|○|
|distinct()|なし|同一の要素を除外する|○|
|sorted()|なし|自然順序で並び替える|○|
|sorted()|(T, T) -> int|並び替える|×|
|peek()|T -> void|デバッグ用途の forEach()|○|
|limit()|long|引数の件数に要素数を制限する|○|
|skip()|long|引数の件数文を読み飛ばす|○|

### 12.3.4. 終端操作

|メソッド名|引数|戻り値|概要|プリミティブ|
|----|----|----|----|----|
|forEach()|T -> void|void|要素ごとに何らかの処理を行う|○|
|findAny()|なし|T|任意の要素を 1 つだけ取得する|○|
|findFirst()|なし|T|最初の要素を取得する|○|
|anyMatch()|T -> boolean|boolean|いずれかの要素が条件に沿うか|○|
|allMatch()|T -> boolean|boolean|すべての要素が条件に沿うか|○|
|noneMatch()|T -> boolean|boolean|すべての要素が条件に沿わないか|○|
|reduce()|(T, T) -> T|Optional<T>|各要素から 1 つの要素に畳み込む|○|
|collect()|Collector<? super T, A, R>|R|各要素から 1 つのオブジェクトに集計する|○|
|toArray()|int -> A[]|A[]|すべての要素を含む配列を生成する|○|
|count()|なし|int|要素数を取得する|○|

## 12.4. Optional


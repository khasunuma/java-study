# 10. 配列とコレクション

## 10.1. 配列

配列は、同じデータ型の変数をメモリ上の連続した領域に確保したものです。配列は通常、1 つの変数に格納できないような大きなデータ (例: ファイル、ネットワークの送受信データ) を保持するために用います。配列を構成する一つひとつの変数を「要素」といい、それぞれの要素には先頭から「添字 (インデックス)」と呼ばれる連番が振られています。

1. `<データ型>[] <配列名>;`
2. `<データ型>[] <配列名> = {[初期値のリスト]};`
3. `<データ型>[] <配列名> = new <データ型>[<要素数>];`

ArrayCreationExpression:
    new PrimitiveType DimExprs [Dims] 
    new ClassOrInterfaceType DimExprs [Dims] 
    new PrimitiveType Dims ArrayInitializer 
    new ClassOrInterfaceType Dims ArrayInitializer

DimExprs:
    DimExpr {DimExpr}

DimExpr:
    {Annotation} [ Expression ]

Dims:
    {Annotation} [ ] {{Annotation} [ ]}

ArrayAccess:
    ExpressionName [ Expression ] 
    PrimaryNoNewArray [ Expression ]


## 10.2. コレクション

### 10.2.1. コレクションと総称型 (ジェネリクス)

Java SE の標準 API には "Java Collections Framework" と呼ばれる、主要なデータ構造と基本的なアルゴリズムをサポートするクラスライブラリが含まれています。他の言語ではプログラマが必要なデータ構造とアルゴリズムを選択して独自に実装しなければならない場合もありますが、Java ではそれらがあらかじめ API として用意されているため、適切なデータ構造とアルゴリズムを選択するだけで済みます。

ここでは、Java Collections Framework で定義されているデータ構造をコレクションと呼びます。コレクションはインタフェースにて定義されており、各々要素の保持方法が異なる複数の実装クラスを持っています。実装クラスは通常、標準で用意されているものだけでも十分ですが、必要に応じて独自に定義することもできます。Java Collections Framework は `java.util` パッケージに集約されています。

主なコレクション (インタフェース) を以下に示します。

* `Collection` : コレクションのスーパーインタフェース。順序、要素の重複およびアクセス方法はサブインタフェースが決定する。
  * `Set` : 集合。順序を持たず要素の重複も許さない。
  * `List` : リスト。順序を持ち要素の重複も許す、配列同様に添字を用いて要素にアクセスする。
  * `Queue` : キュー。順序を持ち要素の重複も許す、FIFO (先入れ先出し) で要素にアクセスする。
  * `Deque` : 両端キュー。順序を持ち要素の重複も許す、FIFO (先入れ先出し) に加えて LIFO (後入れ先出し) で要素にアクセスする。
* `Map` : テーブル、連装配列とも。キーと値のペアの集合。

コレクションには、どのクラスの要素を設定するものかを示すための「型パラメータ」と呼ばれるものを持ちます。コレクションに限らず、型パラメータを持つクラスのことを「ジェネリクス (総称型)」と呼びます。以下に例を挙げます。

* `List<String>` : `String` クラスの要素を格納する `List`
* `Queue<Integer>` : `Integer` クラスの要素を格納する `Queue`
* `Map<String, BigDecimal>` : `String` クラスのキーと `BigDecimal` クラスの値のペアからなる `Map`

型パラメータに指定できるのはクラスのみでプリミティブ型を指定できないため、コレクションの要素にはプリミティブ型を設定することはできません。対応するラッパークラスを型パラメータに指定して、プリミティブ型とラッパー型の相互変換を使用する必要があります。ただし、相互変換自体は自動で行われるため (オートボクシング機能)、通常はプリミティブ型のままでコレクションの操作を行って構いません。

>プリミティブ型とラッパー型の自動相互変換 (オートボクシング機能) によるパフォーマンス劣化をソースコードレビューで指摘されることもありますが、実際はレビュワーが思っているほどの負荷にはなりませんので (最近の Java API 実装はそこまで愚かではない)、可読性を優先しましょう。

>【バージョン】ジェネリクス (総称型) が導入されたのは Java SE 5.0 以降で、それ以前の古い Java ではコレクションの要素は `Object` クラスで固定されていました。古い Java ではコレクションの要素から値を取り出す時にキャストが必須であり、一方でコレクションの要素にはどのようなインスタンスでも設定できてしまうことから (`Object` はすべてのクラスのスーパークラスであるため)、エラー (`ClassCastException`) が多発する原因でした。その反省から、現在の Java ではジェネリクス (総称型) が導入され、指定したクラス以外の要素は設定できないようになっています。

### 10.2.2. コレクションの実装と選び方

前節で述べた通り、コレクションにはそれぞれデータの保持方法が異なる複数の実装クラスがあります。保持方法によりアクセス特性に違いがあるため、最適な実装クラスを選択するようにしましょう。

|保持方法|`Set<E>`|`List<E>`|`Deque<E>`|`Map<K, V>`|
|----|--------|--------|---------|--------|
|ハッシュ表|`HashSet`|`--`|`--`|`HashMap`|
|可変配列|`--`|`ArrayList`|`ArrayDeque`|`--`|
|B ツリー|`TreeSet`|`--`|`--`|`TreeMap`|
|連結リスト|`--`|`LinkedList`|`LinkedList`|`--`|
|ハッシュ表 + 連結リスト|`LinkedHashSet`|`--`|`--`|`LinkedHashMap`|

|保持方法|順次アクセス|直接アクセス|挿入・削除|
|--------|--------|--------|--------|
|ハッシュ表|遅い|非常に速い|非常に速い|
|可変配列|速い (サイズに依存)|速い (サイズに依存)|遅い|
|B ツリー|普通|普通|速い (速度一定)|
|連結リスト|速い (サイズに依存)|遅い|速い (サイズに依存)|

#### コレクションの宣言と実装の選択 : 基本方針

1. 基本となるデータ型を決める: `String`, `Integer`, `BigDecimal`, etc.
2. データ構造を決める: `Set<E>`, `List<E>`, `Deque<E>`, `Map<K, V>`, etc.
3. データ構造の実装を決める

#### 例1 : Stringクラス、順不同、重複なし

1. 基本データ型 → String
2. データ構造 = 順不同、重複なし → Set<String>
3. 実装 = 要件なし → HashSet<String>

(推奨される実装)
* `Set<String> set = new HashSet<String>();`
* `Set<String> set = new HashSet<>();`  // Java SE 7 以降の略記 (推奨)

#### 例2 : int型、順序あり、直接アクセスあり

1. 基本データ型 → Integer (ラッパー)
2. データ構造 = 順序あり、直接アクセスあり → List<Integer>
3. 実装 = 要件なし → ArrayList<Integer>

(推奨される実装)
* `List<Integer> list = new ArrayList<Integer>();`
* `List<Integer> list = new ArrayList<>();`  // Java SE 7 以降の略記 (推奨)

#### 例3 : Integerクラス、順序あり、直接アクセスあり、挿入・削除が多い

1. 基本データ型 → Integer
2. データ構造 = 順序あり、直接アクセスあり → List<Integer>
3. 実装 = 挿入・削除多い → LinkedList<Integer>

(推奨される実装)
* `List<Integer> list = new LinkedList<Integer>();`
* `List<Integer> list = new LinkedList<>();`  // Java SE 7 以降の略記 (推奨)

### 10.2.3. Collection の主なメソッド

`Collection` インタフェースにはいくつものメソッドが定義されていますが、その中からよく使われるものを以下に示します。これらはコレクション (`Map` を除く) で共通して使用できるメソッドであるため、押さえておきましょう。

|メソッド名|説明|
|----------------|----------------|
|`boolean add(E e)`|コレクションに要素を追加する|
|`void clear()`|コレクションの全要素を削除する|
|`boolean contains(Object o)`|要素がコレクションに含まれている場合は `true`、そうでなければ `false` を返す|
|`boolean isEmpty()`|コレクションの要素数が 0 の場合は `true`、そうでなければ `false` を返す|
|`Iterator<E> iterator()`|イテレータを返す (後述)|
|`boolean remove(Object o)`|コレクションの要素を削除する|
|`int size()`|コレクションの要素数を返す|
|`<T> T[] toArray(T[] a)`|コレクションを T 型/クラスの配列に変換する (後述)|

### 10.2.4. イテレータと拡張 for 文

コレクションは、要素にアクセスする標準的な手段として「イテレータ―」と呼ばれる仕組みを提供しています。イテレータとはコレクションの要素を指し示すもので、イテレータを介することによりコレクションの実装に依存しない形で要素にアクセスすることが可能となります。イテレータによるアクセスを順次アクセス (シーケンシャルアクセス) と呼びます。

コレクションのうち使用頻度の高い `List` では、後述のように配列同様に添字を用いてすべての要素にアクセスできるため、イテレータの必要性は感じられないかもしれません。しかし、他のコレクションである `Set` や `Deque` では添字を用いることができません。そのため、どのようなコレクションであっても使用できるイテレータが必要になってきます。

イテレータは `Iterator` クラスのインスタンスであり、中心となるメソッドは以下の 3 種類です。

|メソッド|説明|
|--------|------------|
|`hasNext()`|次の要素がある場合は `true`、そうでなければ `false` (要素数が 0 の場合は常に `false`)|
|`next()`|次の要素を取得する|
|`remove()`|現在の要素 (= 前回 `next()` で取得した要素) を削除する|

以下にイテレータの使用例を示します。イテレータの使用時には、以下の点に注意してください。

1. イテレータ取得時 (`Collection.iterator()` 呼び出し直後) はどの要素も指し示していない。
2. 最初の要素を指し示すには `Iterator.next()` メソッドを呼び出す。
3. コレクションに要素がない場合は `Iterator.next()` メソッドの呼び出しが失敗するため、事前に `Iterator.hasNext()` メソッドでチェックする。
4. `Iterator.remove()` メソッドはあまり使われない。イテレータの状態を常に意識する必要があり、処理が煩雑になりがち。

(例) イテレータの使用例
```
Set<String> set = new HashSet<>();
...
Iterator iter = set.iterator();
while (iter.hasNext()) {
    String s = iterator.next();
    ...
}
```

イテレータを利用した繰り返しを簡潔に表現するため、Java には拡張 for 文と呼ばれる特殊な繰り返しがあります。拡張 for 文は Visual Basic の For-Each-Next 構文、JavaScript の foreach 文に相当します。上記のイテレータの使用例を拡張 for 文で書き直したものを以下に示します。

(例) 拡張 for 文の使用例
```
Set<String> set = new HashSet<>();
...
for (String s : set) {
    ...
}
```

EnhancedForStatement:
    for ( {VariableModifier} UnannType VariableDeclaratorId : Expression ) Statement

EnhancedForStatementNoShortIf:
    for ( {VariableModifier} UnannType VariableDeclaratorId : Expression ) StatementNoShortIf

VariableModifier:
    (one of) 
    Annotation final

VariableDeclaratorId:
    Identifier [Dims]

Dims:
    {Annotation} [ ] {{Annotation} [ ]}


拡張 for 文は以下のような繰り返しを行います。

1. コレクションからイテレータを用いて要素を取り出し、変数に代入する。
2. 文を実行する。変数の参照はこの段階で行う。
3. イテレータが指し示す要素を次に進めて繰り返す。次の要素がない場合は繰り返しを終了する。

拡張 for 文の使用には以下の条件があります。

1. 繰り返し対象は `Iterable` インタフェースの実装 (`Collection` は `Iterable` のスーパーインタフェースのため条件を満たす) または配列。
2. 繰り返し処理では要素の読み取りのみ可能。要素を追加・変更・削除することはできない。

以下に拡張 for 文の書式を示します。

```
for (変数 : 繰り返し対象) 文
```

### 10.2.5. List、Queue、Deque の主なメソッド

`Set` には独自のメソッドは特に用意されていませんが、`List`、`Queue` および `Deque` には独自のメソッドが用意されています。`List` はリスト操作 (直接アクセス、ランダムアクセス)、`Queue` はキュー操作 (FIFO)、`Deque` はキュー操作 (FIFO) およびスタック操作 (LIFO) を提供します。

#### リスト操作 (list のみ)

|メソッド名|説明|
|----------------|----------------|
|`boolean add(int index, E element)`|`index` 番目に要素を挿入する|
|`E get(int index)`|`index` 番目の要素を取得する|
|`int indexOf(Object o)`|要素が最初に見つかった `index` を返す|
|`int lastIndexOf(Object o)`|要素が最後に見つかった `index` を返す|
|`int remove(int index)`|`index` 番目の要素を削除する|
|`E set(int index, E element)`|`index` 番目の要素を置き換える|

#### キュー操作 (Queue および Deque)

|メソッド名|説明|
|----------------|----------------|
|`boolean add(E e)`|要素をキューへ挿入する|
|`boolean offer(E e)`|要素をキューへ挿入する (失敗した場合は非チェック例外 `NoSuchElementException` をスロー)|
|`E poll()`|要素を取得してキューから取り除く (要素がなければ `null` を返す)|
|`E element()`|要素を取得してキューから取り除く (要素がなければ非チェック例外 `NoSuchElementException` をスロー)|
|`E peek()`|要素を取得するがキューには残す (要素がなければ `null` を返す)|
|`E remove()`|要素を取得するがキューには残す (要素がなければ非チェック例外 `NoSuchElementException` をスロー)|

#### スタック操作 (Deque のみ)

|メソッド名|説明|
|----------------|----------------|
|`void push(E e)`|要素をスタックへ挿入する|
|`E pop()`|要素を取得してスタックから取り除く|
|`E peek()`|要素を取得するがスタックには残す|

### 10.2.6. Map の主なメソッド

|メソッド名|説明|
|----------------|----------------|
|`void clear()`|すべてのエントリ (キーと値のペア) を削除する|
|`boolean containsKey(Object key)`|キーが含まれている場合は `true`、そうでない場合は `false` を返す|
|`Set<K> keySet()`|すべてのキーを返す (`Set` インタフェース)|
|`V get(Object key)`|キーに対応する値を取得する (存在しない場合は `null` を返す)|
|`V getOrDefault(Object key, V defaultValue)`|キーに対応する値を取得する (存在しない場合は `defaultValue` を返す)|
|`V put(K key, V value)`|キーに対応する値を設定する (既に存在する場合は置き換える)|
|`V putIfAbsent(K key, V value)`|キーに対応する値を設定する (既に存在する場合は置き換えない)|
|`V remove(Object key)`|キーと対応する値を削除する (存在しない場合は何もしない)|
|`int size()`|エントリ (キーと値のペア) の数を返す|
|`Collection<V> values()`|すべての値を返す (`Collection` インタフェース)|

>【バージョン】`getOrDefault()` および `putIfAbsent()` メソッドは Java SE 8 で追加されたものです。`get()` および `put()` メソッドと比較して、不要な `null` チェックを回避できる (すなわち `NullPointerException` がスローされにくい) 点で優れています。この他にも `Map` には Java SE 8 で追加されたメソッドが多数あります。

### 10.3. Arrays クラスと Collections クラス

### 10.3.1. Arrays クラス

### 10.3.2. Collections クラス

### 10.4. 配列とコレクションの相互変換

### 10.4.1. 配列からコレクションへの変換

`Arrays` クラスの `asList` メソッドで配列を `List` に変換可能です。

`Set` や `Deque` への変換は一旦 `List` に変換してから行います。

# 7.4.2. コレクションから配列への変換

`Collection` インタフェースに `toArray` メソッドが用意されています。

1. `Object toArray()`
2. `T[] toArray(T[] array)`

なお、`Map` を配列に直接変換する方法は用意されていません。キーと値の割り当て方に合わせて独自に実装する必要があります。
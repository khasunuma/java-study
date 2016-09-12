# 5. 制御文

## 5.1. if 文

IfThenStatement:
    if ( Expression ) Statement

- 書式: `if ( expr ) stmt` (`expr`: 論理型、`stmt`: 文)
- 条件式 `expr` の値が `true` のとき `stmt` を実行します。それ以外の場合は何も行いません。
- `stmt` には任意の文を指定できます。
- if 文は、C、Python、JavaScript の if 文、Visual Basic の If-Then 文に相当します。

### 5.1.1. if 文とブロック

例えば、以下のような if 文を想定してみましょう。

```java
if ( expr )
    stmt1  // expr の評価値が false の場合は実行されない
    stmt2  // expr の評価値に関わらず実行される (※意図と異なる！)
```

この時、`expr` が `true` の場合に実行されるのは `stmt1` だけで、`stmt2` は条件式に関わらず必ず実行されます。これは C および JavaScript と共通の仕様で、Python および Visual Basic との大きな違いです。`expr` が `true` の場合に `stmt1` と `stmt2` をともに実行したい場合には、これらをブロックで囲みます。上記の場合、以下のように書き換えると意図した処理となります

```java
if ( expr ) {
    stmt1  // expr の評価値が false の場合は実行されない
    stmt2  // expr の評価値が false の場合は実行されない
}
```

if 文とブロックの組み合わせは、他にも if-else 文、while 文、do 文、for 文、拡張 for 文にも適用できます。いずれも実際のプログラミングでは頻繁に使用されるものなので、是非身につけておいてください。

## 5.2. if-else 文

- 書式: `if ( expr ) stmt1 else stmt2` (`expr`: 論理型、`stmt1`, `stmt2`: 文)
- 条件式 `expr` の値が `true` のとき `stmt1` を、`false` のとき `stmt2` を実行します。
- `stmt1` には if 文 (もしくは if 文が連なる文) 以外を指定します。
- `stmt2` には任意の文を指定できます。

### 5.2.1. if-else 文の stmt1 に if 文を含められない理由

まず、以下の if-else 文を見てください。

```java
// あれ？
if ( expr1 )
    if ( expr2 )
        stmt1
else
    stmt2
```

この文は、以下のような処理を意図して記述したものです。

- `expr1` が `true` の場合:
  - さらに `expr2` が `true` の場合:
    - `stmt1` を実行する
  - さらに `expr2` が `false` の場合:
    - 何もしない
- `expr1` が `false` の場合:
  - `stmt2` を実行する

しかし、実際には以下のように解釈されます。

- `expr1` が `true` の場合:
  - さらに `expr2` が `true` の場合:
    - `stmt1` を実行する
  - さらに `expr2` が `false` の場合:
    - `stmt2` を実行する
- `expr1` が `false` の場合:
  - 何もしない

なぜこのような解釈の相違が発生するのかというと、「`else` は最も近い `if` と結びつく」という Java の構文解釈があるためです。同様の問題は C と JavaScript にも存在しており、Python ではこの曖昧さを回避する目的も合わせてインデント (オフサイド・ルール) によるブロックを採用しています (なお、Visual Basic は Pascal から拝借した Begin-End ブロック構文を採用しているため、このような問題は起こりません)。

上記のコードを意図した通りに動作させるには、以下のようにブロックを使用します。

```java
// これなら意図した通りに動く
if ( expr1 ) {
    if ( expr2 )
        stmt1
} else
    stmt2
```

### 5.2.2. if-else 文と多肢分岐

if-else 文を続けて記述することで、多肢分岐を実現することができます。

```java
if ( !current.isAfter( LocalTime.of(6, 0) ) )
    ...  // 06:00 より前の場合の処理
else if ( !current.isAfter( LocalTime.of(12, 0) ) )
    ...  // 12:00 より前の場合の処理
else if ( !current.isAfter( LocalTime.of(18, 0) ) )
    ...  // 18:00 より前の場合の処理
else
    ...  // それ以外の場合の処理
```

条件式の判定は先頭の if から順番に行います。従って、if-else 文で多肢分岐を行う場合には優先順位の高い条件式ほど上位に記述するのが定石です。

>Java では多肢分岐専用の構文として switch 文を用意しています。switch 文については分岐先に優先順位はありません。

## 5.3. while 文

- 書式: `while ( expr ) stmt` (`expr`: 論理型、`stmt`: 文)
- 条件式 `expr` の値が `true` の間だけ `stmt` を繰り返し実行します。
- while 文が、if-else 文 `if ( expr ) stmt1 else stmt2` の `stmt1` に該当する場合には、`stmt` には if 文以外を指定します。そうでない場合は任意の文を指定できます。

>while 文は、C および JavaScript の while 文と同じで、Python の while 文、Visual Basic の Do While ... Loop 構文 (または While ... Wend 構文) に相当します。

## 5.4. do-while 文

- 書式: `do stmt while ( expr ) ;` (`expr`: 論理型、`stmt`: 文)
- 最初に `stmt` を実行し、その後は条件式 `expr` の値が `true` の間だけ `stmt` を繰り返し実行します。
- `stmt` には任意の文を指定できます。

>do-while 文は、C および JavaScript の do-while 文と同じで、Visual Basic の Do ... While Loop 構文に相当します。Python には相当する構文がありません。

## 5.5. for 文

- 書式: `for ( [init] ; [expr] ; [update] ) stmt` (`init`: 式のリスト/ローカル変数定義式、`expr`: 論理型、`update`: 式のリスト、`stmt`: 文)
- 最初に `init` を評価します。その後は条件式 `expr` の値が `true` の間だけ `stmt` を繰り返し実行します。毎回の `stmt` 実行後に `update` を評価します。
- `init` は式のリストまたはローカル変数宣言式を指定することができます。ローカル変数宣言式を指定した場合は、for 文内でのみ使用可能なローカル変数を宣言することができます。
- for 文は指定回数繰り返しによく使用されます (`init` でループカウンタを宣言、`expr` にでループカウンタの上限を設定、`update` にカウント処理を記述)。
- `init`、`expr`、`update` はいずれも省略が可能で、すべて省略した場合には無限ループになります。
- for 文が、if-else 文 `if ( expr ) stmt1 else stmt2` の `stmt1` に該当する場合には、`stmt` には if 文以外を指定します。そうでない場合は任意の文を指定できます。

実際には、Java における for 文は、以下の while 文に相当します (ほとんどの場合、下記のように書き換えることができます)。

```java
init;
while ( expr ) {
    stmt
    update;
}
```

>for 文は、C および JavaScript の for 文と同じです。Visual Basic の For ... Next 構文に相当しますが、Visual Basic のそれが純粋な指定回数繰り返しであるのに対して、Java は制約が緩い (3 式の選び方でどのようにも制御できる) ことに注意してください。Python の for 文は次節の拡張 for 文に相当するもので、Java の for 文とは異なります。

## 5.6. 拡張 for 文

- 書式: `for ( T var : expr ) stmt` (`T`: データ型、`var`: ローカル変数名、`expr`: `Iterable` 実装のインスタンスまたは配列)
- `expr` が `T` クラスのイテレータを返す `Iterable` 実装か、または `T` 型の配列の場合に限って使用できます。
- `expr` を評価して取得した要素を `var` に代入し、`stmt` を実行する処理を、`expr` のすべての要素に対して順番に実行します。
- 拡張 for 文が、if-else 文 `if ( expr ) stmt1 else stmt2` の `stmt1` に該当する場合には、`stmt` には if 文以外を指定します。そうでない場合は任意の文を指定できます。
- 拡張 for 文の詳細は 10 章で取り上げます。

>拡張 for 文は、JavaScript の foreach 文、Python の for 文、Visual Basic の For Each ... Next 構文に相当します。C には拡張 for 文に相当するものはありません。

## 5.7. break 文と continue 文

break と continue 文は、処理の流れを強制的に変えるための文です。

break 文は繰り返し (while 文/do-while 文、for 文) の中で使用し、繰り返しの中止に使用します。また continue 文は繰り返しの中で使用し、繰り返しを中断して次の繰り返しの実行を指示します。一般的な説明は以上ですが感覚的でないため疑似的なコードを用いて説明します。

```java
// while 文の処理内における break 文の使用例
while ( expr ) {
    ...
    if ( expr_interrupt ) {
        break;
    }
    ...
    // break 文の後、ここにジャンプする
}
```

```java
// for 文の処理内における continue 文の使用例
for ( init ; expr ; update ) {
    ...
    if ( expr_interrupt ) {
        continue;
    }
    ...
    // continue 文の後、ここにジャンプする
}
```

break 文と continue 文は通常、繰り返しの中で if-else 文との組み合わせで使用します。特定の条件を満たしたときに限り break 文または continue 文を実行するようにコーディングします (そうでなければ繰り返しの途中で必ず中断され、それ以降の処理が意味をなさなくなるためです)。

break 文、continue 文とも、実行後に繰り返しの最後までジャンプする点では同じです。 異なるのはその後の流れで、break 文はそのまま繰り返しを終了してしまいますが、continue 文は繰り返しを終了しません。したがって、continue 文では次の繰り返しに移り、さらに for 文においては <増分> の式が実行されることになります。

>break 文および continue 文は C から引き継いだ構文で、JavaScript および Python にも同等の構文があります。Visual Basic では、break 文に対応する Exit Do/Exit For 構文は存在しますが、continue 文に対応する構文は存在しません。

## 5.8. return 文

return 文はメソッドの処理を終了するために使用します。return 文はメソッドの戻り値を伴うため必ず処理の最後に置かなければなりません。メソッド内の処理分岐で 1 つでも return 文に到達できない流れがあるとコンパイルエラーとなります。唯一の例外は戻り値を持たないメソッドです。

```java
// 戻り値の型が void 以外
return <戻り値>;

// 戻り値の型が void (強制的にメソッドを終了する場合)
return;
```

>return 文は C から引き継いだ構文で、JavaScript および Python にも同等の構文があります。Visual Basic には相当する構文として、Exit Function/End Function/Exit Sub/End Sub の各構文が存在します。

## 5.9. switch 文

switch 文は、値に応じて処理を分岐させる場合に使用します。

```java
switch ( expr ) {
case label_expr: stmt stmt ...
case label_expr: stmt stmt ...
default: stmt stmt ...
}
```

`switch` に続く `( )` に判定条件 `expr` を指定します。値には整数、列挙型、文字列が使用できます。switch 文のブロック `{ }` 内にはラベル (`case label_expr` または `default`) と文 (`stmt`) を必要なだけ置くことができます。

switch 文はまず判定条件を評価し、式の値に対応するラベル (`case label_expr`) にジャンプしてそこから処理を実行します。いずれの `case` にも一致しなかった場合には `default` にジャンプして処理を実行します。

switch 文では、`case` にジャンプした後は、他の `case` を含めた以降すべての処理を実行してしまうことに注意します。ほとんどの場合、該当する `case label_expr` に続く処理が終了した後、後続の処理を実行する必要はありません。そのために、処理の最後に `break` 文 (メソッドを終了する場合は `return` 文でも可) を置いて switch 文から抜けるようにします。従って、実質的な switch 文の基本形は下記のような形式になります。

```java
switch ( expr ) {
case 'A':
    ...
    break;  // case 'B' 以降には進まず処理を抜ける
case 'B':
    ...
    break;  // case 'B' 以降には進まず処理を抜ける
case 'C':
    ...
    break;  // default 以降には進まず処理を抜ける
default:
    ...
}
```

>switch 文 C および JavaScript の switch 文と酷似しています (分岐処理後に break 文または return 文で抜ける必要があるところまで同じです)。Visual Basic の Select-Case 構文に相当します (Visual Basic の場合は分岐処理後に特別な処理は必要ありません)。Python には Java の switch 文に相当する構文はなく、if-else 文による多肢分岐にて対応することになります。

>【バージョン】switch 文の判定条件の値には当初、整数型のみが指定可能でした (これは C と完全に同一の仕様)。その後 Java SE 5.0 で列挙型が、Java SE 7 で文字列がそれぞれ使用できるように拡張され、現在では JavaScript や Visual Basic とほぼ同等の分岐条件が記述できるようになっています。

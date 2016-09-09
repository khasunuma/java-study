# 5. 制御文

Statement:
    StatementWithoutTrailingSubstatement 
    LabeledStatement 
    IfThenStatement 
    IfThenElseStatement 
    WhileStatement 
    ForStatement

StatementNoShortIf:
    StatementWithoutTrailingSubstatement 
    LabeledStatementNoShortIf 
    IfThenElseStatementNoShortIf 
    WhileStatementNoShortIf 
    ForStatementNoShortIf

StatementWithoutTrailingSubstatement:
    Block 
    EmptyStatement 
    ExpressionStatement 
    AssertStatement 
    SwitchStatement 
    DoStatement 
    BreakStatement 
    ContinueStatement 
    ReturnStatement 
    SynchronizedStatement 
    ThrowStatement 
    TryStatement

## if 文

IfThenStatement:
    if ( Expression ) Statement

- 書式: `if ( expr ) stmt` (`expr`: 論理型、`stmt`: 任意の文)
- 条件式 `expr` の値が `true` のとき `stmt` を実行します。それ以外の場合は何も行いません。
- `stmt` には任意の文を指定できます。

## if-else 文

- 書式: `if ( expr ) stmt1 else stmt2` (`expr`: 論理型、`stmt1`: if 文を含まない文、`stmt2`: 任意の文)
- 条件式 `expr` の値が `true` のとき `stmt1` を、`false` のとき `stmt2` を実行します。
- `stmt1` は if 文であったり、while 文/for 文と if 文の組み合わせであってはいけません (if-else 文や、while 文/for 文と if-else 文の組み合わせは問題ありません)。
- `stmt2` には任意の文を指定できます。

## while 文

- 書式: `while ( expr ) stmt` (`expr`: 論理型、`stmt`: 文)
- 条件式 `expr` の値が `true` の間だけ `stmt` を繰り返し実行します。
- `stmt` はその while 文が if-else 文 `if ( expr ) stmt1 else stmt2` の `stmt1` に該当する場合には、if 文以外の文としなければなりません。それ以外の場合は任意の文を指定できます。

## do 文

- 書式: `do stmt while ( expr ) ;` (`expr`: 論理型、`stmt`: 文)
- 最初に `stmt` を実行し、その後は条件式 `expr` の値が `true` の間だけ `stmt` を繰り返し実行します。
- `stmt` には任意の文を指定できます (while 文と異なり条件はありません)。

## for 文

- 書式: `for ( [init] ; [expr] ; [update] ) stmt` (`init`: 式のリストまたはローカル変数定義式、`expr`: 論理型、`update`: 式のリスト、`stmt`: 文)
- 最初に `init` を評価します。その後は条件式 `expr` の値が `true` の間だけ `stmt` を繰り返し実行します。毎回の `stmt` 実行後に `update` を評価します。
- `init` は式のリストまたはローカル変数宣言式を指定することができます。ローカル変数宣言式を指定した場合は、for 文内でのみ使用可能なローカル変数を宣言することができます。
- for 文は指定回数繰り返しによく使用されます (`init` でループカウンタを宣言、`expr` でループカウンタの上限を指定、`update` でカウント処理を指定)。
- `init`、`expr`、`update` はいずれも省略が可能で、すべて省略した場合には無限ループになります。
- `stmt` はその for 文が if-else 文 `if ( expr ) stmt1 else stmt2` の `stmt1` に該当する場合には、if 文以外の文としなければなりません。それ以外の場合は任意の文を指定できます。

## 拡張 for 文

- 書式: `for ( type var : expr ) stmt` (`type`: データ型、`var`: ローカル変数名、`expr`: `Iterable` 実装のインスタンスまたは配列)
- `stmt` はその拡張 for 文が if-else 文 `if ( expr ) stmt1 else stmt2` の `stmt1` に該当する場合には、if 文以外の文としなければなりません。それ以外の場合は任意の文を指定できます。
- 詳細は 10 章で取り上げます。


## 5.2. if-else 文

if-else 文は条件式により処理を分岐します。C、JavaScript の if-else 文と同等で、Python の if-elif-else 構文、Visual Basic の If-Then-Else 構文に相当します。if-else 文には以下の 2 つの書式があります。

1. `if (条件式) 文1 else 文2`
2. `if (条件式) 文1`

条件式の値は `boolean` 型で、`true` の場合には 文1 を、`false` の場合には 文2　をそれぞれ実行します。書式 2 は書式 1 の簡略表記で、文 2 を実行しない場合には 'else 文2' ごと省略できることを表しています。

ここで 文1、文2 は、具体的な処理を行う式文 (式; で表される文) の他に、if-else 文などのプログラムの流れを制御する文を指定することもできます。例えば後者を利用して、else-if の連鎖により複数の条件に合わせて処理を分岐することができます。

```
if (!current.isAfter(LocalTime.of(6, 0)))
    ...  // 06:00 より前の場合の処理
else if (!current.isAfter(LocalTime.of(12, 0)))
    ...  // 12:00 より前の場合の処理
else if (!current.isAfter(LocalTime.of(18, 0)))
    ...  // 18:00 より前の場合の処理
else
    ...  // それ以外の場合の処理
```

else-if の連鎖を用いた場合、条件式の判定は先頭の if から順番に行います。従って、優先順位の高い条件式ほど上位に記述するのが定石です。

## 5.3. 文とブロック

例えば、以下のような if 文を想定してみましょう。

```
if (条件式)
    文1  // 条件式によって実行されない場合がある
    文2  // 条件式にかかわらず実行される (※意図と異なる！)
```

この時、条件式によって実行可否が決まるのは 文1 だけで、文 2 は条件式に関わらず必ず実行されます (これは C および JavaScript と共通の仕様で、Python および Visual Basic との大きな違いです)。条件式によって 文1、文2 を続けて実行したい場合には、これらをブロック { ... } で囲みます。上記の場合、以下のように書き換えると意図した処理となります

```
if (条件式) {
    文1 // 条件式によって実行されない場合がある
    文2 // 条件式によって実行されない場合がある
}
```

ブロックは { ... } で囲まれた範囲をあたかも 1 つの文として扱うことができる構文です。ここで例示した if-else 文の他、繰り返しを表す while 文や for 文でも頻繁に使用されるものなので、是非身につけておいてください。

>if-else 文、while 文、for 文などの処理は、無条件にブロックとしてしまう方が安全です (コーディング規約でそのように決められている場合もあります)。ブロックを用いることによりソースコードの可読性が低下することもほとんどありません。ただし、海外のプログラマが書いたソースコードではブロックを使用していないものも多くため気を付けましょう。

ブロック内での変数宣言について、Java では特殊な扱いがなされます (C++ と同様の振る舞いです)。

* ブロック内では、そのブロックの外側で既に宣言されている変数名と同じ変数名は使用できません。
* ブロック内で宣言した変数は、宣言したブロック内でのみ有効です。言い換えると、異なるブロックであれば同じ変数名を使用することができます。


## 5.5. for 文

for 文は指定回数の繰り返し処理に適した構文です。C および JavaScript の for 文と同等で、Visual Basic の For-Next 構文と類似しています。書式は以下の通りです (if-else 文同様、ブロックを用いることもできます)。

* `for (式 1; 式 2; 式 3) 文`

ただし、

* 式 1 : 初期条件
* 式 2 : 繰り返し条件
* 式 3 : 増分

実際には、Java における for 文は、以下の while 文と等価です (C/C++ 言語と同じ)。Visual Basic の For-Next ループと異なり、純粋な指定回数繰り返しではないことに注意してください。

```
式1;
while (式 2) {
    文
    式 3;
}
```

for 文に類似するものとして拡張 for 文 (Visual Basic の For Each-Next 構文に相当) があります。Python の for 文は Java の拡張 for 文に相当します。拡張 for 文については後述します。

## 5.6. break/continue/return 文

BreakStatement:
    break [Identifier] ;

ContinueStatement:
    continue [Identifier] ;

ReturnStatement:
    return [Expression] ;

break/continue/return 文は、処理の流れを強制的に変えるための文です。

break 文は繰り返し (while 文/do-while 文、for 文) の中で使用し、繰り返しの中止に使用します。また continue 文は繰り返しの中で使用し、繰り返しを中断して次の繰り返しの実行を指示します。一般的な説明は以上ですが感覚的でないため疑似的なコードを用いて説明します。

(例) while 文の処理内における break 文
```
while (<繰り返し条件>) {
    ...
    if (<中断条件>) {
        break;
    }
    ...
    // break 文の後、ここにジャンプする
}
```

(例) for 文の処理内における continue 文
```
for (<初期条件>; <繰り返し条件>; <増分>) {
    ...
    if (<中断条件>) {
        continue;
    }
    ...
    // continue 文の後、ここにジャンプする
}
```

break 文と continue 文は通常、繰り返しの中で if-else 文との組み合わせで使用します。特定の条件を満たしたときに限り break 文または continue 文を実行するようにコーディングします (そうでなければ繰り返しの途中で必ず中断され、それ以降の処理が意味をなさなくなるためです)。

break 文、continue 文とも、実行後に繰り返しの最後までジャンプする点では同じです。 異なるのはその後の流れで、break 文はそのまま繰り返しを終了してしまいますが、continue 文は繰り返しを終了しません。したがって、continue 文では次の繰り返しに移り、さらに for 文においては <増分> の式が実行されることになります。

return 文はメソッドの処理を終了するために使用します。return 文はメソッドの戻り値を伴うため必ず処理の最後に置かなければなりません。メソッド内の処理分岐で 1 つでも return 文に到達できない流れがあるとコンパイルエラーとなります。唯一の例外は戻り値を持たないメソッドです。

```
return <戻り値>;  // 戻り値の型が void 以外
return;  // 戻り値の型が void (強制的にメソッドを終了する場合)
```

break 文、continue 文、return 文は C から引き継いだ構文で、JavaScript および Python にも同等の構文があります。Visual Basic では、break 文に対応する Exit Do/Exit For 構文、return 文に対応する Exit Function/End Function は存在しますが、continue 文に対応する構文は存在しません。

## 5.7. switch 文

SwitchStatement:
    switch ( Expression ) SwitchBlock

SwitchBlock:
    { {SwitchBlockStatementGroup} {SwitchLabel} }

SwitchBlockStatementGroup:
    SwitchLabels BlockStatements

SwitchLabels:
    SwitchLabel {SwitchLabel}

SwitchLabel:
    case ConstantExpression : 
    case EnumConstantName : 
    default :

EnumConstantName:
    Identifier

switch 文は、値に応じて処理を分岐させる場合に使用します。C および JavaScript の switch 文をベースとしており、Visual Basic の Select-Case 構文に相当します。Python には Java の switch 文に相当する構文はありません。

```
switch (値) {
case ラベル: 文
...
default: 文
}
```

`switch` に続く `( )` に判定条件となる値 (変数) を指定します。値には整数、列挙型、文字列が使用できます。`{ }` 内には `case ラベル:` を複数置くことができ、値に一致するラベルの `case` にジャンプしてそこから処理を実行します。いずれの `case` にも一致しなかった場合には `default` にジャンプして処理を実行します。

switch 文では、`case` にジャンプした後は、他の `case` を含めた以降すべての処理を実行してしまうことに注意します (これは C/C++ から引き継いだ仕様で、Visual Basic の Select Case 構文との相違点です)。ほとんどの場合、該当する `case` の処理が終了した後に続く `case`/`default` の処理を実行する必要はありません。そのために、処理の最後に `break` 文 (メソッドを終了する場合は `return` 文でも可) を置いて switch 文から抜けるようにします。従って、実質的な switch 文の基本形は下記のような形式になります。

```
switch (x) {
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

>【バージョン】switch 文の判定条件の値には当初、整数のみが指定可能で、Java SE 5.0 以降で列挙型が、Java SE 7 以降で文字列がそれぞれ使用できるように拡張されています。

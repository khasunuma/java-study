# 3. 文

文には、いくつかの種類があります。

## 3. 1. 式の文

変数、リテラル（文字列・整数・浮動小数点数・論理値）、算術式、論理式、代入式、その他の式、およびこれらの組み合わせのみで構成される文を式の文と言います。式文の末尾には ; (セミコロン) を付加します。

### 3.1.1. 文字列リテラル

前後を " " で囲んだ文字の列を文字列リテラル (定数) と呼びます。文字列リテラルは必ず String クラスのインスタンスになります。文字列リテラル内では " と \ を直接使用することはできず、それぞれ \" と \\ に置き換えます。文字列リテラルには \n : 改行、\t : タブ などの制御文字を含めることもできます (制御文字は必ず \ で始まります)。
         
文字列 (リテラルを含む) を + で繋ぐと、文字列を連結することができます。連結後の文字列は文字列リテラルとなり、String クラスのインスタンスとなります。

(例) `"Good night, " + "everybody" + "!" => "Good night, everybody!"`

### 3.1.2. 整数リテラル

整数リテラルは、10 進数、16 進数、8 進数、2 進数で以下のように表現します。

* 0, 10, 16 // 10 進数; 0-9、0 以外は 0 から始まらない
* 0x0, 0xa, 0x10 // 16 進数; 0-9,a-f(A-F)、先頭に 0x または 0X を付ける
* 00, 012, 020 // 8 進数; 0-8、先頭に 0 を付ける
* 0b0, 0b1010, 0b10000 // 2 進数; 0 or 1、先頭に 0b または 0B を付ける

数値リテラルは通常 int 型になります (int はクラスではなく、プリミティブ型と呼ばれます)。int 型のサイズに収まらない数値リテラルは long 型にする必要があり、その場合にはリテラルの末尾に L を付けます (例: 0L, 10L, 16L)。

### 3.1.3. 浮動小数点数リテラル

数値リテラルのうち小数点 . を含むものは浮動小数点リテラルと呼ばれます。小数点以下が 0 の場合でも .0 を付加することで浮動小数点リテラルになります (例: 0, 16 は整数リテラル、0.0, 16.0 は浮動小数点リテラル)。

Java には float (単精度浮動小数点型) と double (倍精度浮動小数点型) と 2 種類の浮動小数点型が用意されており、それぞれの型の浮動小数点型の表記が決まっています。

* 末尾にF (例: 0.0F, 1.5F) => float 型
* 末尾にD (例: 0.0D, 1.5D) => double 型
* 末尾なし (例: 0.0, 1.5) => double 型

#### [参考] float 型と double 型のどちらを選択すべきか？

特別な理由がない限り double 型を選択してください。

浮動小数点型が float 型と double 型に分かれているのは歴史的経緯によるものです。現在は double 型を用いるのが一般的です。

>1990 年代ごろまでの一般的なコンピュータは CPU に浮動小数点演算用のユニットが含まれていませんでした。そのため、多くの CPU は整数演算用ユニットを利用して浮動小数点演算を行っており、消費リソースは少ないが演算桁数も少ない float 型と、演算桁数は十分だが消費リソースも大きい double 型を使い分けていました。現在の CPU はすべて浮動小数点演算専用のユニットを内蔵しており、設計の簡略化のため double 型の演算のみサポートし、float 型の演算は CPU 内部で double 型に変換した上で実行しています。従って、データ型変換のロスを考慮すると始めからすべて double 型で演算をした方が効率がよいのです。

### 3.1.4. 論理値リテラル

`true` は「真」の論理値リテラル、`false` は「偽」の論理値リテラルで、ともに boolean 型となります。

### 3.1.5. 算術式

数値同士について、算術演算 (四則演算) がサポートされています。異なる数値型同士の演算の場合、より大きいデータ型 (int < long < double) に合わせられます。

* (加算) x ＋ y :  x + y
* (減算) x － y :  x - y
* (乗算) x × y : x * y
* (除算) x ÷ y : x / y
* (剰余) x mod y : x % y

また、インクリメント (+1)・デクリメント (-1) 演算が存在します。

* インクリメント (前置) : ++x
* インクリメント (後置) : x++
* デクリメント (前置) : --x
* デクリメント (後置) : x--

前置と後置では式の評価タイミングが異なり、前置は評価前に+1/-1演算が行われ、後置は評価後に+1/-1演算が行われます。

(例) x = 3, y = 2 の時

* ++x * y の結果は、式の値 = 8、x = 4、y = 2 (計算前に+1)
* x++ * y の結果は、式の値 = 6、x = 4、y = 2 (計算後に+1)

### 3.1.6. 論理式

数値同士について比較演算がサポートされています。演算結果は boolean 型になります。

* (未満) x ＜ y : x < y
* (以下) x ≦ y : x <= y
* (等しい) x ＝ y : x == y
* (等しくない) x ≠ y : x != y
* (以上) x ≧ y : x >= y
* (より大きい) x ＞ y : x > y

比較演算には && (AND)、|| (OR)、! (NOT) を使用することができ、基本的な等式・不等式を表現することができます。

また、論理型には独自の AND、OR、NOT 演算が存在しており、組み合わせて使うことができます。

* AND : x & y
* OR : x | y
* NOT : !x

#### [参考] 条件演算子 (三項演算子)

`x ? y : z` で論理式 x が真のとき y、偽のとき z を値とする「条件演算子 (三項演算子)」が用意されています。ほとんどの場合 `if (x) y; else z;` と同じですが、式は置けるが文は置けない個所で条件分岐をしたい場合に用いられます。

条件演算子は使い方次第でソースコードの可読性を向上させる場合も悪化させる場合もあるため、注意が必要です。

### 3.1.7. 変数と代入式

式の値に名前を付けたものを変数と言います。変数名にはフィールド名と同じキャメルケース (先頭小文字) を用いるのが慣習ですが、フィールド名と比較して短い名前が付けられる傾向にあります。

* <変数型> <変数名>;
* <変数型> <変数名> = <初期値>;  // 変数の初期化を伴う場合
* final <変数型> <変数名> = <初期値>;  // 変数を初期化し、変更できないようにする場合 (定数)

変数に値を設定する式を代入式と言います。書式は以下の通りです。

* <変数名> = <式>
* <変数型> <変数名> = <式>  // 変数の初期化を伴う場合

式の値が boolean、int、long、double など (プリミティブ型といいます) の場合は、式の値が変数にコピーされます。式の値がクラスのインスタンスの場合は、そのインスタンスへの参照が変数にコピーされます。final が付加された変数を除き、代入式は何度でも (その変数自身に対しても) 行うことができます。

>代入式の再適用は、変数に設定された値が刻一刻と変化するような状態を表すために用いられます。変数型が同じだからと言って意味合いが全く異なる値を変数に再設定することは、処理の流れを読みづらくするだけなので避けてください。1970 年代のプログラミング言語では変数名に利用できる文字の種類や長さに制限があり、宣言できる変数の個数に限界があったため変数を再利用するような手段も採られていましたが、Java では変数名に制約がないため変数を再利用するメリットが全くありません。

変数はメソッド内の任意の場所で宣言することができ、宣言した箇所以降メソッドの終了まで有効です。メソッドの引数は初期値が呼び出し元メソッドで設定された変数とみなすことができます。変数はメソッドごとに独立しているため、異なるメソッドで同じ変数名を使用することは問題ありません。

### 3.1.8. その他の式

new 演算子でクラスのインスタンスを生成した結果 (例: `new BigDecimal(150)`) や、. 演算子でクラスのフィールドやメソッドを参照した結果 (例: `s.toUpperCase()`) も、式として扱われます。

## 3.2. if-else 文

if-else 文は条件式により処理を分岐します。if-else 文には以下の 2 つの書式があります。

1. if (条件式) 文1 else 文2
2. if (条件式) 文1

条件式の値は boolean 型で、true の場合には 文1 を、false の場合には 文2　をそれぞれ実行します。書式 2 は書式 1 の簡略表記で、文 2 を実行しない場合には 'else 文2' ごと省略できることを表しています。

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

## 3.3. 文とブロック

例えば、以下のような if 文を想定してみましょう。

```
if (条件式)
    文1  // 条件式によって実行されない場合がある
    文2  // 条件式にかかわらず実行される (※意図と異なる！)
```

この時、条件式によって実行可否が決まるのは 文1 だけで、文 2 は条件式に関わらず必ず実行されます (Python との大きな違い)。条件式によって 文1、文2 を続けて実行したい場合には、これらをブロック { ... } で囲みます。上記の場合、以下のように書き換えると意図した処理となります

```
if (条件式) {
    文1 // 条件式によって実行されない場合がある
    文2 // 条件式によって実行されない場合がある
}
```

ブロックは { ... } で囲まれた範囲をあたかも 1 つの文として扱うことができる構文です。ここで例示した if-else 文の他、繰り返しを表す while 文や for 文でも頻繁に使用されるものなので、是非身につけておいてください。

>if-else 文、while 文、for 文などの処理は、無条件にブロックとしてしまう方が安全です (コーディング規約でそのように決められている場合もあります)。ブロックを用いることによりソースコードの可読性が低下することもほとんどありません。ただし、海外のプログラマが書いたソースコードではブロックを使用していないものも多く見られます。

ブロック内での変数宣言について、Java では特殊な扱いがなされます (C++ と同様の振る舞いです)。

* ブロック内では、そのブロックの外側で既に宣言されている変数名と同じ変数名は使用できません。
* ブロック内で宣言した変数は、宣言したブロック内でのみ有効です。言い換えると、異なるブロックであれば同じ変数名を使用することができます。

## 3.4. while 文、do-while 文

while 文、do-while 文は条件式により繰り返し処理を行います。書式は以下の通りです (if-else 文同様、ブロックを用いることもできます)。

* while (条件式) 文
* do 文 while (条件式);

## 3.5. for 文

for 文は指定回数の繰り返し処理に適した構文です。書式は以下の通りです (if-else 文同様、ブロックを用いることもできます)。

* for (式 1; 式 2; 式 3) 文

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

for 文に類似するものとして拡張 for 文 (Visual Basic の For Each-Next 構文に相当) があります。拡張 for 文については後述します。

## 3.6. break 文、continue 文、return 文

break 文、continue 文、return 文は、処理の流れを強制的に変えるための文です。

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

break 文、continue 文、return 文は C/C++ から引き継いだ構文です。Visual Basic では、break 文に対応する Exit Do/Exit For 構文、return 文に対応する Exit Function/End Function は存在しますが、continue 文に対応する構文は存在しません。一方で、C/C++ の goto 文や Visual Basic の GoTo 構文に対応する Java の構文は存在しません (近い構文としてラベル付き break 文が存在しますが、ほとんど使用されないため割愛します)。 

## 3.7. switch 文

switch 文は、値に応じて処理を分岐させる場合に使用します。C/C++ の switch 文や、Visual Basic の Select Case 構文に相当します。

```
switch (値) {
case ラベル: 文
...
default: 文
}
```

switch に続く ( ) に判定条件となる値 (変数) を指定します。値には整数、列挙型、文字列が使用できます。{ } 内には case ラベル: を複数置くことができ、値に一致するラベルの case にジャンプしてそこから処理を実行します。いずれの case にも一致しなかった場合には default にジャンプして処理を実行します。

switch 文では、case にジャンプした後は、他の case 文を含めた以降すべての処理を実行してしまうことに注意します (これは C/C++ から引き継いだ仕様で、Visual Basic の Select Case 構文との相違点です)。ほとんどの場合、該当する case の処理が終了した後に続く case/default の処理を実行する必要はありません。そのために、処理の最後に break 文 (メソッドを終了する場合は return 文でも可) を置いて switch 文から抜けるようにします。

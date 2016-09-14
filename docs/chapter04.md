# 4. 文と式

Java における処理の記述を文 (Statement) といいます。文の一覧を下表に示します。

|文の分類|文|解説する章|
|--------|--------|----|
|単純な文|式文、空文|4 章|
|分岐・繰り返し|if 文、if-else 文、while 文、do-while 文、for 文、switch 文|5 章|
| |拡張 for 文|5 章、10 章|
|ジャンプ|break 文、continue 文、return 文|5 章|
|例外処理|throw 文|6 章|
| |try 文|6 章|
| |try-with-resources 文|6 章、11 章|
|同期化|synchronized 文|9 章|
|文のグルーピング|ブロック|4 章|

- if-else 文、while 文および for 文は、if 文との組み合わせで注意事項があります。詳細は 5 章にて取り上げます。
- 上記の他にラベル付き文、assert 文が用意されていますが、特殊な用途で使用頻度も高くないためここでは割愛します。

この章では、式文、空文、ブロックの 3 種類と、式文のもとになる「式」について解説します。

## 4.1. 式文

式文は、文字通り式を実行 (評価) する文です。Java では以下の式の末尾に `;` (セミコロン) を置いたものが式文となります。それぞれの式については後述しますが、ここでは (1) 式を実行する文を式文という、(2) 式文に使える式には一応制限が設けられている (どのような式でも式文にできるわけではない) ことを押さえておいてください。

- 代入演算式
- 前置インクリメント式
- 前置デクリメント式
- 後置インクリメント式
- 後置デクリメント式
- メソッド呼び出し式
- インスタンス生成式

>実際のプログラミングで多用することになる四則演算の式は、単独では式文にすることができません。四則演算の式は代入演算式やメソッド呼び出し式と組み合わせて使うことになります。式文の制約はプログラミング言語によって異なりますが、C や JavaScript は制約が緩く、Python や Visual Basic は制約が厳しい傾向にあります。

## 4.2. 空文

空文 (EmptyStatement) は、何も処理をしない文です。`;` (セミコロン) だけで表します。

>【対比】 空文は後述の while 文、do 文、for 文においてダミー処理が必要になった時に使用します。C および JavaScript には空文は存在しており記法・用途も Java と同様ですが、Python および Visual Basic には空文の概念がないため注意が必要です。

## 4.3. ブロック

文を `{ }` で囲んだものをブロック (Block) といいます。ブロックの中には以下の 4 種類を記述することができます。

- ローカル変数宣言文 (下記参照)
- クラス定義
- 文
- ブロック

ローカル変数宣言文は、ブロック内でのみ有効 (ローカル) な変数 (式の値に名前を付けたもの) である「ローカル変数」を宣言するための文で、以下の書式となります。

```
[final] データ型 (クラスまたはプリミティブ型) ローカル変数名 ;
```

(例) リテラルの解説とあわせて参照のこと

```java
double doubleValue;  // データ型: double、ローカル変数名: doubleValue
String str;  // データ型: String、ローカル変数名: str
long longValue = 10L;  // データ型: long、ローカル変数名: intValue、初期値: 10
final int round = 0;  // データ型: int、ローカル変数名: round、初期値: 0、変更不可
```

ブロックは、メソッド本体の処理記述そのものです。また、後述の if 文、if-else 文、while 文、do 文、for 文との組み合わせで多用されます。

>【対比】  ブロックは C や JavaScript に存在しており記法・用途も Java と同様です。Python はブロックの代わりにインデント (オフサイド・ルール) を用います。Visual Basic のブロックは各構文と一体化しており、Java のように単独で用いることはできません。

ローカル変数は、宣言されたメソッド内でのみ有効です。メソッドの仮引数もローカル変数と同じものとみなされます。同じブロック内でローカル変数名を重複させることはできませんが、異なるブロックであれば重複は許されます。ただし、ブロックをネストさせている場合には、内側のブロック自体が外側のブロックの一部とみなされるため、ローカル変数名の重複は許されません。

>ローカル変数に対して、フィールドはインスタンスの範囲で有効な変数と言い換えることができます。フィールドとローカル変数は同じ変数名を使用することができますが、同時に使用する場合にはフィールド名の先頭に `this .` を付加してインスタンスに属する = フィールドであることを明示する必要があります。

## 4.4. リテラル

### 4.4.1. 整数リテラル (IntegerLiteral)

整数リテラルは 10 進数、16 進数、8 進数および 2 進数の各表記があります。

#### 4.4.1.1. 10 進数整数リテラル

- 使用できる数字は `0` `1` `2` `3` `4` `5` `6` `7` `8` `9` です。
- `0`、または `1` `2` `3` `4` `5` `6` `7` `8` `9` から始まる 1 つ以上の数字で構成されます。
- 原則として `int` 型の値となりますが、末尾に `l` または `L` を付加することで `long` 型の値となります。
- 数字と数字の間に `_` (アンダースコア) を含めて、桁区切りとして用いることができます。

#### 4.4.1.2. 16 進数整数リテラル

- 使用できる数字は `0` `1` `2` `3` `4` `5` `6` `7` `8` `9` `a` `b` `c` `d` `e` `f` `A` `B` `C` `D` `E` `F` です。
- 先頭 `0x` または `0X` で始まり、1 つ以上の数字で構成されます。
- 原則として `int` 型の値となりますが、末尾に `l` または `L` を付加することで `long` 型の値となります。
- 数字と数字の間に `_` (アンダースコア) を含めて、桁区切りとして用いることができます。

#### 4.4.1.3. 8 進数整数リテラル

- 使用できる数字は `0` `1` `2` `3` `4` `5` `6` `7` です。
- 先頭 `0` で始まり、1 つ以上の数字で構成されます。
- 原則として `int` 型の値となりますが、末尾に `l` または `L` を付加することで `long` 型の値となります。
- 数字と数字の間に `_` (アンダースコア) を含めて、桁区切りとして用いることができます。

#### 4.4.1.4. 2 進数整数リテラル

- 使用できる数字は `0` `1` です。
- 先頭 `0b` または `0B` で始まり、1 つ以上の数字で構成されます。
- 原則として `int` 型の値となりますが、末尾に `l` または `L` を付加することで `long` 型の値となります。
- 数字と数字の間に `_` (アンダースコア) を含めて、桁区切りとして用いることができます。

>【バージョン】 `_` (アンダースコア) を区切り文字として使用できる仕様、および 2 進数リテラルは Java SE 7 以降で導入されたものです。

>【対比】  Java とその他の言語 (C、Python、JavaScript、Visual Basic) との整数リテラルの対照表を以下に示します。
>|言語|10 進数|16 進数|8 進数|2 進数|桁区切り|サフィックス|
>|----|----|----|----|----|----|----|
>|Java|そのまま|先頭 `0x` `0X`|先頭 `0`|先頭 `0b` `0B`|`_` (アンダースコア)|`l` `L` で `long` 型|
>|C|そのまま|先頭 `0x` `0X`|先頭 `0`|N/A|N/A|`l` `L` で `long` 型|
>|Python|そのまま|先頭 `0x` `0X`|先頭 `0`|先頭 `0b` `0B`|N/A|`l` `L` で `long` 型|
>|JavaScript|そのまま|先頭 `0x` `0X`|N/A|N/A|N/A|N/A|
>|Visual Basic|そのまま|先頭 `&h` `&H`|先頭 `&o` `&O`|N/A|N/A|`%` で `Integer` 型、`&` で `Long` 型|

### 4.4.2. 浮動小数点数リテラル (FloatingPointLiteral)

浮動小数点数リテラルは 10 進数および 16 進数の表記があります。

#### 4.4.2.1. 10 進数浮動小数点数リテラル

10 進数整数リテラルと同じ数字を使用し、かつ以下のいずれかの条件を満たす場合に、10 進数浮動小数点リテラルとなります。

- `.` (小数点) が存在する
- `e` `E` で始まる指数部を含む

10 進数浮動小数点数リテラルには「小数点表記」と「指数表記」の 2 種類が存在します。

小数点表記は、例えば数式 0.015 のように小数点を含む形で表す記法です。小数点表記は事務処理計算で多用される記法です。

指数表記は、例えば数式 1.5 × 10<sup>-2</sup> を 1.5e-2 とする記法で、この時の 1.5 を仮数部、e2 を指数部といいます。仮数部は整数リテラルまたは小数点表記の浮動小数点リテラルと同じ形式となります。指数部は先頭が `e` または `E` で始まり、それに指数 (正または負の整数、この例では指数は -2) が続きます。例えば 2e3、3.0e+2、0.5e-1 はいずれも指数表記となります (それぞれ 2 × 10<sup>3</sup>、3.0 × 10<sup>2</sup>、0.5 × 10<sup>-1</sup> を意味します)。指数表記は科学技術計算で多用される記法です。

10 進数浮動小数点数リテラルの型は、末尾に `f` `F` が付加された場合は `float` 型、`d` `D` が付加された場合は `double` 型となります。省略時には `double` 型とみなされます。

>【対比】  C、Python、JavaScript および Visual Basic にも 10 進数浮動小数点数リテラルが存在しており、記法も Java と同じです。

#### 4.4.2.2. 16 進数浮動小数点数リテラル

先頭 `0x` `0X` で始まり、かつ以下のいずれかの条件を満たす場合に、16 進数の浮動小数点リテラルとなります (数字として 16 進数整数リテラルと同じものを使用できるようになります)。

- `.` (小数点) が存在する
- `p` `P` で始まる指数部を含む

16 進数浮動小数点数リテラルには「小数点表記」と「指数表記」の 2 種類が存在します。

小数点表記は、例えば数式 0x1F.7FF のように小数点を含む形で表す記法です。これは 10 進数浮動小数点数リテラルを 16 進数に拡張したもので、先頭の `0x` `0X` で 10 進数浮動小数点数リテラルと区別されます。

指数表記は、例えば数式 0x1.ff × 2<sup>-3</sup> を 0x1.ffp-3 とする記法で、この時の 1.ff を仮数部、p-3 を指数部といいます。こちらも 10 進数浮動小数点数リテラルを 16 進数に拡張したもので、先頭の `0x` `0X` および指数部の先頭 `p` `P` で　10 進数浮動小数点数リテラルと区別されます。

16 進数浮動小数点数リテラルの型は、末尾に `f` `F` が付加された場合は `float` 型、`d` `D` が付加された場合は `double` 型となります。省略時には `double` 型とみなされます。

>【対比】  C には Java と同じ記法の 16 進数浮動小数点数リテラルが存在します (C99 規格以降)。Python、JavaScript、Visual Basic には 16 進数浮動小数点数リテラルは存在しません。

### 4.4.3. 文字リテラルと文字列リテラル

#### 4.4.3.1. 文字リテラル (CharacterLiteral)

文字リテラルは 1 文字を表すリテラルで、文字、Unicode エスケープ (Unicode の 16 進数文字コード 4 桁 *xxxx* を \u*xxxx* 形式で表す記法) またはエスケープ・シーケンスを `' '` (シングルクォート) で囲んで表します。ただし、`'` (シングルクォート) および `\` (バックスラッシュ) は文字として表すことはできず、代わりにエスケープ・シーケンスを用います。文字リテラルは `char` 型の値となります。

|Esc |意味|
|:--:|----------------------------------|
|`\b`|backspace BS, Unicode \u0008|
|`\t`|horizontal tab HT, Unicode \u0009| 
|`\n`|linefeed LF, Unicode \u000a|
|`\f`|form feed FF, Unicode \u000c|
|`\r`|carriage return CR, Unicode \u000d| 
|`\"`|double quote ", Unicode \u0022| 
|`\'`|single quote ', Unicode \u0027 
|`\\`|backslash \, Unicode \u005c| 

#### 4.4.3.2. 文字列リテラル (StringLiteral)

文字列リテラルは 0 文字以上の文字列を表すリテラルで、文字、Unicode エスケープまたはエスケープ・シーケンスの列を `" "` (ダブルクォート) で囲んで表します。ただし、`'` (ダブルクォート) および `\` (バックスラッシュ) は文字として表すことはできず、代わりにエスケープ・シーケンスを用います。文字列リテラルは `String` 型への参照となります。

>【対比】  C には Java と類似した記法の文字リテラルおよび文字列リテラルが存在します (Unicode エスケープが使用できるのは C99 規格以降)。Python および JavaScript には文字列リテラルのみが存在し、囲み文字は `" "` (ダブルクォート) と `' '` (シングルクォート) のいずれも使用することができます。Visual Basic も文字列リテラルのみ存在しますが (囲み文字は Java 同様 `" "` (ダブルクォート) のみ)、エスケープ・シーケンスが使用できず代わりに `vbCrLf` などを使用する点が異なります。

### 4.4.4. その他のリテラル

#### 4.4.4.1. 論理リテラル (BooleanLiteral)

論理リテラルは論理値を表すリテラルで、真 `true`、偽 `false` の 2 値となります。また、`boolean` 型の値となります。

>【対比】 論理リテラルは C (C99 規格以降)、Python、JavaScript、Visual Basic にも存在します。他の言語では論理型と整数型は変換可能ですが (多くは 真 = 1、偽 = 0 となりますが、Visual Basic だけは真 = -1、偽 = 0)、Java ではできないことに注意してください。

#### 4.4.4.2. Null リテラル (NullLiteral)

いずれのインスタンスも参照しないことを示すリテラルを Null リテラルといい、キーワード `null` で表します。

>【対比】 C には言語レベルの Null はなく、マクロ定数 `NULL` が相当します (値はコンパイラに依存します)。Python は `None`、JavaScript は `null`、Visual Basic は `Nothing` がそれぞれ相当します。

## 4.5. 式

処理の基本単位を「式」といい、式を実行することを「評価する」といいます。式を評価した結果を「値」といい、値はプリミティブ型 (数値型・浮動小数点型・論理型)、何らかのクラス、または `void` (値を持たない) になります。

### 4.5.1. 一次式

Java において最も単純な式を一次式 (Primary) と呼びます。一次式は他のすべての式に優先して評価されます。一次式には以下のものが挙げられます。

- 配列生成式ではない一次式
  - リテラル
  - クラス・リテラル
  - this 参照
  - 括弧
  - インスタンス生成式
  - フィールド・アクセス式
  - 配列アクセス式 (10 章)
  - メソッド呼び出し式
  - メソッド参照式 (12 章)
- 配列生成式 (10 章)

#### 4.5.1.1. リテラル

- リテラルそのものを表します。
- 評価後の式の値はリテラルの値そのものであり、データ型もリテラルと一致します。

#### 4.5.1.2. クラス・リテラル

- 書式: `プリミティブ型名 . class`、`クラス名 . class` または `void . class`
- 実行中の Java アプリケーションのデータ型を表します。
- 評価後の式の値は `Class` クラスのインスタンスとなります。`Class` クラスは 7 章で取り上げる `Object` クラスの `getClass()` メソッドの戻り値でもあります。
- クラス・リテラルをアプリケーション内で処理することは稀です。多くの場合、引数の型が `Class` であるメソッドを呼び出すために使用します。

#### 4.5.1.3. this 参照

- 書式: `this`
- メソッド内において、メソッドと紐付くインスタンス自身を表します。`static` メソッド内では紐付くインスタンスがないため使用できません。
- 評価後の式の値はインスタンスそのものであり、データ型もインスタンスと一致します。
- フィールド・アクセス式やメソッド呼び出し式と組み合わせて、`this . フィールド名` や `this . メソッド名 ( [実引数のリスト] )` のように使用します。

#### 4.5.1.4. 括弧

- 書式: `( type )` (`type`: 式)
- 括弧に囲まれた式を評価します。対象となる式はすべての式です (つまり、括弧をネストさせることも可能です)。
- 評価後の式の値は、括弧内の式の評価結果がそのまま適用されます。また。データ型は括弧内の式の種類に依存します。
- 括弧は一次式であり、括弧で囲まれた式は最優先で評価されます。そのため、括弧は式の評価順位を制御するためのツールとして用いることができます。

#### 4.5.1.5. インスタンス生成式

- 書式: `new クラス名 ( [ 実引数のリスト] )`
- 評価後の式の値は生成したインスタンスとなり、データ型は指定したクラスです。

#### 4.5.1.6. フィールド・アクセス式

- 書式: `インスタンス . フィールド名` または `クラス . フィールド名`
- 評価後の式の値はアクセスしたフィールドとなります。データ型は変化しません。

#### 4.5.1.7. メソッド呼び出し式

- 書式: `インスタンス . メソッド名 ( [実引数のリスト] )` または `クラス . メソッド名 ( [実引数のリスト] )`
- 評価後の式の値は呼び出したメソッドの戻り値となります。
- 評価後の式のデータ型はメソッドの戻り値の型となりますが、戻り値がない場合は `void` になります。なお、`void` の場合はさらに式を適用することはできません (後述のクラス・リテラルを除きます)。

### 4.5.2. 後置式

後置式 (PostfixExpression) は一次式に次いで 2 番目に高い優先順位で評価される式です。

- 変数
- 後置インクリメント式
- 後置デクリメント式

#### 4.5.2.1. 変数

式の値に名前を付けたものを「変数」と呼びます。通常はメソッド (ブロック) で有効な「ローカル変数」のことを指しますが、フィールドもまた変数とみなすことができます (インスタンス全体で有効な変数という意味で「インスタンス変数」と呼ばれることもあります)。

変数の名前を準備することを「変数を宣言する」といい、宣言した変数に値を対応付けることを「変数に値を代入する」といいます。変数の宣言と値の代入を同時に行うことを「変数を初期化する」といいます。宣言直後の (初期化を行っていない) フィールドとローカル変数の状態を以下に示します。

|データ型|フィールド|ローカル変数|
|----|----|:--:|
|`boolean`|`false` で初期化|不定|
|`byte`|`(byte) 0` で初期化|不定|
|`short`|`(short) 0` で初期化|不定|
|`int`|`0` で初期化|不定|
|`long`|`0L` で初期化|不定|
|`char`|`'\u0000'` で初期化|不定|
|`float`|`0.0F` で初期化|不定|
|`double`|`0.0D` で初期化|不定|
|クラス|`null` で初期化|不定|

ローカル変数は (初期化を行わない限り) 状態が「不定」となることに注意してください。これは、ローカル変数は初期化するか、あるいは確実に代入しなければならないことを意味します。状態が不定なローカル変数は評価できず、コンパイルエラーとなります。

変数は評価されると、プリミティブ型はそのまま式の値となり、クラスはインスタンスが式の値となります。なお、クラスがいずれのインスタンスも参照していない場合は `null` が式の値となります。

>`null` に対してさらに式の評価 (例: メソッド呼び出し式の実行) を行わないでください。`null` に対する式の評価が行われた場合は、Java VM が非チェック例外 `NullPointerException` をスローし処理が中断します。例外については 6 章を参照してください。

#### 4.5.2.2. 後置インクリメント式と後置デクリメント式

- 書式: 後置インクリメント式 `var++`、後置デクリメント式 `var--` (`var`: 整数型・浮動小数点型の**変数**)
- 式を評価した後にその値を `+1` または `-1` します。データ型は変わりません。
  - 例えば、`var` の値が `3` の場合に式 `var++` の値は `3` と評価され、その後 `var` の値が `4` となります。
  - 同様に `ｎ` の値が `3` の場合に式 `var--` の値は `3` と評価され、その後 `var` の値が `2` となります。
- 式の結合順序: `var++++` は `(var++)++`、`var----` は `(var--)--` として評価されます。

>【対比】 後置インクリメント式および後置デクリメント式は C および JavaScript には存在しますが、Python および Visual Basic には存在しません。

### 4.5.3. 単項式

単項式 (UnaryExpression) は後置式に次いで 3 番目に高い優先順位で評価される式です。

- 前置インクリメント式
- 前置デクリメント式
- 符号
- ビット反転
- 否定
- キャスト

#### 4.5.3.1. 前置インクリメント式と前置デクリメント式

- 書式: 前置インクリメント式 `++var`、前置デクリメント式 `--var` (`var`: 整数型・浮動小数点型の**変数**)
- 式の値を `+1` (前置インクリメント式) または `-1` (前置デクリメント式) した後に式を評価します。データ型は変わりません。
  - 例えば、`var` の値が `3` の場合に式 `++var` の値が `4` となりその値で評価されます。
  - 同様に `var` の値が `3` の場合に式 `--var` の値は `2` となりその値で評価されます。
- 式の結合順序 (1): `++++var` は `++(++var)`、`----var` は `--(--var)` として評価されます。
- 式の結合順序 (2): `++var++` は `++(var++)`、`--var--` は `--(var--)` として評価されます (後置インクリメント式および後置デクリメント式の方が優先順位が高いため)。

>【対比】 前置インクリメント式および前置デクリメント式は C および JavaScript には存在しますが、Python および Visual Basic には存在しません。

#### 4.5.3.2. 符号

- 書式: `+n` または `-n` (`n`: 整数型・浮動小数点型)
- 式 (整数型・浮動小数点型) の符号を維持 (`+n`) または反転 (`-n`) します。データ型は変わりません。
- 式の結合順序 (1): `+ +n`、`- -n` は `+(+n)`、`-(-n)` として評価され、最終的には `(n)`、`(n)` と評価されます。
- 式の結合順序 (2): `++n`、`--n` と表記するとそれぞれ前置インクリメント式、前置デクリメント式となり、符号の二重適用にはなりません (優先順位の高い前置インクリメント式・前置デクリメント式とみなします)。

#### 4.5.3.3. ビット反転

- 書式: `~n` (`n`: 整数型)
- 式の値の 2 進数表記について全ビットを反転します。データ型は変わりません。
- 浮動小数点型は単純な 2 進数表記ではないため (IEEE 754 浮動小数点数)、ビット反転は適用できません。
- 結合順序: `~~n` は `~(~n)` として評価され、最終的には `(n)` と評価されます。

>【対比】 C、Python および Javascript のビット反転は Java と同じ `~` となります。Visual Basic ではビット反転は否定 (次項) と同じ `Not` であり、文脈により判断されます。

#### 4.5.3.4. 否定

- 書式: `!b` (`b`: 論理型)
- 式の値 (論理型) を否定します。式の値が `true` の場合は `false` として評価され、`false` の場合は `true` として評価されます。
- 主に後述の関係演算式、等価演算式、条件 AND/OR 演算式に対して適用します (何れも式の値は論理型です)。

>【対比】 C および JavaScript の否定は Java と同じ `!` です。Python の否定は `not` です。Visual Basic では否定はビット反転 (前項) と同じ `Not` であり、文脈により判断されます。

#### 4.5.3.5. キャスト

- 書式: `(変換後のデータ型) expr` (`expr`: 式)
- データ型 (プリミティブ型またはクラス) の変換を行う式です。
- 用途 (1): 数値型のサイズ縮小
  - `char`、`short`、`int`、`long` から `byte` へ
  - `char` ('\u8000' ～ '\uffff')、`int`、`long` から `short` へ
  - `long` から `int` へ
  - `double` から `float` へ
  - 上記いずれも、変換後のデータ型が変換前の式の値を格納できることが条件です。
  - 評価後の式の値は、データ型が変換後のものになります。
- 用途 (2): 数値 (整数型) から 数値 (浮動小数点型) へのサイズ拡大
  - `byte`、`short`、`char` から `float` へ
  - `byte`、`short`、`int`、`char` から `double` へ
  - `int`、`long` から `float` への変換、および `long` から `double` への変換自体は可能ですが、精度不足で変換前の式の値を維持できないことに注意してください。
  - 評価後の式の値は、データ型が変換後のものになります。なお、浮動小数点数の仕様上、式の値を完全に維持できるとは限りません。
- 用途 (3): スーパークラス/インタフェースの参照からサブクラス/実装クラスの参照への変換 (ダウンキャスト)
  - `Number` クラス (スーパークラス) から `BigDecimal` クラス (サブクラス) への変換
  - `List` インタフェースから `ArrayList` クラス (実装クラス) への変換
  - 変換前のクラスが参照しているインスタンスは、変換後のクラスのインスタンスである必要があります。後述の `instanceof` 式で、`変換前 instanceof 変換後` が `true` となる場合のみ変換可能です。変換できない場合は非チェック例外 `ClassCastException` がスローされます (例外については 6 章を参照のこと)。
- 結合順序: `(型) (型) expr` は `(型) ((型) expr)` として評価されます。

```java
// (1) 数値型のサイズ縮小
int intValue = 10;
byte byteValue = (byte) intValue;

// (2) 数値 (整数型) から数値 (浮動小数点型) へのサイズ拡大
int x = 3;
int y = 2;
double d = (double) x / (double) y;

// (3) スーパークラスの参照からサブクラスの参照への変換
// number のインスタンスが BigDecimal であること
BigDecimal decimal = (Number) number;
```

>【対比】 C は Java と同じ書式のキャストがあります (データ型は異なります)。Python、JavaScript および Visual Basic ではデータ型変換の関数を利用します (例えば、Visual Basic の場合は `CStr`、`CInt`、`CDouble` など)。

### 4.5.4. 乗算式と加算式 (四則演算)

乗算式 (MultiplicativeExpression) は単項式に次いで 4 番目に高い優先順位で評価される式です。

- 書式: 乗算式 `x * y`、除算式 `x / y`、剰余式 `x % y` (`x`, `y`: 整数型・浮動小数点型)
- 2 つの式の乗算・除算・剰余の算出を行います。
- 評価前の式が異なるデータ型の場合、より大きいデータ型 (整数型: `byte` < `short` < `int` < `long`、浮動小数点型: `float` < `double`) に、また整数型と浮動小数点型が混在している場合は浮動小数点型に統一されます。
- 評価後の式の値はそれぞれの演算結果で、データ型は統一された後のものになります。
- `0` による除算 (`x / 0`) を行うと非チェック例外 `ArithmeticException` がスローされます (例外については 6 章を参照のこと)。
- 結合順序: `x * y * z`、`x / y / z`、`x % y % z` はそれぞれ `(x * y) * z`、`(x / y) / z`、`(x % y) % z` として評価されます。

>【対比】 乗算式・除算式・剰余式は C、Python、JavaScript および Visual Basic でサポートされます。ただし、剰余式で浮動小数点数を使用できるのは Java と JavaScript だけです。また、剰余の記号は Visual Basic のみ `Mod` となります。

加算式 (AdditiveExpression) は乗算式に次いで 5 番目に高い優先順位で評価される式です。

- 書式: 加算式 `x + y`、減算式 `x - y` (`x`, `y`: 整数型・浮動小数点型)
- 2 つの式の加算・減算を行います。
- 評価前の式が異なるデータ型の場合、より大きいデータ型 (整数型: `byte` < `short` < `int` < `long`、浮動小数点型: `float` < `double`) に、また整数型と浮動小数点型が混在している場合は浮動小数点型に統一されます。
- 評価後の式の値はそれぞれの演算結果で、データ型は統一された後のものになります。
- 結合順序: `x + y + z`、`x - y - z` はそれぞれ `(x + y) + z`、`(x - y) - z` として評価されます。

>【対比】 加算式・減算式は C、Python、JavaScript および Visual Basic でサポートされます。加算演算子 `+` は文字列結合にも使用しますが (演算子オーバーライド)、C では文字列リテラル同士の結合にしか使用できない点、Visual Basic では `&` による文字列結合が推奨される点がそれぞれ異なります。

>括弧、符号、乗算式、加算式の優先順位は、括弧 (1 位) > 符号 (3 位) > 乗算式 (4 位) > 加算式 (5 位) となり、数式における優先順位と一致します。

### 4.5.5. シフト演算式

シフト演算式 (ShiftExpression) は加算式に次いで 6 番目に高い優先順位で評価される式です。

- 書式: 左シフト `n << m`、右算術シフト `n >> m`、右論理シフト `n >> m` (`n`、`m`: 整数型)
- 式 `n` を `m` ビットだけシフトします。右算術シフトでは符号は維持されますが、右論理シフトでは符号ビットがクリアされます。
- 浮動小数点数型は単純な 2 進数表記ではないため (IEEE 754 浮動小数点数)、シフト演算式は適用できません。
- 結合順序: `n << m << k` は `(n << m) << k` として評価されます。他も同様です。

>【対比】 シフト演算式は C、Python、JavaScript に存在し、Visual Basic には存在しません。JavaScript のシフト演算式は Java と同一です。Python は論理シフト `>>>` がなく、すべて算術シフトになります。C は `>>>` 演算子がない代わりに、データ型の符号の有無により算術シフトと論理シフトが切り替わります。

### 4.5.6. 関係演算式と等価演算式

関係演算式 (RelationalExpression) はシフト演算式に次いで 7 番目に高い優先順位で評価される式です。

- 書式 (1):  より小さい `expr1 < expr2`、より大きい `expr1 > expr2`、以下 `expr1 <= expr2`、以上 `expr1 => expr2` (`expr1`, `expr2`: 整数型・浮動小数点型)
- 書式 (2): `ref instanceof type` (`ref`: インスタンスへの参照、`type`: クラス)
- 書式 (1) は式 `expr1` と `expr2` を評価し、大小関係が条件を満たしている場合には式の値を `true`、そうでない場合は `false` とします。
- 書式 (2) はインスタンスの参照 `ref` がクラス `type` のインスタンスである場合には式の値を `true`、そうでない場合は `false` とします。
- 書式 (1)、書式 (2) のいずれも、評価後の式の値は論理型となります。
- 結合順序: `expr1 < expr2 < expr3` は `(expr1 < expr2) < expr3` として評価されます。他も同様です。

>【対比】 関係演算式 (`instanceof` を除く) は C、Python、JavaScript、Visual Basic に存在し、記法・用法も同じです。

等価演算式 (EqualityExpression) は関係演算式に次いで 8 番目に高い優先順位で評価される式です。

- 書式: 等しい `expr1 == expr2`、等しくない `expr1 != expr2` (`expr1`, `expr2`: 整数型・浮動小数点型・論理型・クラス)
- 式 `expr1` と `expr2` を評価し、等価関係 (等しい/等しくない) が条件を満たしている場合には式の値を `true`、そうでない場合は `false` とします。
- 評価後の式の値は論理型となります。
- 結合順序: `expr1 == expr2 == expr3` は `(expr1 == expr2) == expr3` として評価されます。他も同様です。

>【対比】 等価演算式は C、Python、JavaScript および Visual Basic に存在しますが、Visual Basic のみ記法が異なり等号 `=`、不等号 `<>` となります。

### 4.5.7. ビット演算式 (AND 演算式・OR 演算式・XOR 演算式)

#### 4.5.7.1. AND 演算式

AND 演算式 (AndExpression) は等価演算式に次いで 9 番目に高い優先順位で評価される式です。

- 書式: `expr1 & expr2` (`expr1`, `expr2`: 整数型・論理型)
- 式 `expr1` および `expr2` を評価し、それぞれの式の値の 2 進数表現について AND (論理積) 演算を行います。評価後の式の値についてデータ型は維持されます。
- 式 `expr1` および `expr2` は論理型であっても構いません。式の値は `true & true` については `true`、それ以外の組み合わせについては `false` となります。
- 式 `expr1` および `expr2` は論理型であっても必ず両方が評価されます (条件 AND 演算式 `expr1 && expr2` では `expr1` が `false` の場合には `expr2` は評価されません)。
- 結合順序: `expr1 & expr2 & expr3` は `(expr1 & expr2) & expr3` として評価されます。

#### 4.5.7.2. XOR 演算式

XOR 演算式 (ExclusiveOrExpression) は AND 演算式に次いで 10 番目に高い優先順位で評価される式です。

- 書式: `expr1 ^ expr2` (`expr1`, `expr2`: 整数型・論理型)
- 式 `expr1` および `expr2` を評価し、それぞれの式の値の 2 進数表現について XOR (排他的論理和) 演算を行います。評価後の式の値についてデータ型は維持されます。
- 式 `expr1` および `expr2` は論理型であっても構いません。式の値は `true ^ true` および `false ^ false` については `true`、`true ^ false` および `false ^ true` については `false` となります。
- 結合順序: `expr1 ^ expr2 ^ expr3` は `(expr1 ^ expr2) ^ expr3` として評価されます。

#### 4.5.7.3. OR 演算式

OR 演算式 (InclusiveOrExpression) は XOR 演算式に次いで 11 番目に高い優先順位で評価される式です。

- 書式: `expr1 | expr2` (`expr1`, `expr2`: 整数型・論理型)
- 式 `expr1` および `expr2` を評価し、それぞれの式の値の 2 進数表現について OR (論理和) 演算を行います。評価後の式の値についてデータ型は維持されます。
- 式 `expr1` および `expr2` は論理型であっても構いません。式の値は `false | false` については `false`、それ以外の組み合わせについては `true` となります。
- 式 `expr1` および `expr2` は論理型であっても必ず両方が評価されます (条件 OR 演算式 `expr1 || expr2` では `expr1` が `true` の場合には `expr2` は評価されません)。
- 結合順序: `expr1 | expr2 | expr3` は `(expr1 | expr2) | expr3` として評価されます。

>【対比】 ビット演算式は C、Python、JavaScript および Visual Basic に存在しますが、Visual Basic のみ記法が異なり `And`、`Or`、`Xor` となります。

### 4.5.8. 条件 AND/OR 演算式

#### 4.5.8.1. 条件 AND 演算式

条件 AND 演算式 (ConditionalAndExpression) は OR 演算式に次いで 12 番目に高い優先順位で評価される式です。

- 書式: `expr1 && expr2` (`expr1`, `expr2`: 論理型)
- 式 `expr1` および `expr2` を評価し、いずれも `true` の場合は式の値が `true`、それ以外は式の値が `false` となります。
- 式 `expr1` を評価した結果が `false` の場合、`expr2` は評価されません (AND 演算式 `expr1 & expr2` で論理型を使用した場合は `expr1` と `expr2` の両方が必ず評価されます)。
- 結合順序: `expr1 && expr2 && expr3` は `(expr1 && expr2) && expr3` として評価されます。

#### 4.5.8.2. 条件 OR 演算式

条件 OR 演算式 (ConditionalOrExpression) は 条件 AND 演算式に次いで 13 番目に高い優先順位で評価される式です。

- 書式: `expr1 || expr2` (`expr1`, `expr2`: 論理型)
- 式 `expr1` および `expr2` を評価し、いずれか一方でも `true` の場合は式の値が `true`、それ以外 (両方とも `false`) は式の値が `false` となります。
- 式 `expr1` を評価した結果が `true` の場合、`expr2` は評価されません (OR 演算式 `expr1 | expr2` で論理型を使用した場合は `expr1` と `expr2` の両方が必ず評価されます)。
- 結合順序: `expr1 || expr2 || expr3` は `(expr1 || expr2) || expr3` として評価されます。

>【対比】  条件 AND/OR 演算式は C、Python、JavaScript および Visual Basic に存在しますが、Visual Basic のみ記法が異なり `And`、`Or` となります。

### 4.5.9. 条件演算式 (三項演算子)

条件演算式 (ConditionalExpression) は条件 OR 演算式に次いで 14 番目に高い優先順位で評価される式です。

- 書式: `cond ? expr1 : expr2` (`cond`: 論理型、`expr1`, `expr2`: 整数型・浮動小数点型・論理型・クラス)
- `cond` が `true` の場合は式の値として `expr1` を、`false` の場合は `expr2` を選択します。
- 後述の if-else 文と似ていますが、あくまで式であるため、値となる `expr1` および `expr2` を評価した結果は `void` になってはいけません。
- `expr1` と `expr2` の一方が論理型である場合は、他方も論理型である必要があります。
- `expr1` と `expr2` の一方が整数型または浮動小数点型の場合は、他方も整数型または浮動小数点型である必要があります。ただし式の値の適用先 (多くは代入式) に合わせられる必要があります。
- `expr1` と `expr2` の一方がインスタンスの場合は、他方はインスタンスまたは `null` である必要があります。なお、両方とも式の値の適用先 (多くは代入式) のクラスで参照できるインスタンスでなければなりません。

>【対比】  条件演算子 (三項演算子) は C および JavaScript に同じ構文があります。Python は構文が異なります。Visual Basic では `IIf` 関数で代用します。

### 4.5.10. 代入演算式

代入演算式 (AssignmentExpression) は変数に値を代入するために使用し、最も低い優先順位で評価される式です。代入式には単純代入演算式と複合代入演算式があります。

|複合代入演算式|単純代入演算式|結合順序 (評価前)|結合順序 (評価後)|
|--------|--------|--------|--------|
|             |`lhs = expr`|`lhs = lhs = expr`|`lhs = (lhs = expr)`|
|`lhs *= expr`|`lhs = lhs * expr`|`lhs *= lhs *= expr`|`lhs *= (lhs *= expr)`|
|`lhs /= expr`|`lhs = lhs / expr`|`lhs /= lhs /= expr`|`lhs /= (lhs /= expr)`|
|`lhs %= expr`|`lhs = lhs % expr`|`lhs %= lhs %= expr`|`lhs %= (lhs %= expr)`|
|`lhs += expr`|`lhs = lhs + expr`|`lhs += lhs += expr`|`lhs += (lhs += expr)`|
|`lhs -= expr`|`lhs = lhs - expr`|`lhs -= lhs -= expr`|`lhs -= (lhs -= expr)`|
|`lhs <<= expr`|`lhs = lhs << expr`|`lhs <<= lhs <<= expr`|`lhs <<= (lhs <<= expr)`|
|`lhs >>= expr`|`lhs = lhs >> expr`|`lhs >>= lhs >>= expr`|`lhs >>= (lhs >>= expr)`|
|`lhs >>= expr`|`lhs = lhs >>> expr`|`lhs >>>= lhs >>>= expr`|`lhs >>>= (lhs >>>= expr)`|
|`lhs &= expr`|`lhs = lhs & expr`|`lhs &= lhs &= expr`|`lhs &= (lhs &= expr)`|
|`lhs ^= expr`|`lhs = lhs ^ expr`|`lhs^ = lhs ^= expr`|`lhs^ = (lhs ^= expr)`|
|`lhs |= expr`|`lhs = lhs | expr`|`lhs |= lhs |= expr`|`lhs |= (lhs |= expr)`|

- lhs: 左辺式 - 変数、フィールド・アクセス式、配列アクセス式 (10 章)
- expr: 式 - 代入式を含むすべての式

>【対比】  複合代入演算式は C、Python および JavaScript に存在しますが、Python では文脈により異なる意味合いを持つ場合があります。Visual Basic には複合代入演算子は存在しません。
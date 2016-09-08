# 4. 文と式

Java における処理の記述を文 (Statement) といいます。文の一覧を下表に示します。

|文の分類|文|解説する章|
|--------|--------|----|
|単純な文|式文、空文|4 章|
|分岐・繰り返し|if 文、if-else 文、while 文、do 文、switch 文|5 章|
| |for 文|5 章、10 章|
|ジャンプ|break 文、continue 文、return 文|5 章|
|例外処理|throw 文|6 章|
| |try 文|6 章、11 章|
|同期化|synchronized 文|9 章|
|文のグルーピング|ブロック|4 章|

- if-else 文、while 文および for 文は、if 文との組み合わせで注意事項があります。詳細は 5 章にて取り上げます。
- 上記の他にラベル付き文、assert 文が用意されていますが、特殊な用途で使用頻度も高くないためここでは割愛します。

この章では、式文、空文、ブロックの 3 種類と、式文のもとになる「式」について解説します。

## 式文

式文は、文字通り式を実行 (評価) する文です。Java では以下の式の末尾に `;` (セミコロン) を置いたものが式文となります。それぞれの式については後述しますが、ここでは (1) 式を実行する文を式文という、(2) 式文に使える式には一応制限が設けられている (どのような式でも式文にできるわけではない) ことを押さえておいてください。

- 代入演算式
- 前置インクリメント式
- 前置デクリメント式
- 後置インクリメント式
- 後置デクリメント式
- メソッド呼び出し式
- インスタンス生成式

>実際のプログラミングで多用することになる四則演算の式は、単独では式文にすることができません。四則演算の式は代入演算式やメソッド呼び出し式と組み合わせて使うことになります。式文の制約はプログラミング言語によって異なりますが、C や JavaScript は制約が緩く、Python や Visual Basic は制約が厳しい傾向にあります。

## 空文

空文 (EmptyStatement) は、何も処理をしない文です。`;` (セミコロン) だけで表します。

>空文は後述の while 文、do 文、for 文においてダミー処理が必要になった時に使用します。C や JavaScript には空文は存在しており記法・用途も Java と同様ですが、Python や Visual Basic には空文の概念がないため注意が必要です。

## ブロック

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

>ブロックは C や JavaScript に存在しており記法・用途も Java と同様です。Python はブロックの代わりにインデント (オフサイド・ルール) を用います。Visual Basic のブロックは各構文と一体化しており、Java のように単独で用いることはできません。

ローカル変数は、宣言されたメソッド内でのみ有効です。メソッドの仮引数もローカル変数と同じものとみなされます。同じブロック内でローカル変数名を重複させることはできませんが、異なるブロックであれば重複は許されます。ただし、ブロックをネストさせている場合には、内側のブロック自体が外側のブロックの一部とみなされるため、ローカル変数名の重複は許されません。

>ローカル変数に対して、フィールドはインスタンスの範囲で有効な変数と言い換えることができます。フィールドとローカル変数は同じ変数名を使用することができますが、同時に使用する場合にはフィールド名の先頭に `this .` を付加してインスタンスに属する = フィールドであることを明示する必要があります。

## リテラル

### 整数リテラル (IntegerLiteral)

整数リテラルは 10 進数、16 進数、8 進数および 2 進数の各表記があります。

#### 10 進数整数リテラル

- 使用できる数字は `0` `1` `2` `3` `4` `5` `6` `7` `8` `9` です。
- `0`、または `1` `2` `3` `4` `5` `6` `7` `8` `9` から始まる 1 つ以上の数字で構成されます。
- 原則として `int` 型の値となりますが、末尾に `l` または `L` を付加することで `long` 型の値となります。
- 数字と数字の間に `_` (アンダースコア) を含めて、桁区切りとして用いることができます。

#### 16 進数整数リテラル

- 使用できる数字は `0` `1` `2` `3` `4` `5` `6` `7` `8` `9` `a` `b` `c` `d` `e` `f` `A` `B` `C` `D` `E` `F` です。
- 先頭 `0x` または `0X` で始まり、1 つ以上の数字で構成されます。
- 原則として `int` 型の値となりますが、末尾に `l` または `L` を付加することで `long` 型の値となります。
- 数字と数字の間に `_` (アンダースコア) を含めて、桁区切りとして用いることができます。

#### 8 進数整数リテラル

- 使用できる数字は `0` `1` `2` `3` `4` `5` `6` `7` です。
- 先頭 `0` で始まり、1 つ以上の数字で構成されます。
- 原則として `int` 型の値となりますが、末尾に `l` または `L` を付加することで `long` 型の値となります。
- 数字と数字の間に `_` (アンダースコア) を含めて、桁区切りとして用いることができます。

#### 2 進数整数リテラル

- 使用できる数字は `0` `1` です。
- 先頭 `0b` または `0B` で始まり、1 つ以上の数字で構成されます。
- 原則として `int` 型の値となりますが、末尾に `l` または `L` を付加することで `long` 型の値となります。
- 数字と数字の間に `_` (アンダースコア) を含めて、桁区切りとして用いることができます。

>【バージョン】 `_` (アンダースコア) を区切り文字として使用できる仕様、および 2 進数リテラルは Java SE 7 以降で導入されたものです。

>Java とその他の言語 (C、Python、JavaScript、Visual Basic) との整数リテラルの対照表を以下に示します。
>|言語|10 進数|16 進数|8 進数|2 進数|桁区切り|サフィックス|
>|----|----|----|----|----|----|----|
>|Java|そのまま|先頭 `0x` `0X`|先頭 `0`|先頭 `0b` `0B`|`_` (アンダースコア)|`l` `L` で `long` 型|
>|C|そのまま|先頭 `0x` `0X`|先頭 `0`|N/A|N/A|`l` `L` で `long` 型|
>|Python|そのまま|先頭 `0x` `0X`|先頭 `0`|先頭 `0b` `0B`|N/A|`l` `L` で `long` 型|
>|JavaScript|そのまま|先頭 `0x` `0X`|N/A|N/A|N/A|N/A|
>|Visual Basic|そのまま|先頭 `&h` `&H`|先頭 `&o` `&O`|N/A|N/A|`%` で `Integer` 型、`&` で `Long` 型|

### 浮動小数点数リテラル (FloatingPointLiteral)

浮動小数点数リテラルは 10 進数および 16 進数の表記があります。

#### 10 進数浮動小数点数リテラル

10 進数整数リテラルと同じ数字を使用し、かつ以下のいずれかの条件を満たす場合に、10 進数浮動小数点リテラルとなります。

- `.` (小数点) が存在する
- `e` `E` で始まる指数部を含む

10 進数浮動小数点数リテラルには「小数点表記」と「指数表記」の 2 種類が存在します。

小数点表記は、例えば数式 0.015 のように小数点を含む形で表す記法です。小数点表記は事務処理計算で多用される記法です。

指数表記は、例えば数式 1.5 × 10<sup>-2</sup> を 1.5e-2 とする記法で、この時の 1.5 を仮数部、e2 を指数部といいます。仮数部は整数リテラルまたは小数点表記の浮動小数点リテラルと同じ形式となります。指数部は先頭が `e` または `E` で始まり、それに指数 (正または負の整数、この例では指数は -2) が続きます。例えば 2e3、3.0e+2、0.5e-1 はいずれも指数表記となります (それぞれ 2 × 10<sup>3</sup>、3.0 × 10<sup>2</sup>、0.5 × 10<sup>-1</sup> を意味します)。指数表記は科学技術計算で多用される記法です。

10 進数浮動小数点数リテラルの型は、末尾に `f` `F` が付加された場合は `float` 型、`d` `D` が付加された場合は `double` 型となります。省略時には `double` 型とみなされます。

>C、Python、JavaScript および Visual Basic にも 10 進数浮動小数点数リテラルが存在しており、記法も Java と同じです。

#### 16 進数浮動小数点数リテラル

先頭 `0x` `0X` で始まり、かつ以下のいずれかの条件を満たす場合に、16 進数の浮動小数点リテラルとなります (数字として 16 進数整数リテラルと同じものを使用できるようになります)。

- `.` (小数点) が存在する
- `p` `P` で始まる指数部を含む

16 進数浮動小数点数リテラルには「小数点表記」と「指数表記」の 2 種類が存在します。

小数点表記は、例えば数式 0x1F.7FF のように小数点を含む形で表す記法です。これは 10 進数浮動小数点数リテラルを 16 進数に拡張したもので、先頭の `0x` `0X` で 10 進数浮動小数点数リテラルと区別されます。

指数表記は、例えば数式 0x1.ff × 2<sup>-3</sup> を 0x1.ffp-3 とする記法で、この時の 1.ff を仮数部、p-3 を指数部といいます。こちらも 10 進数浮動小数点数リテラルを 16 進数に拡張したもので、先頭の `0x` `0X` および指数部の先頭 `p` `P` で　10 進数浮動小数点数リテラルと区別されます。

16 進数浮動小数点数リテラルの型は、末尾に `f` `F` が付加された場合は `float` 型、`d` `D` が付加された場合は `double` 型となります。省略時には `double` 型とみなされます。

>C には Java と同じ記法の 16 進数浮動小数点数リテラルが存在します (C99 規格以降)。Python、JavaScript、Visual Basic には 16 進数浮動小数点数リテラルは存在しません。

### 文字リテラルと文字列リテラル

#### 文字リテラル (CharacterLiteral)

文字リテラルは 1 文字を表すリテラルで、文字、Unicode エスケープ (Unicode の 16 進数文字コード 4 桁 *xxxx* を \u*xxxx* 形式で表す記法) またはエスケープ・シーケンスを `' '` (シングルクォート) で囲んで表します。ただし、`'` (シングルクォート) および `\` (バックスラッシュ) は文字として表すことはできず、代わりにエスケープ・シーケンスを用います。文字リテラルは `char` 型の値となります。

|Esc|意味|
|:----:|--------|
|`\b`|backspace BS, Unicode \u0008|
|`\t`|horizontal tab HT, Unicode \u0009| 
|`\n`|linefeed LF, Unicode \u000a|
|`\f`|form feed FF, Unicode \u000c|
|`\r`|carriage return CR, Unicode \u000d| 
|`\"`|double quote ", Unicode \u0022| 
|`\'`|single quote ', Unicode \u0027 
|`\\`|backslash \, Unicode \u005c| 

#### 文字列リテラル (StringLiteral)

文字列リテラルは 0 文字以上の文字列を表すリテラルで、文字、Unicode エスケープまたはエスケープ・シーケンスの列を `" "` (ダブルクォート) で囲んで表します。ただし、`'` (ダブルクォート) および `\` (バックスラッシュ) は文字として表すことはできず、代わりにエスケープ・シーケンスを用います。文字列リテラルは `String` 型への参照となります。

>C には Java と類似した記法の文字リテラルおよび文字列リテラルが存在します (Unicode エスケープが使用できるのは C99 規格以降)。Python および JavaScript には文字列リテラルのみが存在し、囲み文字は `" "` (ダブルクォート) と `' '` (シングルクォート) のいずれも使用することができます。Visual Basic も文字列リテラルのみ存在しますが (囲み文字は Java 同様 `" "` (ダブルクォート) のみ)、エスケープ・シーケンスが使用できず代わりに `vbCrLf` などを使用する点が異なります。

### その他のリテラル

#### 論理リテラル (BooleanLiteral)

論理リテラルは論理値を表すリテラルで、真 `true`、偽 `false` の 2 値となります。また、`boolean` 型の値となります。

>論理リテラルは C (C99 規格以降)、Python、JavaScript、Visual Basic にも存在します。他の言語では論理型と整数型は変換可能ですが (多くは 真 = 1、偽 = 0 となりますが、Visual Basic だけは真 = -1、偽 = 0)、Java ではできないことに注意してください。

#### Null リテラル (NullLiteral)

いずれのインスタンスも参照しないことを示すリテラルを Null リテラルといい、キーワード `null` で表します。

>C には言語レベルの Null はなく、マクロ定数 `NULL` が相当します (値はコンパイラに依存します)。Python は `None`、JavaScript は `null`、Visual Basic は `Nothing` がそれぞれ相当します。

## 式

処理の基本単位を「式」といい、式を実行することを「評価する」といいます。式を評価した結果を「値」といい、値はプリミティブ型、何らかのクラス、または `void` (値を持たない) になります。

値に名前を付けたものを「変数」と呼びます。

### 一次式

Java において最も単純な式を一次式 (Primary) と呼びます。一次式は他のすべての式に優先して評価されます。一次式には以下のものが挙げられます。

- 配列生成式ではない一次式
  - リテラル
  - クラス・リテラル (7 章)
  - `this` 参照 (インスタンスにおいて自分自身の参照を表す)
  - `(` *式* `)` (括弧)
  - インスタンス生成式 (3 章)
  - フィールド・アクセス式 (3 章)
  - 配列アクセス式 (10 章)
  - メソッド呼び出し式 (3 章)
  - メソッド参照式 (12 章)
- 配列生成式 (10 章)

>括弧で囲まれた式は最優先で評価されます (括弧をネストさせることも可能です)。そのため、括弧は式の評価順位を制御するためのツールとして用いることができます。

### 後置式

後置式 (PostfixExpression) は一次式に次いで 2 番目に高い優先順位で評価される式です。

- 変数
- 後置インクリメント式 (`++`) ; (例) `index++`
- 後置デクリメント式 (`--`) ; (例) `index--`

変数は評価されると、それがプリミティブ型の場合はその値がそのまま式の値となり、クラスの場合はインスタンスが式の値となります。なお、クラスがいずれのインスタンスも参照していない場合は `null` (Null リテラル) が式の値となります。

>`null` に対してさらに式の評価 (例: メソッド呼び出し式の実行) を行わないでください。`null` に対する式の評価が行われた場合は、Java VM が非チェック例外 `NullPointerException` をスローし処理が中断します。例外については 6 章を参照してください。

後置インクリメント式と後置デクリメント式は数値型 (整数型・浮動小数点型) に対して適用し、それぞれ式を評価した後にその値を `+1` または `-1` します。例えば、`index` の値が `3` の場合に式 `index++` の値は `3` と評価され、その後 `index` の値が `4` となります。同様に `index` の値が `3` の場合に式 `index--` の値は `3` と評価され、その後 `index` の値が `2` となります。なお、`index++++` は `(index++)++`、`index----` は `(index--)--` として評価されます。

>後置インクリメント式、後置デクリメント式は Python や Visual Basic にはありません。

### 単項式

単項式 (UnaryExpression) は後置式に次いで 3 番目に高い優先順位で評価される式です。

- 前置インクリメント式 (`++`) ; (例) `++index`
- 前置デクリメント式 (`++`) ; (例) `--index`
- 符号 (`+`/`-`) ; (例) `+num`、`-num`
- ビット反転 (`~`) ; (例) `~bitset`
- 否定 (`!`) ; (例) `!cond`
- キャスト (`(型)`)  ; (例) `(short) intValue`、`(byte) intValue`

前置インクリメント式と前置デクリメント式は数値型 (整数型・浮動小数点型) に対して適用し、それぞれの値を `+1` または `-1` した後に式を評価します。例えば、`index` の値が `3` の場合に式 `++index` の値が `4` となりその値で評価されます。同様に `index` の値が `3` の場合に式 `--index` の値は `2` となりその値で評価されます。なお、`++++index` は `++(++index)`、`----index` は `--(--index)` として評価されます。

符号は数値型 (整数型・浮動小数点型) に対して適用し、式 `+num` を評価すると式 `num` の符号を維持した値、式 `-num` を評価すると式 `num` の符号を反転した値になります。なお、`+ +num`、`- -num` は `+(+num)`、`-(-num)` として評価され、最終的には `(num)`、`(num)` と評価されます。

>`++num`、`--num` と表記するとそれぞれ前置インクリメント式、前置デクリメント式となり、符号の二重適用にはなりません (一次式である前置インクリメント式、前置デクリメント式が優先されるため)。

ビット反転は数値型 (整数型) に対して適用し、値の 2 進数表記について全ビットを反転します。数値型 (浮動小数点型) は単純な 2 進数表記にできないため、ビット反転は適用できません。なお、`~~bitset` は `~(~bitset)` として評価され、最終的には `(bitset)` と評価されます。

否定は論理型の式 (主に後述の関係演算式、等価演算式、条件 AND/OR 演算式) に対して適用します。

>前置インクリメント式、前置デクリメント式は Python や Visual Basic にはありません。Python ではビット反転は `~`、否定は `not` です。Visual Basic ではビット反転と否定はともに `Not` で、文脈により判断されます。C や JavaScript には上記すべての式が用意されています。

キャストはデータ型 (プリミティブ型またはクラス) の変換を行う式です。数値型 (整数型・浮動小数点型) で小さなサイズから大きなサイズに拡大変換する場合と、サブクラスをスーパークラスとして参照する場合には特別な操作は要りませんが、数値型 (整数型・浮動小数点型) で大きなサイズから小さなサイズに縮小変換が可能な場合、スーパークラスでの参照をサブクラスに変える場合には、明示的にデータ型の変換が必要となります。また、数値型 (整数型) と数値型 (浮動小数点型) との間で計算精度を調整するためにキャストを行う場合があります。以下に数値型 (整数型・浮動小数点型) のキャストの例を示します。

```java
int intValue = 10;
byte byteValue = (byte) intValue;  // int 型を byte 型へキャスト

double doubleValue = 5.0;
float floatValue = (float) doubleValue;  // float 型を double 型へキャスト

int x = 3;
int y = 2;
// int 型を double 型へキャストしてから除算
// 結果を double 型で取得したい (= 小数点以下まで計算したい) ための措置
// このパターンのキャストは比較的よく使用するため覚えておくと良い
double d = (double) x / (double) y;
```

なお、`(型) (型) value` は `(型) ((型) value)` として評価されます。

UnaryExpression:
    PreIncrementExpression 
    PreDecrementExpression 
    + UnaryExpression 
    - UnaryExpression 
    UnaryExpressionNotPlusMinus

PreIncrementExpression:
    ++ UnaryExpression

PreDecrementExpression:
    -- UnaryExpression

UnaryExpressionNotPlusMinus:
    PostfixExpression 
    ~ UnaryExpression 
    ! UnaryExpression 
    CastExpression

CastExpression:
    ( PrimitiveType ) UnaryExpression 
    ( ReferenceType {AdditionalBound} ) UnaryExpressionNotPlusMinus 
    ( ReferenceType {AdditionalBound} ) LambdaExpression 

AdditionalBound:
    & InterfaceType

### 乗算式と加算式 (四則演算)

乗算式 (MultiplicativeExpression) は単項式に次いで 4 番目に高い優先順位で評価される式です。

- 乗算式
- 除算式
- 剰余式

>乗算式・除算式・剰余式は C、Python、JavaScript および Visual Basic でサポートされます。ただし、剰余式で浮動小数点数を使用できるのは Java と JavaScript だけです。また、剰余の記号は Visual Basic のみ `Mod` となります。

MultiplicativeExpression:
    UnaryExpression 
    MultiplicativeExpression * UnaryExpression 
    MultiplicativeExpression / UnaryExpression 
    MultiplicativeExpression % UnaryExpression

加算式 (AdditiveExpression) は乗算式に次いで 5 番目に高い優先順位で評価される式です。

- 加算式
- 減算式

>加算式・減算式は C、Python、JavaScript および Visual Basic でサポートされます。加算演算子 `+` は文字列結合にも使用しますが、C では文字列リテラル同士の結合にしか使用できない点、Visual Basic では `&` による文字列結合が推奨される点がそれぞれ異なります。

AdditiveExpression:
    MultiplicativeExpression 
    AdditiveExpression + MultiplicativeExpression 
    AdditiveExpression - MultiplicativeExpression

>括弧、符号、乗算式、加算式の優先順位は、括弧 (1 位) > 符号 (3 位) > 乗算式 (4 位) > 加算式 (5 位) となり、数式における優先順位と一致します。

### シフト演算式

シフト演算式 (ShiftExpression) は加算式に次いで 6 番目に高い優先順位で評価される式です。

>シフト演算式は C、Python、JavaScript に存在し、Visual Basic には存在しません。JavaScript のシフト演算式は Java と同一です。Python は論理シフト `>>>` がなく、すべて算術シフトになります。C は `>>>` 演算子がない代わりに、データ型の符号の有無により算術シフトと論理シフトが切り替わります。

ShiftExpression:
    AdditiveExpression 
    ShiftExpression << AdditiveExpression 
    ShiftExpression >> AdditiveExpression  算術シフト
    ShiftExpression >>> AdditiveExpression 論理シフト

### 関係演算式と等価演算式

関係演算式 (RelationalExpression) はシフト演算式に次いで 7 番目に高い優先順位で評価される式です。

RelationalExpression:
    ShiftExpression 
    RelationalExpression < ShiftExpression 
    RelationalExpression > ShiftExpression 
    RelationalExpression <= ShiftExpression 
    RelationalExpression >= ShiftExpression 
    RelationalExpression instanceof ReferenceType

>関係演算式は C、Python、JavaScript、Visual Basic に存在し、記法・用法も同じです。

等価演算式 (EqualityExpression) は関係演算式に次いで 8 番目に高い優先順位で評価される式です。

EqualityExpression:
    RelationalExpression 
    EqualityExpression == RelationalExpression 
    EqualityExpression != RelationalExpression

>等価演算式は C、Python、JavaScript、Visual Basic に存在しますが、Visual Basic のみ記法が異なり等号 `=`、不等号 `<>` となります。

### ビット演算式 (AND 演算式・OR 演算式・XOR 演算式)

AND 演算式 (AndExpression) は等価演算式に次いで 9 番目に高い優先順位で評価される式です。

AndExpression:
    EqualityExpression 
    AndExpression & EqualityExpression

XOR 演算式 (ExclusiveOrExpression) は AND 演算式に次いで 10 番目に高い優先順位で評価される式です。

ExclusiveOrExpression:
    AndExpression 
    ExclusiveOrExpression ^ AndExpression

OR 演算式 (InclusiveOrExpression) は XOR 演算式に次いで 11 番目に高い優先順位で評価される式です。

InclusiveOrExpression:
    ExclusiveOrExpression 
    InclusiveOrExpression | ExclusiveOrExpression

>ビット演算式は C、Python、JavaScript、Visual Basic に存在しますが、Visual Basic のみ記法が異なり `And`、`Or`、`Xor` となります。

### 条件 AND/OR 演算式

条件 AND 演算式 (ConditionalAndExpression) は OR 演算式に次いで 12 番目に高い優先順位で評価される式です。

ConditionalAndExpression:
    InclusiveOrExpression 
    ConditionalAndExpression && InclusiveOrExpression

条件 OR 演算式 (ConditionalOrExpression) は 条件 AND 演算式に次いで 13 番目に高い優先順位で評価される式です。

ConditionalOrExpression:
    ConditionalAndExpression 
    ConditionalOrExpression || ConditionalAndExpression

>条件 AND/OR 演算式は C、Python、JavaScript、Visual Basic に存在しますが、Visual Basic のみ記法が異なり `And`、`Or` となります。

### 条件演算式 (三項演算子)

条件演算式 (ConditionalExpression) は条件 OR 演算式に次いで 14 番目に高い優先順位で評価される式です。

ConditionalExpression:
    ConditionalOrExpression 
    ConditionalOrExpression ? Expression : ConditionalExpression 
    ConditionalOrExpression ? Expression : LambdaExpression 

>条件演算子 (三項演算子) は C や JavaScript に同じ構文があります。Python は構文が異なり、Visual Basic は `IIf` 関数で代用します。

### 代入演算式

代入演算式 (AssignmentExpression) は最も低い優先順位で評価される式です。代入式には単純代入演算式と複合代入演算式があります。

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

|複合代入演算式|対応する代入式|
|----|----|
|`x *= y`|`x = x * y`|
|`x /= y`|`x = x / y`|
|`x %= y`|`x = x % y`|
|`x += y`|`x = x + y`|
|`x -= y`|`x = x - y`|
|`x <<= y`|`x = x << y`|
|`x >>= y`|`x = x >> y`|
|`x >>= y`|`x = x >>> y`|
|`x &= y`|`x = x & y`|
|`x ^= y`|`x = x ^ y`|
|`x |= y`|`x = x | y`|

>複合代入演算式は C、Python、JavaScript に存在しますが、Python では文脈により異なる意味合いを持つ場合があります。Visual Basic には複合代入演算子は存在しません。








### 4.1.8. 変数と代入式

式の値に名前を付けたものを変数と言います。変数名にはフィールド名と同じキャメルケース (先頭小文字) を用いるのが慣習ですが、フィールド名と比較して短い名前が付けられる傾向にあります。

* <変数型> <変数名>;
* <変数型> <変数名> = <初期値>;  // 変数の初期化を伴う場合
* `final` <変数型> <変数名> = <初期値>;  // 変数を初期化し、変更できないようにする場合 (定数)

変数に値を設定する式を代入式と言います。書式は以下の通りです。

* <変数名> = <式>
* <変数型> <変数名> = <式>  // 変数の初期化を伴う場合

変数への代入や初期化には `=` を用います。この記法は C、JavaScript、Python および Visual Basic と同じです。ただし、変数型については言語により記法が異なります (C は Java と同様ですが、Visual Basic では変数名の後に `As <データ型>` を付加します。JavaScript と Python には変数型の宣言がありません)。

式の値が `boolean`、`int`、`long`、`double` などプリミティブ型の場合は、式の値が変数にコピーされます。式の値がクラスのインスタンスの場合は、そのインスタンスへの参照が変数にコピーされます。`final` が付加された変数を除き、代入式は何度でも (その変数自身に対しても) 行うことができます。

>代入式の再適用は、変数に設定された値が刻一刻と変化するような状態を表すために用いられます。変数型が同じだからと言って意味合いが全く異なる値を変数に再設定することは、処理の流れを読みづらくするだけなので避けてください。1970 年代のプログラミング言語 (Fortran 77 など) では変数名に利用できる文字の種類や長さに制限があり、宣言できる変数の個数に限界があったため変数を再利用するような手段も採られていましたが、Java では変数名に制約がないため変数を再利用するメリットが全くありません。

変数はメソッド内の任意の場所で宣言することができ、宣言した箇所以降メソッドの終了まで有効です。メソッドの引数は初期値が呼び出し元メソッドで設定された変数とみなすことができます。変数はメソッドごとに独立しているため、異なるメソッドで同じ変数名を使用することは問題ありません。

#### 4.1.8.1. 変数と型キャスト

例えば `long` 型の変数/リテラルであっても明らかに `int` 型のサイズに収まることがわかっている場合があります。そのような場合には、`long` 型の変数/リテラルを `int` 型に変換して `int` 型変数に代入することができます。これを型キャストと言います。型キャストの書式は C と同様で、Visual Basic では `CStr`、`CInt`、`CLong` 関数などがこれに相当します。JavaScript と Python はデータ型変換について Java とは異なるアプローチを採るため割愛します。

```
long longValue = 10L;  // 明らかに int 型のサイズに収まる
...
int intValue = (int) longValue;  // 型キャスト; (データ型) 変数/リテラル
```

キャストは `( )` 内に変換先のデータ型を記述し、変数/リテラルの前に付加します。この時の `( )` は「キャスト演算子」と呼ばれることがあります。キャスト演算子は四則演算や比較よりも優先順位が高く設定されています。

`byte`、`char`、`short` などの整数型は、`int` 型よりも小さなサイズであるため、これらの型の変数に整数リテラルを代入する場合には必ず型キャストが発生します。その他、整数同士の除算を行い結果を浮動小数点数として取得したい (例: 3 / 2 の結果として 1.5 を得たい) 場合等に、演算に先立って数値を double 型にキャストします。


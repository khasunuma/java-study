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
- ラベル付き文、assert 文については用途が特殊なため割愛します (少なくとも筆者は使用した試しがありません)。

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

>実際のプログラミングで多用することになる四則演算の式は、単独では式文にすることができません。四則演算の式は代入演算式やメソッド呼び出し式と組み合わせて使うことになります。

## 空文

空文 (EmptyStatement) は、何も処理をしない文です。`;` (セミコロン) だけで表します。

EmptyStatement:
    ;

## ブロック

文を `{ }` で囲んだものをブロック (Block) といいます。ブロックの中に記述できるものは、正確には次の 4 種類です。

- ローカル変数宣言文
- クラス定義
- 文
- ブロック

ブロックは、メソッド本体の処理記述を行う際に使用します。また、後述の if 文、if-else 文、while 文、do 文、for 文の中で使用することもできます。

Block:
    { [BlockStatements] }

BlockStatements:
    BlockStatement {BlockStatement}

BlockStatement:
    LocalVariableDeclarationStatement 
    ClassDeclaration 
    Statement

LocalVariableDeclarationStatement:
    LocalVariableDeclaration ;

LocalVariableDeclaration:
    {VariableModifier} UnannType VariableDeclaratorList

LabeledStatement:
    Identifier : Statement

LabeledStatementNoShortIf:
    Identifier : StatementNoShortIf

ExpressionStatement:
    StatementExpression ;

StatementExpression:
    Assignment 
    PreIncrementExpression 
    PreDecrementExpression 
    PostIncrementExpression 
    PostDecrementExpression 
    MethodInvocation 
    ClassInstanceCreationExpression

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

|言語|10 進数|16 進数|8 進数|2 進数|桁区切り|サフィックス|
|----|----|----|----|----|----|----|
|Java|そのまま|先頭 `0x` `0X`|先頭 `0`|先頭 `0b` `0B`|`_` (アンダースコア)|`l` `L` で `long` 型|
|C|そのまま|先頭 `0x` `0X`|先頭 `0`|N/A|N/A|`l` `L` で `long` 型|
|Visual Basic|そのまま|先頭 `&h` `&H`|先頭 `&o` `&O`|N/A|N/A|`%` で `Integer` 型、`&` で `Long` 型|
|Python|そのまま|先頭 `0x` `0X`|先頭 `0`|先頭 `0b` `0B`|N/A|`l` `L` で `long` 型|
|JavaScript|そのまま|先頭 `0x` `0X`|N/A|N/A|N/A|N/A|

### 浮動小数点数リテラル (FloatingPointLiteral)

浮動小数点数リテラルは 10 進数および 16 進数の表記があります。

#### 10 進数浮動小数点数リテラル

10 進数整数リテラルと同じ数字を使用し、かつ以下のいずれかの条件を満たす場合に、10 進数浮動小数点リテラルとなります。

- `.` (小数点) が存在する
- `e` `E` で始まる指数部を含む

10 進数浮動小数点数リテラルの型は、末尾に `f` `F` が付加された場合は `float` 型、`d` `D` が付加された場合は `double` 型となります。省略時には `double` 型とみなされます。

FloatingPointLiteral:
    DecimalFloatingPointLiteral 
    HexadecimalFloatingPointLiteral
DecimalFloatingPointLiteral:
    Digits . [Digits] [ExponentPart] [FloatTypeSuffix] 
    . Digits [ExponentPart] [FloatTypeSuffix] 
    Digits ExponentPart [FloatTypeSuffix] 
    Digits [ExponentPart] FloatTypeSuffix
ExponentPart:
    ExponentIndicator SignedInteger
ExponentIndicator:
    (one of) 
    e E
SignedInteger:
    [Sign] Digits
Sign:
    (one of) 
    + -
FloatTypeSuffix:
    (one of) 
    f F d D

#### 16 進数浮動小数点数リテラル

16 進数整数リテラルと同じ数字とプリフィクス (`0x` `0X`) を使用し、かつ以下のいずれかの条件を満たす場合に、16 進数の浮動小数点リテラルとなります。

- `.` (小数点) が存在する
- `p` `P` で始まる指数部を含む

16 進数浮動小数点数リテラルの型は、末尾に `f` `F` が付加された場合は `float` 型、`d` `D` が付加された場合は `double` 型となります。省略時には `double` 型とみなされます。

HexadecimalFloatingPointLiteral:
    HexSignificand BinaryExponent [FloatTypeSuffix]
HexSignificand:
    HexNumeral [.] 
    0 x [HexDigits] . HexDigits 
    0 X [HexDigits] . HexDigits
BinaryExponent:
    BinaryExponentIndicator SignedInteger
BinaryExponentIndicator:
    (one of) 
    p P

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

C には文字リテラルおよび文字列リテラルが存在します。表記は Java と同じですが文字列リテラルのデータ型は異なります (C の文字列リテラルは `const char *` など)。Python および JavaScript には文字列リテラルのみが存在し、囲み文字は `" "` (ダブルクォート) と `' '` (シングルクォート) のいずれも使用することができます。Visual Basic も文字列リテラルのみ存在しますが (囲み文字は Java 同様 `" "` (ダブルクォート) のみ)、エスケープ・シーケンスが使用できず代わりに `vbCrLf` などを使用する点が異なります。

### その他のリテラル

#### 論理リテラル (BooleanLiteral)

論理リテラルは論理値を表すリテラルで、キーワード `true` `false` が値となります。また、`boolean` 型の値となります。

>他の言語の論理型と異なり、Java の `boolean` 型は数値型との相互変換ができません。

#### Null リテラル (NullLiteral)

いずれのインスタンスも参照しないことを示すリテラルを Null リテラルといい、キーワード `null` で表します。

## 式

処理の基本単位を「式」といい、式を実行することを「評価する」といいます。式を評価した結果を「値」といい、値はプリミティブ型、何らかのクラス、または `void` (値を持たない) になります。

値に名前を付けたものを「変数」と呼びます。

### 一次式

Java において最も単純な式を一次式 (Primary) と呼びます。一次式は他のすべての式に優先して評価されます。一次式には以下のものが挙げられます。

- 配列生成式ではない一次式
  - リテラル
  - クラス・リテラル (7 章)
  - `this` 参照
  - TypeName . `this` 参照
  - 括弧 ; `( 式 )`
  - インスタンス生成式
  - フィールド・アクセス式
  - 配列アクセス式 (10 章)
  - メソッド呼び出し式
  - メソッド参照式 (12 章)
- 配列生成式 (10 章)

Primary:
    PrimaryNoNewArray 
    ArrayCreationExpression

PrimaryNoNewArray:
    Literal 
    ClassLiteral 
    this 
    TypeName . this 
    ( Expression ) 
    ClassInstanceCreationExpression 
    FieldAccess 
    ArrayAccess 
    MethodInvocation 
    MethodReference

Literal:
    IntegerLiteral 
    FloatingPointLiteral 
    BooleanLiteral 
    CharacterLiteral 
    StringLiteral 
    NullLiteral

IntegerLiteral:
    DecimalIntegerLiteral 
    HexIntegerLiteral 
    OctalIntegerLiteral 
    BinaryIntegerLiteral
DecimalIntegerLiteral:
    DecimalNumeral [IntegerTypeSuffix]
HexIntegerLiteral:
    HexNumeral [IntegerTypeSuffix]
OctalIntegerLiteral:
    OctalNumeral [IntegerTypeSuffix]
BinaryIntegerLiteral:
    BinaryNumeral [IntegerTypeSuffix]
IntegerTypeSuffix:
    (one of) 
    l L

UnicodeInputCharacter:
    UnicodeEscape 
    RawInputCharacter
UnicodeEscape:
    \ UnicodeMarker HexDigit HexDigit HexDigit HexDigit
UnicodeMarker:
    u {u}
HexDigit:
    (one of) 
    0 1 2 3 4 5 6 7 8 9 a b c d e f A B C D E F
RawInputCharacter:
    any Unicode character

LineTerminator:
    the ASCII LF character, also known as "newline" 
    the ASCII CR character, also known as "return" 
    the ASCII CR character followed by the ASCII LF character
InputCharacter:
    UnicodeInputCharacter but not CR or LF

WhiteSpace:
    the ASCII SP character, also known as "space" 
    the ASCII HT character, also known as "horizontal tab" 
    the ASCII FF character, also known as "form feed" 
    LineTerminator

ClassInstanceCreationExpression:
    UnqualifiedClassInstanceCreationExpression 
    ExpressionName . UnqualifiedClassInstanceCreationExpression 
    Primary . UnqualifiedClassInstanceCreationExpression

UnqualifiedClassInstanceCreationExpression:
    new [TypeArguments] ClassOrInterfaceTypeToInstantiate ( [ArgumentList] ) [ClassBody]

ClassOrInterfaceTypeToInstantiate:
    {Annotation} Identifier {. {Annotation} Identifier} [TypeArgumentsOrDiamond]

TypeArgumentsOrDiamond:
    TypeArguments 
    <>

ArgumentList:
    Expression {, Expression}

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

FieldAccess:
    Primary . Identifier 
    super . Identifier 
    TypeName . super . Identifier

MethodInvocation:
    MethodName ( [ArgumentList] ) 
    TypeName . [TypeArguments] Identifier ( [ArgumentList] ) 
    ExpressionName . [TypeArguments] Identifier ( [ArgumentList] ) 
    Primary . [TypeArguments] Identifier ( [ArgumentList] ) 
    super . [TypeArguments] Identifier ( [ArgumentList] ) 
    TypeName . super . [TypeArguments] Identifier ( [ArgumentList] )

ArgumentList:
    Expression {, Expression}

### 後置式

後置式 (PostfixExpression) は一次式に次いで 2 番目に高い優先順位で評価される式です。

- 変数名
- 後置インクリメント式 (`++`)
- 後置デクリメント式 (`--`)

PostfixExpression:
    Primary 
    ExpressionName 
    PostIncrementExpression 
    PostDecrementExpression

PostIncrementExpression:
    PostfixExpression ++

PostDecrementExpression:
    PostfixExpression --

### 単項式

単項式 (UnaryExpression) は後置式に次いで 3 番目に高い優先順位で評価される式です。

- 前置インクリメント式 (`++`)
- 前置デクリメント式 (`++`)
- 符号 (`+`/`-`)
- ビット反転 (`~`)
- 否定 (`!`)
- キャスト (`(型)`) 

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

MultiplicativeExpression:
    UnaryExpression 
    MultiplicativeExpression * UnaryExpression 
    MultiplicativeExpression / UnaryExpression 
    MultiplicativeExpression % UnaryExpression

加算式 (AdditiveExpression) は乗算式に次いで 5 番目に高い優先順位で評価される式です。

AdditiveExpression:
    MultiplicativeExpression 
    AdditiveExpression + MultiplicativeExpression 
    AdditiveExpression - MultiplicativeExpression

>括弧、符号、乗算式、加算式の優先順位は、括弧 (1 位) > 符号 (3 位) > 乗算式 (4 位) > 加算式 (5 位) となり、数式における優先順位と一致します。

### シフト演算式

シフト演算式 (ShiftExpression) は加算式に次いで 6 番目に高い優先順位で評価される式です。

ShiftExpression:
    AdditiveExpression 
    ShiftExpression << AdditiveExpression 
    ShiftExpression >> AdditiveExpression 
    ShiftExpression >>> AdditiveExpression

### 関係演算式 (大なり・小なり と等価演算式 (等号・不等号)

関係演算式 (RelationalExpression) はシフト演算式に次いで 7 番目に高い優先順位で評価される式です。

RelationalExpression:
    ShiftExpression 
    RelationalExpression < ShiftExpression 
    RelationalExpression > ShiftExpression 
    RelationalExpression <= ShiftExpression 
    RelationalExpression >= ShiftExpression 
    RelationalExpression instanceof ReferenceType

等価演算式 (EqualityExpression) は関係演算式に次いで 8 番目に高い優先順位で評価される式です。

EqualityExpression:
    RelationalExpression 
    EqualityExpression == RelationalExpression 
    EqualityExpression != RelationalExpression

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

### 条件 AND/OR 演算式

条件 AND 演算式 (ConditionalAndExpression) は OR 演算式に次いで 12 番目に高い優先順位で評価される式です。

ConditionalAndExpression:
    InclusiveOrExpression 
    ConditionalAndExpression && InclusiveOrExpression

条件 OR 演算式 (ConditionalOrExpression) は 条件 AND 演算式に次いで 13 番目に高い優先順位で評価される式です。

ConditionalOrExpression:
    ConditionalAndExpression 
    ConditionalOrExpression || ConditionalAndExpression

### 条件演算式 (三項演算子)

条件演算式 (ConditionalExpression) は条件 OR 演算式に次いで 14 番目に高い優先順位で評価される式です。

ConditionalExpression:
    ConditionalOrExpression 
    ConditionalOrExpression ? Expression : ConditionalExpression 
    ConditionalOrExpression ? Expression : LambdaExpression 

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







### 4.1.6. 算術式

数値同士について、算術演算 (四則演算) がサポートされています。異なる数値型同士の演算の場合、より大きいデータ型 (int < long < double) に合わせられます。文法は C と同一で、JavaScript、Python および Visual Basic とも多くが共通しています (Visual Basic では一部記法が異なります)。

* (加算) x ＋ y :  `x + y`
* (減算) x － y :  `x - y`
* (乗算) x × y : `x * y`
* (除算) x ÷ y : `x / y`
* (剰余) x mod y : `x % y` (Visual Basic と異なり `MOD` ではない)

また、インクリメント (+1)・デクリメント (-1) 演算が存在します。なお、インクリメント・デクリメント演算は Python と Visual Basic には存在しません。

* インクリメント (前置) : `++x`
* インクリメント (後置) : `x++`
* デクリメント (前置) : `--x`
* デクリメント (後置) : `x--`

前置と後置では式の評価タイミングが異なり、前置は評価前に+1/-1演算が行われ、後置は評価後に+1/-1演算が行われます。

(例) x = 3, y = 2 の時

* `++x * y` の結果は、式の値 = 8、x = 4、y = 2 (計算前に+1)
* `x++ * y` の結果は、式の値 = 6、x = 4、y = 2 (計算後に+1)

### 4.1.7. 論理式

数値同士について比較演算がサポートされています。演算結果は boolean 型になります。

* (未満) x ＜ y : `x < y`
* (以下) x ≦ y : `x <= y`
* (等しい) x ＝ y : `x == y` (Pascal、Ada と異なり `:=` ではない)
* (等しくない) x ≠ y : `x != y` (Pascal、Ada、Visual Basic と異なり `<>` ではない)
* (以上) x ≧ y : `x >= y`
* (より大きい) x ＞ y : `x > y`

比較演算には `&&` (AND)、`||` (OR)、`!` (NOT) を使用することができ、基本的な等式・不等式を表現することができます (Visual Basic では記法が異なり、それぞれ `And`、`Or`、`Not` となります)。

#### 4.1.7.1. 条件演算子 (三項演算子)

`x ? y : z` で論理式 x が真のとき y、偽のとき z を値とする「条件演算子 (三項演算子)」が用意されています。ほとんどの場合 `if (x) y; else z;` と同じですが、式は置けるが文は置けない個所で条件分岐をしたい場合に用いられます。条件演算子 (三項演算子) は C および JavaScript と共通であり、Python にもそれに相当する構文があります。また、Visual Basic では `IIf` 関数が相当します。

条件演算子は使い方次第でソースコードの可読性を向上させる場合も悪化させる場合もあるため、注意が必要です。

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

### 4.1.9. その他の式

new 演算子でクラスのインスタンスを生成した結果 (例: `new BigDecimal(150)`) や、メンバ演算子 `.` でクラスのフィールドやメソッドを参照した結果 (例: `s.toUpperCase()`) も、式として扱われます。

(例) new 演算子によるインスタンスの生成式 (代入式との組み合わせ)
```
// インスタンス生成式: new String("Hello, world") → 結果を変数 s に代入
// "Hello, world" は String クラスのコンストラクタの「実引数」(→呼び出しにより仮引数に設定される)
String s = new String("Hello, world");
```

(例) `.` 演算子によるメソッド参照式 (代入式との組み合わせ)
```
// s は String クラスのインスタンスで 5 文字以上と仮定 (具体的には上記の例の結果)
// メソッド参照式: s.subSequence(0, 5) → 結果を prefix に代入
// 0, 5 は subSequence() メソッドの「実引数」(→呼び出しにより仮引数に設定される)
String prefix = s.subSequence(0, 5);
```

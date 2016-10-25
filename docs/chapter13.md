# 13. Date and Time API

Date and Time API は Java SE 8 で導入された API です。Java には最初のバージョンから日付と時刻を表す `java.util.Date` クラスを用意しており、JDK 1.1 からはワールド・クロックに対応した `java.util.Calendar` クラスなどを追加しましたが、当初から使い勝手に難があることで知られていました。Date and Time API は古く使いづらい `java.util.Date` や `java.util.Calendar` を置き換えるため 8 年の歳月をかけて開発された新世代の日付・時刻 API です。

>Java SE 6/7 であっても、Date and Time API の開発者が提供する互換ライブラリ "ThreeTen backport" を追加することでほとんどの機能を利用することが可能です。

この章では、Date and Time API の導入資料として高い評価を受けている参考文献 [4] をベースに解説を進めます。参考文献 [4] は Date and Time API が日付と時刻を表現する国際規格である「ISO 8601」をモデリングした API である点に注目し、最初に ISO 8601 の基本事項に触れてから本題である Date and Time API の解説に入ります。解説はやや長くなりますが、基礎固めも含めた内容であるとご理解ください。

## 13.1. 「時」に関する基礎事項

Date and Time API は最も身近で、かつ複雑な「時」を扱う API です。最初の一歩として、まずは「時」に関する知識を整理することから始めましょう。

### 13.1.1. 「時」とは？

時の基本単位は「秒」と定められています。 「秒」は７つある SI 基本単位（他の単位の組み合わせでは表せない単位）の１つとして定められています (その他の 6 つは、長さの 'm'、質量の 'kg'、電流の 'A'、温度の 'K'、光度の 'cd'、分子量の 'mol' です)。 「秒」を基本として、時刻は次のように定義されています。

- 1 分 = 60 秒
- 1 時間 = 60 分 = 3,600 秒
- 1 日 = 24 時間 = 86,400 秒

もっとも、歴史的には逆で、１日の半分を占める日の出～日没（または日没～日の出）を 12 等分したものを 1 時間、 1 時間の 60 分の 1 が 1 分、1 分の 60 分の 1 が 1 秒として定義され、それが数千年に渡って使用されてきました。

>分や秒に 100 等分でなく 60 等分が採用されているのは、現在の「時」を作り上げた古代メソポタミア文明のバビロニア王朝において 60 進法が日常的に使われていたためと言われています。また、1 日を 10 でなく 12 等分としているのは、インドで 0 の概念が発見される前に「時」が成立していたことによります。 0 が発見されるまで 10 進法はマイナーなもので、古くから 2、3、4、6 のいずれでも割り切れる 12 が位取りとして世界各所で好まれていました。

現在のように「秒」を時の基準とするようになったのは 20 世紀半ば以降です。 観測技術が進み 1 日の長さが厳密には一定でないことが判明したため、その代わりとして秒を超高精度に刻むことが可能な原子時計 (TAI = 国際原子時) を基準とするようになりました。しかし、原子時計はあまりに高精度のため天体観測から得られる体感的な「秒」と数十秒単位での差が生じてしまうことから、現在では原子時計の時刻に「うるう秒」を加えて天体観測から得られる結果に近づけた UTC (協定世界時) を基準として用いるようになっています。ただし、一般的なコンピュータの内蔵時計では「うるう秒」補正が必要になるほどの高精度が得られないことから、Date and Time API では「うるう秒」に対する考慮は行いません。

>現在の「秒」の定義は、セシウム 133 原子を基底状態 (エネルギーが最小) から励起状態 (エネルギーが最大) に変化させる電磁波の周波数の 9,192,631,770 分の 1 となっています。実はこの電磁波の周波数が 9,192,631,770 Hz であり、そこから逆算したものです。原子を基底状態から励起状態に変化させる電磁波の周波数は原子の種類ごとに異なっていて、しかもわずかな誤差があると励起状態にはならないことが判明しています。セシウム 133 は励起状態に変化させるための周波数が厳密にわかっている原子であり、原子時計のコアとして用いられているものでもあります。

### 13.1.2. 地方時と時差

現在、世界の標準時として使用されているのは UTC (協定世界時) で、これは本初子午線 (経度 0 度) において太陽が南中した時刻を 12 時 (正午) とする GMT (グリニッジ標準時) と概ね一致します。

私たちが使用している時刻は、例えば日本では JST (日本標準時) であり、UTC と異なることが少なくありません。太陽が南中した時刻をその土地の 12 時とする時刻を地方時と言い、JST は地方時の１つです。JST は UTC よりも 9 時間進んでいるため、UTC で 0 時ちょうどの時、JST では 9 時となります。さらにアメリカ西海岸の地方時である PST は UTC より 8 時間遅れているため、UTC で 0 時ちょうどの時、PST では前の日の 16 時 (夏時間の場合は 15 時) となります。これが時差と呼ばれるものです。

ISO 8601 では UTC からのずれで時差を表現しますが、Java では同じ時差を持つ地域であるタイムゾーンを Time Zone Database (tz database) としてデータベース化して利用しています。tz database は時差に加え夏時間の情報、さらにはこれらの履歴をも保持しています。tz database の原本はインターネットの標準化団体である ICANN と IANA による管理の下で常に更新が行われており、Java 実行環境 (JRE) のアップデート時に最新版が反映されるようになっています。Date and Time API では ISO 8601 に基づく時差と tz database が定めるタイムゾーンの双方を利用できるようになっています。

## 13.2. ISO 8601

### 13.2.1. ISO 8601 について

Date and Time API は、日付と時刻の表記に関する国際標準である ISO 8601 をモデリングした API です。ISO 8601 はコンピュータ上で確実に取り扱うことを第一義として定められています。ISO 8601 では一般に用いられているグレゴリオ暦を用います (一部のキリスト教会で用いるユリウス暦ではないことに注意してください)。日本では ISO 8601 を翻訳した上で日本独自の和暦表記の規則を追加した JIS X 0301 という日本標準規格を制定しており、その他の国々も ISO 8601 を元にした国内標準規格を定めています。 また、ISO 8601 のサブセットとしてインターネット標準として用いられている RFC 3339 があります。

ここで注意したいことは、ISO 8601 はコンピュータ上で普及している Unix 時刻と互換性がないということです。 言い換えると、Date and Time API は従来の `java.util.Data` や `java.util.Calendar` とは互換性がないことを意味します。 意外と見落としがちな事実なので注意してください。

ISO 8601 の詳細については以下の解説記事を参照してください。

- [ISO 8601 -- Date and Time API の基礎理論(1)](http://www.coppermine.jp/docs/programming/2015/06/iso8601-1.html)
- [ISO 8601 -- Date and Time API の基礎理論(2)](http://www.coppermine.jp/docs/programming/2015/06/iso8601-2.html)

### 13.2.2. 時刻の表現

ISO 8601 における時刻の表現は、良く見慣れた時・分・秒の並びです。いずれも必ず数字 2 桁で表現します (1 桁の場合はゼロパディングを行い 2 桁とします)。区切りには `:` を用います。

>ISO 8601 では、時刻・日付などの情報は必ず**桁数**が定められており、情報の種類はすべて桁数で決定します。区切り文字は一部の例外を除き、見やすさを考慮して定められています。ただし、Date and Time API では区切り文字も含めた形での表現を標準形として扱うため注意が必要です。

|情報      |表現    |例|
|---------|--------|--------|
|時・分・秒|hh:mm:ss|14:30:15|
|時・分    |hh:mm   |14:30|
|時        |hh      |14|
|時・分・秒・秒未満|hh:mm:ss.<u>s</u>|14:30:15.250|

### 13.2.3. タイムゾーンを含む時刻の表現

タイムゾーン情報を含む時刻を表現するには、先に挙げた時刻の表現に加え、サフィックス: UTC からの時差 (±hh:mm) を付加します。なお、UTC の場合はサフィックス 'Z' とします。

|情報           |表現           |例|
|---------------|--------------|--------------------------|
|時・分・秒＋時差|hh:mm:ss±hh:mm|14:30:45+09:00 (Asia/Tokyo)|
|               |              |21:30:45-08:00 (America/Los_Angeles)|
|               |              |05:30:45Z (UTC)|

### 13.2.4. 日付の表現

日付の表現には、暦日付 (calendar date)、年間通算日 (ordinal date)、暦週日付 (week date) の 3 種類が用意されています。良く用いられるのは暦日付です。

|種類     |情報        |表現       |例          |
|---------|-----------|-----------|------------|
|暦日付    |年・月・日  |YYYY-MM-DD|2015-07-11   |
|年間通算日|年・年始からの通算日|YYYY-DDD|2015-192|
|暦週日付  |年・週・曜日|YYYY-Www-D |2015-W29-6  |

### 13.2.5. 日付の短縮表現

日付にはその一部のみを表すための短縮表現が用意されています。

|情報  |表現    |例|
|------|-------|-------|
|年・月|YYYY-MM|2015-07|
|年    |YYYY   |2015   |
|月・日|--MM-DD|--07-11|

### 13.2.6. 日時

日時 (日付 + 時刻) は以下のように表現します。

- 日付表現と時刻表現を 'T' で繋ぎます。
- 必要に応じて、時差 (タイムゾーン) を付加することができます。

|情報                      |表現                      |例|
|--------------------------|-------------------------|------------------------|
|年・月・日・時・分・秒      |YYYY-MM-DDThh:mm:ss      |2015-07-11T14:45:30|
|年・月・日・時・分・秒・時差|YYYY-MM-DDThh:mm:ss±hh:mm|2015-07-10T21:45:30-08:00|

### 13.2.7. 時間長

時間長 (duration) は、例えば「1 時間」「3 分間」といった時間の長さを表すものです。以下のように日付の表現と時間の表現が定められており、両者を 'T' で繋ぐことで日付・時間の時間長を表すことができます。時間長の主な目的は、次節で取り上げる時間間隔を表現することです。なお、Date and Time API の `Duration` は次の時間間隔 (period of Time) の時間部の表現であり、ISO 8601 の duration = 時間長ではないことに注意します。

|情報                     |表現         |例|
|---------------------|-------------|----------------|
|時間量 (日付)         |nYnMnD       |1Y3M22D         |
|時間量 (時刻)         |nHnMnS       |9H30M45S        |
|時間量 (日付・時刻)|nYnMnDTnHnMnS|1Y3M22DT9H30M45S|

### 13.2.8. 時間間隔

任意の 2 つの日付または時刻の間隔を示すものが時間間隔 (period / period-of-time / time-interval) といいます。時間間隔の表現には以下の 4 種類があります (日付の例を示しますが、時間または日付・時間についても同様です)。

1. YYYY-MM-DD/YYYY-MM-DD (開始日付/終了日付)
2. YYYY-MM-DD/PnYnMnD (開始日付/開始日付からの時間長)
3. PnYnMnD/YYYY-MM-DD (終了日付までの時間長/終了日付)
4. PnYnMnD (時間長)

第 4 の書式のみ、開始日付・終了日付のいずれも含まないため、特定の日付に結び付かない時間間隔となります。なお、Date and Time API の `Period` は内部表現として第 4 の書式のみを採用しており、`Period` が日付部のみの時間間隔、`Duration` が時間部のみの時間間隔を表します。

### 13.2.9. 週と曜日の定義

ISO 8601 では、週と曜日を以下のように定義しています。

|番号 |曜日             | 
|:--:|:----------------|
|1   |月曜日 (Monday)|
|2   |火曜日 (Tuesday)|
|3   |水曜日 (Wednesday)|
|4   |木曜日 (Thursday)|
|5   |金曜日 (Friday)|
|6   |土曜日 (Saturday)|
|7   |日曜日 (Sunday)|

- 1 週は 7 日からなります。週の最初の日は**月曜日**です。また 1 年は 52 週または 53 週からなります。
- 年の第 1 週は、その年の最初の**木曜日**が含まれる週となります。
- 第 1 週は前年末の何日かを、また最終週は翌年初の何日かを、それぞれ含む場合があります。そのような性格から、ISO 8601 では週を厳密な日時表現に使わないことを推奨しています。

## 13.3. Date and Time API の基本

### 13.3.1. パッケージ

Date and Time API は、`java.time` パッケージとそのサブパッケージの 5 パッケージから構成されており、クラス数は 69 (Java SE 8) となります。ただし、そのすべてを使用しなければならないわけではなく、実際の使用頻度には大きな偏りがあります。使用するクラスはアプリケーションによっても異なってきますが、多くの場合は `java.time` 配下の基本的なクラスを中心に 20 程度のクラスを使用することになるでしょう。

|パッケージ         |説明           |頻度|
|------------------|---------------|---------|
|`java.time`       |基本的なクラス  |高       |
|`java.time.format`|フォーマット関連|部分的に高|
|`java.time.chrono`|暦サポート      |部分的に高|
|`java.time.temporal`|低水準 API    |低       |
|`java.time.zone`  |低水準 API      |低       |

### 13.3.2. 日付・時刻クラス

Date and Time API には 6 つの基本的な日付・時刻クラスが用意されており、日付のみ、時刻のみ、日付と時刻、タイムゾーン表現の有無などによるバリエーションが存在します。最も基本となるクラスは `LocalDate` クラスで、日付のみでタイムゾーンを持たない分だけ、小回りが利くように作られています。実際のアプリケーション開発でもおそらく最も使用頻度の高いクラスとなるでしょう。

|クラス           |日付/時刻|タイムゾーン表現|対応する ISO 8601 書式|
|:--------------:|:-------:|:------------:|-------------------------|
|`LocalDate`     |Date     |N/A           |YYYY-MM-DD|
|`LocalDateTime` |Date/Time|N/A           |YYYY-MM-DDThh:mm:ss|
|`LocalTime`     |Time     |N/A           |hh:mm:ss|
|`OffsetDateTime`|Date/Time|`ZoneOffset`  |YYYY-MM-DDThh:mm:ss±hh:mm|
|`OffsetTime`    |Time     |`ZoneOffset`  |hh:mm:ss±hh:mm|
|`ZonedDateTime` |Date/Time|`ZoneId`      |N/A|

### 13.3.3. ファクトリ・メソッド

日付・時刻クラスには、いずれも共通したファクトリ・メソッドが用意されており、それを用いてインスタンスを取得します。メソッド名もクラス間で統一されており、いずれか 1 つのクラスについて覚えれば、他のクラスについても概ね適用できるように工夫されています。以下に代表的なファクトリ・メソッドの一覧を示します。

|メソッド|説明                             |例                         |
|:-----:|---------------------------------|---------------------------|
|`of-`  |フィールドから日付/時刻を生成する   |`LocalDate.of(2015, 7, 11)`|
|`now`  |現在時刻から日付/時刻を生成する     |`LocalDate.now()`|
|`from` |他の日付/時刻から日付/時刻を生成する|`LocalDate.from(LocalDateTime.now())`|
|`parse`|日付文字列から日付/時刻を生成する   |`LocalDate.parse("2015-07-11")`|

### 13.3.4. "of" methods (examples)

ファクトリ・メソッド `of` は日付・時刻クラスのインスタンスを取得する最も基本的な手段です。以下にサンプルコードを示しますが、フィールドに適切な値を設定するだけなので、特に難しいところはないでしょう。

```java
// LocalDate : 引数に年・月・日を指定
LocalDate.of(2015, 7, 11);

// LocalDateTime : 引数に年・月・日・時・分・秒・秒以下を指定
LocalDateTime.of(2015, 7, 11, 13, 30, 45, 250);

// LocalTime : 引数に時・分・秒・秒以下を指定
LocalTime.of(13, 30, 45, 250);

// ZoneOffset : 時差を表す (ここでは時差 +09:00)
// ZoneOffset クラスの詳細については後述
ZoneOffset offset = ZoneOffset.ofHours(9);

// OffsetDateTime : 引数に年・月・日・時・分・秒・秒以下・時差を指定
OffsetDateTime.of(2015, 7, 11, 13, 30, 45, 250, offset);

// OffsetTime : 引数に時・分・秒・秒以下・時差を指定
OffsetTime.of(13, 30, 45, 250, offset);

// ZoneId : タイムゾーンを表す (ここでは "Asia/Tokyo" = 東京)
// ZoneId クラスの詳細については後述
ZoneId zoneId = ZoneId.of("Asia/Tokyo");

// ZonedDateTime : 引数に年・月・日・時・分・秒・秒以下・タイムゾーンを指定
ZonedDateTime.of(2015, 7, 11, 13, 30, 45, 250, zoneId);
```

### 13.3.5. 変換メソッド

日付・時刻クラスのメソッドのうち、`at-` で始まるものはフィールドを追加し、`to-` で始まるものはフィールドを変換または切り捨てます。戻り値のクラスは操作後のフィールドに合わせて変更されます。これらのメソッドは日付・時刻クラス間の相互変換に使用されます。日付・時刻クラスとそれらの相互変換メソッドのうち主要なものについて以下に示します。

Date and Time API はすべて変更不可能 (イミュータブル) なインスタンスとなっているため、`at-`/`to-` メソッドの戻り値は新しく生成されたインスタンスであり、変換元のインスタンスには影響がありません。

|変換元クラス        |変換メソッド (操作)                                 |変換先クラス        |
|--------------------|----------------------------------------------|--------------------|
|`LocalDate`        |`atTime` (時刻を追加)                            |`LocalDateTime` |
|`LocalDate`        |`atTime` (時刻/時差を追加)                     |`OffsetDateTime`|
|`LocalTime`        |`atDate` (日付を追加)                            |`LocalDateTime` |
|`LocalTime`        |`atOffset` (時差を追加)                          |`OffsetTime`       |
|`LocalDateTime`  |`atZone` (ゾーンを追加)                        |`ZonedDateTime`|
|`LocalDateTime`  |`toLocalDate` (日付以外を切り捨て)        |`LocalDate`         |
|`LocalDateTime`  |`toLocalTime` (時刻以外を切り捨て)        |`LocalTime`        |
|`OffsetTime`       |`atDate` (日付を追加)                            |`OffsetDateTime`|
|`OffsetTime`       |`toLocalTime` (時刻以外を切り捨て)        |`LocalTime`        |
|`OffsetDateTime`|`toZonedDateTime` (時差をゾーンに変換)|`ZonedDateTime`|
|`OffsetDateTime`|`toOffsetTime` (時刻/時差以外を切り捨て)|`OffsetTime`       |
|`OffsetDateTime`|`toLocalDate` (日付以外を切り捨て)         |`LocalDate`        |
|`OffsetDateTime`|`toLocalTime` (時刻以外を切り捨て)        |`LocalTime`        |
|`ZonedDateTime`|`toLocalDateTime`(ゾーンを切り捨て)     |`LocalDateTime`  |
|`ZonedDateTime`|`toOffsetDateTime` (ゾーンを時差に変換)|`OffsetDateTime` |
|`ZonedDateTime`|`toLocalDate` (日付以外を切り捨て)        |`LocalDate`         |
|`ZonedDateTime`|`toLocalTime` (時刻以外を切り捨て)        |`LocalTime`        |

### 13.3.6. フィールド値の取得と設定

フィールド値の取得メソッドは `get-` または `get` (汎用)、設定メソッドは `with-` または `with` (汎用) です。ここでは `LocalDate` クラスの例を示しますが、その他の日付・時刻クラスでも共通です。

Date and Time API はすべて変更不可能 (イミュータブル) なインスタンスとなっているため、`with-`/`with` メソッドの戻り値はフィールド値を変更したコピーとなります。

|メソッド|説明|例|
|:----:|--------|--------|
|`get-`|フィールドの値を取得する|`LocalDate.now().getYear()`|
| | |`LocalDate.now().get(YEAR)`|
|`with-`|フィールドの値を変更し、コピーを返す|`LocalDate.now().withYear(2016)`|
| | |`LocalDate.now().with(2016, YEAR)`|

汎用の `get` および `with` は、引数に取得/設定するフィールドを `ChronoField` 列挙型で指定します。

|ChronoField|取得メソッド名|設定メソッド名|
|:------------:|:--------:|:--------:|
|`YEAR`|`getYear`|`withYear`|
|`MONTH_OF_YEAR`|`getMonthValue`|`withMonth`|
|`DAY_OF_MONTH`|`getDayOfMonth`|`withDayOfMonth`|
|`DAY_OF_WEEK`|`getDayOfWeek`|N/A|
|`HOUR_OF_DAY`|`getHour`|`withHour`|
|`MINUTE_OF_HOUR`|`getMinute`|`withMinute`|
|`SECOND_OD_MINUTE`|`getSecond`|`withSecond`|
|`NANO_OF_SECOND`|`getNano`|`withNano`|

### 13.3.7. 日付・時刻の加算・減算

日付・時刻の加算メソッドは `plus-` または `plus` (汎用)、減算メソッドは `minus-` または `minus` (汎用) です。ここでは `LocalDate` クラスの例を示しますが、その他の日付・時刻クラスでも共通です。

Date and Time API はすべて変更不可能 (イミュータブル) なインスタンスとなっているため、`plus-`/`plus` および `minus-`/`minus` メソッドの戻り値は日付・時刻を加算または減算したコピーとなります。

|メソッド|説明|例|
|:----:|--------|--------|
|`plus-`|フィールドの値を加算し、コピーを返す|`LocalDate.now().plusDays(7)`|
| | |`LocalDate.now().plus(7, DAYS)`|
|`minus-`|フィールドの値を減算し、コピーを返す|`LocalDate.now().minusDays(7)`|
| | |`LocalDate.now().minus(7, DAYS)`|

汎用の `plus` および `minus` は、加算・減算の単位を `ChronoUnit` 列挙型で指定します。

|ChronoUnit|加算メソッド名|減算メソッド名|
|:--------:|:--------:|:--------:|
|`YEARS`|`plusYears`|`minusYears`|
|`MONTHS`|`plusMonths`|`minusMonths`|
|`DAYS`|`plusDays`|`minusDays`|
|`WEEKS`|`plusWeeks`|`minusWeeks`|
|`HOURS`|`plusHours`|`minusHours`|
|`MINUTES`|`plusMinutes`|`minusMinutes`|
|`SECONDS`|`plusSeconds`|`minusSeconds`|
|`NANOS`|`plusNanos`|`minusNanos`|

### 13.3.8. 日付・時刻の比較

日付・時刻の比較には、`isBefore`、`isEqual`、`isAfter` といったメソッドを使用します。以下に例を示しますが、`isBefore` や `isAfter` は比較対象が同一の日付・時刻の場合に `false` を返しますので、必要に応じて `isEqual` を組み合わせます。

|メソッド|例|
|:----:|--------|
|`isBefore`|`today.isBefore(yesterday)` ; false|
| |`today.isBefore(today)` ; false|
| |`today.isBefore(tomorrow)` ; true|
|`isEqual`|`today.isEqual(yesterday)` ; false|
| |`today.isEqual(today)` ; true|
| |`today.isEqual(tomorrow)` ; false|
|`isAfter`|`today.isAfter(yesterday)` ; true|
| |`today.isAfter(today)` ; false|
| |`today.isAfter(tomorrow)` ; false|

なお、`LocalTime` など一部のクラスでは `isEqual` ではなく `equals` メソッドのオーバーライドとなっていますが、使用方法は同じです。

### 13.3.9. Format/Parse methods

`LocalDate` をはじめとする日付・時刻クラスには、以下のような文字列との相互変換 (フォーマット／パース）メソッドを持っています。既定のフォーマッタはいずれも ISO 8601 形式であることを想定してます。

|メソッド|説明|
|--------|--------|
|`toString`|既定のフォーマッタを使用して文字列化する|
|`format(DateTimeFormatter f)`|指定したフォーマッタを使用して文字列化する|
|`parse(String s)`|既定のフォーマッタを使用して文字列から日付/時刻を生成する (ファクトリ・メソッド)|
|`parse(String s, DateTimeFormatter f)`|指定したフォーマッタを使用して文字列から日付/時刻を生成する (ファクトリ・メソッド)|

一方で、日本国内では日付の表現としてスラッシュ ('/') 区切りがよく使われており、ISO 8601 形式とは異なることからそのままでは使用できません。そこで、フォーマッタを独自に定義する必要があります。

フォーマッタは `DateTimeFormatter` クラスで表現され、インスタンスは以下の 3 通りの方法で取得することができます。一覧の下に行くほど柔軟性は高く、特に `DateTimeFormatterBuilder` は `DateTimeFormatter` のすべてを制御可能な非常に強力なものです。ただし、一般的には `ofPattern` ファクトリ・メソッドでほぼ対応することができるため、`DateTimeFormatterBuilder` を使用することはまずありません。この章でもそれを踏まえて `DateTimeFormatterBuilder` の解説は割愛します (参考文献 [2] には `DateTimeFormatterBuilder` の使用例が掲載されているため、必要に応じて参照してください)。

1. `DateTimeFormatter` の定義済みパターンから選択する
2. `DateTimeFormatter` の `ofPattern` ファクトリ・メソッドを使用する *e.g.* ofPattern("uuuu/MM/dd")
3. `DateTimeFormatterBuilder` クラスを使用して `DateTimeFormatter` を構築する。

`DateTimeFormatter` の定義済みパターンには以下のようなものがあります。この中には日付・時刻クラスの既定値も含まれています。

|フィールド|リゾルバ|説明
|----------------|------|------------------------|
|`BASIC_ISO_DATE`|`STRICT`|時差なし ISO 日付・基本形式 (例: "20111203")|
|`ISO_DATE`|`STRICT`|時差任意の ISO 日付 (例: "2011-12-03"、"2011-12-03+01:00")|
|`ISO_DATE_TIME`|`STRICT`|時差任意の日付/時刻 (例: "2011-12-03T10:15:30"、"2011-12-03T10:15:30+01:00"、"2011-12-03T10:15:30+01:00[Europe/Paris]")|
|`ISO_INSTANT`|`STRICT`|UTC インスタント (例: "2011-12-03T10:15:30Z")|
|`ISO_LOCAL_DATE`|`STRICT`|時差なし ISO 日付、`LocalDate` 既定値 (例: "2011-12-03")|
|`ISO_LOCAL_DATE_TIME`|`STRICT`|時差なし ISO 日付/時刻、`LocalDateTime` 既定値 (例: "2011-12-03T10:15:30")|
|`ISO_LOCAL_TIME`|`STRICT`|時差なし ISO 時刻、`LocalTime` 既定値 (例: "10:15"、"10:15:30")|
|`ISO_OFFSET_DATE`|`STRICT`|時差あり ISO 日付 (例: "2011-12-03+01:00")|
|`ISO_OFFSET_DATE_TIME`|`STRICT`|時差あり ISO 日付/時刻、`OffsetDateTime` 既定値 (例: 「2011-12-03T10:15:30+01:00」など)|
|`ISO_OFFSET_TIME`|`STRICT`|時差あり ISO 時刻、`OffsetTime` 既定値 (例: 「10:15+01:00」、「10:15:30+01:00」など)|
|`ISO_ORDINAL_DATE`|`STRICT`|時差なし ISO 年間通算日 (例: 「2012-337」など)|
|`ISO_TIME`|`STRICT`|時差任意 ISO 時間 (例: 「10:15」、「10:15:30」、「10:15:30+01:00」など)。
|`ISO_WEEK_DATE`|`STRICT`|時差なし ISO 暦週日付 (例: 「2012-W48-6」など)。
|`ISO_ZONED_DATE_TIME`|`STRICT`|時差・ゾーンあり日付/時刻、`ZonedDateTime` 既定値 (例: 「2011-12-03T10:15:30+01:00[Europe/Paris]」など)|
|`RFC_1123_DATE_TIME`|`SMART`|RFC-1123 日付/時刻 (例: "Tue, 3 Jun 2008 11:05:30 GMT")|

定義済みパターンにない書式を使用する場合には、`ofPattern` ファクトリ・メソッドの引数にパターンを渡して、インスタンスを生成します。パターン文字列は書式の一部となる文字に加え、以下のパターン文字 (日付・時刻のフィールドに置き換えられる) を含めることができます。

|文字|意味|表現|例|
|:--:|----|----|----|
|`G`|紀元 (era)|文字列|AD; Anno Domini; A|
|`u`|年 (year)|年|2004; 04|
|`y`|紀元の年 (year-of-era)|年|2004; 04|
|`D`|年の日 (day-of-year)|数値|189|
|`M`/`L`|年の月 (month-of-year)|数値/文字列|7; 07; Jul; July; J|
|`d`|月の日 (day-of-month)|数値|10|

|`Q`/`q`|四半期 (quarter-of-year)|数値/文字列|3; 03; Q3; 3rd quarter|
|`Y`|週基準の年 (week-based-year)|年|1996; 96|
|`w`|週基準の年の週 (week-of-week-based-year)|数値|27|
|`W`|月の週 (week-of-month)|数値|4|
|`E`|曜日 (day-of-week)|文字列|Tue; Tuesday; T|
|`e`/`c`|ローカライズされた曜日 (localized day-of-week)|数値/文字列|2; 02; Tue; Tuesday; T|
|`F`|月の週 (week-of-month)|数値|3|

|`a`|午前・午後 (am-pm-of-day)|文字列|PM|
|`h`|時 (1-12) (clock-hour-of-am-pm (1-12))|数値|12|
|`K`|時 (0-11) (hour-of-am-pm (0-11))|数値|0|
|`k`|時 (1-24) (clock-hour-of-am-pm (1-24))|数値|0|

|`H`|時 (0-23) (hour-of-day (0-23))|数値|0|
|`m`|分 (minute-of-hour)|数値|number|30|
|`s`|秒 (second-of-minute)|数値|55|
|`S`|秒の小数部 (fraction-of-second)|小数部|978|
|`A`|日のミリ秒 (milli-of-day)|数値|1234|
|`n`|ナノ秒 (nano-of-second)|数値|987654321|
|`N`|日のナノ秒 (nano-of-day)|数値|1234000000|

|`V`|タイムゾーン ID (time-zone ID)|ゾーン ID|America/Los_Angeles; Z; -08:30|
|`z`|タイムゾーン名 (time-zone name)|ゾーン名|Pacific Standard Time; PST|
|`O`|ローカライズされた時差 (localized zone-offset)|オフセット O|GMT+8; GMT+08:00; UTC-08:00;|
|`X`|時差、ゼロの場合は 'Z' (zone-offset 'Z' for zero)|オフセット X|Z; -08; -0830; -08:30; -083015; -08:30:15;|
|`x`|時差 (zone-offset)|オフセット x|+0000; -08; -0830; -08:30; -083015; -08:30:15;|
|`Z`|時差 (zone-offset)|オフセット Z|+0000; -0800; -08:00;|

|`p`|パディング (pad next)|パディング修飾子|1|

|`'`|エスケープ (escape for text)|delimiter| |
|`''`|シングルクォート (single quote)|literal|'|
|`[`|オプションのセクション開始 (optional section start)| |
|`]`|オプションのセクション終了 (optional section end)| |
|`#`|予約 (reserved for future use)| |
|`{`|予約 (reserved for future use)| |
|`}`|予約 (reserved for future use)| |

パターン文字の桁数により、表現が変わってきます。

- テキスト: 4 未満 = 短縮形式、4 = フル形式、5 = 縮小形式 (`L`、`c`、`q` は、スタンドアロン形式のテキスト・スタイル)。
- 数値: 1 = 最小桁数・パディングなし、それ以外 = 出力幅・ゼロパディング (ただし `c`、`F` は 1、`d`、`H`、`h`、`K`、`k`、`m`、`s` は 2 以下、`D` は 3 以下)。
- 数値/テキスト: 3 以上はテキスト扱い。2 以下は数値扱い。
- 小数: ナノ秒フィールド (1～9)。
- 年: 2 = 2 桁の縮小形式、符号の出力は、4 未満 (ただし 2 以外) = 負の年のみ、それ以外 = パディング幅を超える場合。
- ゾーンID: 2 のみ。
- ゾーン名: 1～3 = 短い名前、4 = 完全。
- オフセット X/オフセット x: 1 = 分がゼロのときは時 ("+01")、分がゼロでないときは時・分 ("+0130")、2 = 時・分コロンなし ("+0130")、3 = 時・分コロン付き ("+01:30")、4 = 時・分・秒コロンなし ("+013015")、5 = 時・分・秒コロン付き ("+01:30:15")。オフセットがゼロの場合、オフセット X は"Z"、オフセット x は "+00"、"+0000"、"+00:00"。
- オフセット O: 1 = ローカライズされたオフセットの短縮形式 ("GMT+8")、4 = フル形式 ("GMT+08:00")
- オフセット Z: 1、2、3 = 時・分コロンなし ("+0130")、オフセットがゼロの場合 "+0000"。4 = ローカライズされたオフセットのフル形式 (O の4文字と等価)。5 = 時・分・秒コロン付き、オフセットがゼロの場合 "Z"。
- パディング修飾子: 直後にスペース n 文字

リゾルバは、フォーマッタの動作モードの 1 つで、パースを行う際の妥当性チェックのレベルについて規定するものです。`ResolverStyle` 列挙型として定義され、`LENIENT` (非厳密)、`SMART` (スマート)、`STRICT` (厳密) の 3 種類があります。`DateTimeFormatter` 自体の既定値は `SMART` です。それぞれの特徴は以下の通りです。

- LENIENT (非厳密) : 無効な値であっても何らかの形で処理する。
- SMART (スマート) : フィールドに応じて `LENIENT` と `STRICT` を切り替える (独自の処理をする場合もある)。
- STRICT (厳密) : 無効な値は拒否され、必ず有効な値となる。

リゾルバの変更には `DateTimeFormatter` クラスの `withResolverStyle` メソッドを使用します。

```java
// リゾルバを STRICT にしてフォーマッタを生成する
DateTimeFormatter formatter = DateTimeFormatter.withResolverStyle(ResolverStyle.STRICT).ofPattern("uuuu/MM/dd");
```

`STRICT` はパターン文字列自体も厳密にチェックします。以下のコードはパターン文字列 "yyyy/MM/dd" のフォーマッタを 2 つ生成するコードですが、`formatter1` の生成には成功するものの、`formatter2` を生成しようとするとエラーとなってしまいます。なぜ `formatter2` の生成だけエラーになってしまうのでしょうか？

```java
// リゾルバを既定値 (SMART) でフォーマッタを生成する
DateTimeFormatter formatter1 = DateTimeFormatter.ofPattern("yyyy/MM/dd");

// リゾルバを STRICT にしてフォーマッタを生成する → エラー！
DateTimeFormatter formatter2 = DateTimeFormatter.withResolverStyle(ResolverStyle.STRICT).ofPattern("yyyy/MM/dd");
```

`DateTimeFormatter` のパターン文字で「年」を表すものとして、`y` と `u` の 2 種類があります。仕様では、`y` は時代 (era) を表す `G` との組み合わせが必須となっており、年を単独で表すには `u` を使う必要があります。ただし、リゾルバが `LENIENT` の場合はパターンに `y` が存在する場合に `G` が含まれていなくても無視する動作となり、`SMART` の場合も `y` に関する既定の動作は `LENIENT` と同一になります。しかし、`STRICT` の場合はパターンを厳密にチェックするため、`y` が存在する場合に `G` が含まれていないと無効なパターンと判断してエラーとなるのです。

## 13.4. 高度なトピック

### 13.4.1. Instant と Clock

Date and Time API が扱う日付・時刻表現には、人間向けの表現とコンピュータ向けの内部表現があります。ここまで見てきたのは、主に人間向けの表現でした。

人間向けの表現は、`LocalDate` をはじめとする日付・時刻クラスを中心に構成され、これらは ISO 8601 の表現に準拠するように定義されていました。人間向けの表現のうち、まだ取り上げていないものは、2 つの日付または時刻の間隔を表現する方法です。Date and Time API では 2 つの日付の間隔を `Period` クラスで、2 つの時刻の間隔を `Duration` クラスでそれぞれ表します。これらの文字列表現は ISO 8601 の日付間隔に準拠します。例えば 1 年 2 ヶ月 と 3 日間を表す `Period` は "P1Y2M3D"、15 時間 30 分と 45 秒を表す `Duration` は "PT15H30M45D" となります。

>ISO 8601 では、時間量および日付量を durarion、時間間隔および日付間隔を period と定義していますが、Date and Time API では `Duration` は時間間隔、`Period` は日付間隔を表します。本 API における数少ない ISO 8601 との相違点であるため注意してください。

一方、コンピュータ向けの内部表現には、原点、時刻、時間間隔 (時間) および時間軸によって構成されます。それぞれ `Instant.EPOCH`、`Instant`、`Duration`、`Clock` の各クラス (および定数) にて表現されます。起点である `Instant.EPOCH` は、Unix 時刻のエポック (起点) と同じ 1970-01-01T00:00:00Z として定義されています。時刻は `Instant` クラスで表現され、ナノ秒の精度を持ちます。2 つの `Instant` の間隔は `Duration` クラスで表現します (これは人間向け表現と同じです)。

なお、`Instant` クラスは `java.util.Date` との相互変換を行うための唯一のインタフェースでもあります。

>内部表現の構成要素は、ニュートン力学で扱う時刻・時間の概念に類似しています。

ここまで取り上げた内容を下表にまとめます。

|要素|人間向け表現|内部表現|
|----|--------|--------|
|原点|N/A|`Instant.EPOCH`|
|日付・時刻|`LocalDate` 等|`Instant`|
|日付・時間間隔|`Period`、`Duration`|`Duration`|
|時間軸|N/A|`Clock`|

この節の最後の話題として、`Clock` の詳細を取り上げましょう。

先の説明では `Clock` は時間軸を表現するクラスとしました。`Clock` をより正確な表現をするならば、呼び出しの度に `Instant` を返すオブジェクトです。システム標準の `Clock` は、呼び出したときの既定のタイムゾーンに基づく現在時刻を表す `Instant` を返します。以下に `Clock` クラスが提供する `Instant` 取得メソッドを示します。

|メソッド|引数|説明|
|--------|--------|--------|
|`system`|`ZoneId`|指定したタイムゾーンに基づく現在時刻を表す `Instant` を返す|
|`systemDefaultZone`|N/A|既定のタイムゾーンに基づく現在時刻を表す `Instant` を返す (`LocalDateTime.now()` 等が用いる)|
|`systemUTC`|N/A|UTC に基づく現在時刻を表す `Instant` を返す|
|`fixed`|`Instant, ZoneId`|常に引数で指定した `Instant` を返す (Fixed Clock)|

 4 つ目の `fixed` は、常に同じ `Instant` を返す特殊な `Clock` で、Fixed Clock と呼ばれているものです。Fixed Clock は現在時刻が常に一定となる性質を利用して、内部で現在時刻を取得して処理に用いるようなプログラムの単体テストを実施するのに適しています。

Fixed Clock の簡単な使用例を以下に示します。

```java
// 常に返す Instant
Instant instant = ... ;

// Fixed Clock
Clock clock = Clock.fixed(instant, ZoneId.systemDefaultZone());

// Fixed Clock を使用 → dateTime は常に同じ値となる
LocalDateTime dateTime = LocalDateTime.now(clock);
```

### 13.4.2. ZoneId と ZoneOffset

#### 13.4.2.1. ZoneId とは？

`ZoneId` クラスはタイムゾーンに付けられた ID あるいはタグのようなものです。`ZoneId` のインスタンスは Date and Time API が内部で保持しているタイムゾーンに関する情報 (`ZoneRules` クラスなどで構成される) と関連付けられています。そのため、タイムゾーンに関する情報を取得するためには、キーとなる `ZoneId` を指定する必要があります。

`ZoneId` 自身は抽象クラスであり、具象クラスは `ZoneRegion` クラス (パッケージ・プライベート) と `ZoneOffset` クラスになります。どちらの具象クラスが用いられるかは、`ZoneId` の取得方法によって変わってきます。

`ZoneOffset` クラスは、UTC からの時差を指定するタイプの `ZoneId` です。前述の通り、`OffsetDateTime` や `OffsetTime` のインスタンス取得時に時差を指定するために用いられるクラスですが、`ZoneId` のサブクラスであるため実は `ZonedDateTime` のインスタンス取得にも使用できます。

`ZoneRegion` クラスは、`ZoneOffset` 以外のケースで用いられる `ZoneId` であり、"Asia/Tokyo" のような tz database 名を指定した場合にはこちらが用いられます。パッケージ・プライベートとして宣言されているため直接操作することはありませんが、`ZoneOffset` との対比のため意識しておいた方が良いでしょう。通常は `ZoneId` として `ZonedDateTime` インスタンス取得に使用されます。

#### 13.4.2.2. ZoneId の取得方法

`ZoneId` にはいくつかのファクトリ・メソッドが用意されており、それを用いてインスタンスを取得するようになっています。ファクトリ・メソッドの一覧を以下に示します。

|メソッド|引数|説明|
|----|----|----|
|`of`|`String`|固定オフセットまたは地理的地域を指定してインスタンスを取得する|
|`of`|`String, Map`|上と同じだが、最初にエイリアスの `Map` を適用する|
|`systemDefault`|N/A|システム既定のタイムゾーンを取得する|
|`from`|`TemporalAccessor`|例えば `ZonedDateTime` などからタイムゾーンを抽出する|

基本的には第 1 の書式である `of(String)` ファクトリ・メソッドを使用します。引数の指定方法にいくつかのバリエーションがあり、それによって取得されるインスタンスの具象クラスが変わってきます。

1. 固定オフセット (例: "+09:00"、"Z") を指定した場合 → `ZoneOffset` のインスタンス
2. 地理的地域 (例: "Asia/Tokyo"、"America/Los_Angeles") を指定した場合 → `ZoneRegion` のインスタンス

これ以外のパターンはエラーになります。例えば "JST" などの 3 文字コードは使用できません。3 文字コードを使用するには、エイリアスの `Map` を指定する第 2 の書式である `of(String, Map)` を使用する必要があります。既知の 3 文字コードについては `ZoneId.SHORT_IDS` として事前定義されているため、`of("JST", ZoneId.SHORT_IDS)` のように使用します。

システムの既定値を取得する場合には、`ZoneId.systemDefault()` メソッドを使用します。

#### 13.4.2.3. ZoneOffset の取得方法

`ZoneOffset` には、`ZoneId` のファクトリ・メソッドに加えて、以下に示すような時差を指定するいくつかのファクトリ・メソッドが用意されています (通常はこちらを使用します)。また、UTC を表す定数 `ZoneOffset.UTC` が定義されています。

|メソッド|引数|説明|
|----|----|----|
|`ofHours`|`int`|時差 (時) を指定する|
|`ofHoursMinutes`|`int, int`|時差 (時・分) を指定する|
|`ofHoursMinutesSeconds`|`int, int, int`|時差 (時・分・秒) を指定する|
|`ofTotalSeconds`|`int`|時差の合計値 (秒) を指定する|

>現在、世界で設定されている時差はすべて時または時・分のいずれかで表現可能です。

#### 13.4.2.4. OffsetDateTime vs. ZonedDateTime

Date and Time API には、時差・タイムゾーンを含む日付・時刻クラスとして `OffsetDateTime` と `ZonedDateTime` の 2 種類が用意されています。一見すると重複しているように感じられる仕様ですが、これらが独立したクラスとして存在しているのには理由があります。

- `OffsetDateTime` は ISO 8601 に準拠したクラスです。一方で `ZonedDateTime` は ISO 8601 には準拠しない利便のためのクラスです。
- `ZonedDateTime` は夏時間や過去のタイムゾーン改訂に対応できるクラスです。一方で `OffsetDateTime` にはそのような機能はありません。
- タイムゾーンはそれを管轄する国や地域の都合により変更されることがあるため、`ZonedDateTime` はその影響を受けます。一方で UTC からの時差は変化しないため、UTC からの時差に依存する `OffsetDateTime` はタイムゾーン変更の影響を受けません。

`OffsetDateTime` と `ZonedDateTime` には以上のようなトレードオフがあるため、状況に応じて使い分けることが肝心です。

### 13.4.3. TemporalAdjuster

#### 13.4.3.1. Temporal オブジェクトと TemporalAdjuster

`Temporal` オブジェクトとは、これまで見てきた `LocalDate`、`LocalDateTime`、`LocalTime`、`OffsetDateTime`、`OffsetTime`、`ZonedDateTime` と、年を表す `Year` と年月を表す `YearMonth` の総称であり、`Temporal` インタフェースの実装であるという共通点があります。`TemporalAdjuster` は、`Temporal` オブジェクトを調整あるいは変更するためのものです。その目的は、日付・時刻に対する操作を `Temoral` オブジェクトというデータ構造と `TemporalAdjuster` というアルゴリズムに分離することです。

`TemporalAdjuster` は、例えば日付操作を次のように定義します。

- 起点日の月の最初の日を求める (起点日: 2015-03-07 → 2015-03-01)
- 起点日の次の月曜日を求める (起点日: 2015-03-07 → 2015-03-09)
- 起点日の月の最後の日を求める (起点日: 2015-03-07 → 2015-03-31)

`TemporalAdjuster` がない場合は、起点日ごとに月や曜日の情報を参照しながら該当する日を算出する必要がありましたが、`TemporalAdjuster` があればアプリケーションは起点日のみ渡すことで細かな日付計算から解放されます。

#### 13.4.3.2. TemporalAdjuster の使い方

`Temporal` オブジェクトはすべてファクトリ・メソッド `from` を持ちます。その引数に `TemporalAdjuster` を指定することで、`TemporalAdjuster` 適用後の値を求めることができます。以下のサンプルコードを参照してください。

```java
// 次の月曜日を求める TemporalAdjuster
TemporalAdjuster nextMondayAdjuster = ... ;

// 今日
LocalDate today = LocalDate.now();

// 次の月曜日
LocalDate nextMonday = today.from(nextMondayAdjuster);
```

#### 13.4.3.3. TemporalAdjusters ユーティリティ

Date and Time API には、良く使用する `TemporalAdjuster` を `TemporalAdjusters` ユーティリティ・クラスに事前定義しています。`TemporalAdjuster` の実装は難易度の高い仕事ですが、出来合いの `TemporalAdjuster` を使用するだけなら簡単です。

|メソッド|引数|説明|
|---------|--------|---------|
|`dayOfWeekInMonth`|`int, DayOfWeek`|序数形式の曜日を持つ同じ月の新しい日付を返す、月の曜日アジャスタを返します。|
|`firstDayOfMonth`|N/A|月の最初の日を求める|
|`firstDayOfNextMonth`|N/A|翌月の最初の日を求める|
|`firstDayOfNextYear`|N/A|翌年の最初の日を求める|
|`firstDayOfYear`|N/A|年の最初の日を求める|
|`firstInMonth`|`DayOfWeek`|その曜日を持つ、月の最初の日を求める|
|`lastDayOfMonth`|N/A|月の最後の日を求める|
|`lastDayOfYear`|N/A|年の最後の日を求める|
|`lastInMonth`|`DayOfWeek`|その曜日を持つ、月の最後の日を求める|
|`next`|`DayOfWeek`|次の曜日 (同日の場合は翌週) を求める|
|`nextOrSame`|`DayOfWeek`|次の曜日 (同日の場合はその日) を求める|
|`previous`|`DayOfWeek`|前の曜日 (同日の場合は前週) を求める|
|`previousOrSame`|`DayOfWeek`|前の曜日 (同日の場合はその日) を求める|

冒頭に挙げた日付操作の例は、すべて `TemporalAdjusters` ユーティリティ・クラスを用いて実現可能です。以下のサンプルコードを参照してください。

```java
// 起点日 (2015-03-07)
LocalDate today = LocalDate.of(2015, 3, 7);

// 起点日の月の最初の日 (2015-03-01)
LocalDate firstDay = today.from(TemporalAdjusters.firstDayOfMonth());

// 起点日の次の月曜日 (2015-03-09)
LocalDate nextMonday = today.from(TemporalAdjusters.next(DayOfWeek.MONDAY));

// 起点日の月の最後の日 (2015-03-31)
LocalDate lastDay = today.from(TemporalAdjusters.lastDayOfMonth());
```

### 13.4.4. 暦のサポート

#### 13.4.4.1. 暦フレームワーク

Date and Time API では、ISO 8601 以外のローカルな暦として、和暦 (日本)、民国紀元 (台湾)、仏暦 (タイ) およびヒジュラ暦 (イスラム圏) を用意しています。また、それ以外の暦をユーザーが定義できるように暦フレームワークを整備し、提供しています。暦フレームワークは、サポートする暦ごとに `Chronology`、`Era`、`ChronoLocalDate` といった構成要素が存在します。

- `Chronology` : 暦そのものを表現します。その暦の `ChronoLocalDate` インスタンスを生成するためのメソッドを持ち、その暦における `ChronoField` の定義域を保持します。
- `Era` : 暦の紀元を表現します。ほとんどの暦では紀元前と紀元後だけを持つ列挙型として定義されます。和暦のみ各元号に対応する値を持つクラスとして定義されます。
- `ChronoLocalDate` : 暦における日付を表現します。基本的には紀元・年・月・日の各フィールドを持ちます。

標準でサポートする暦における暦フレームワークの構成要素を以下に示します。

|暦|`Chronology`|`Era`|`ChronoLocalDate`|
|--------|----------------|--------|----------------|
|ISO 8601|`IsoChronology`|`IsoEra`|`LocalDate`|
|和暦 (日本)|`JapaneseChronology`|`JapaneseEra`|`JapaneseDate`|
|民国紀元 (台湾)|`MinguoChronology`|`MinguoEra`|`MinguoDate`|
|仏暦 (タイ)|`ThaiBuddhistChronology`|`ThaiBuddhistEra`|`ThaiBuddhistDate`|
|ヒジュラ暦 (イスラム圏)|`HijrahChronology`|`HijrahEra`|`HijrahDate`|

これまで扱ってきた `LocalDate` クラスも、暦フレームワークの一部をなしています (ISO 8601 として)。

ある暦の `ChronoLocalDate` の `from` メソッドは、他の暦の `ChronoLocalDate` を設定することで、暦の変換を行えるようになっています。この機能を利用して `LocalDate` (ISO 8601 の `ChronoLocalDate`) を他の暦に変換するサンプルを以下に示します。

```java
LocalDate today = LocalDate.of(2014, 3, 21);
System.out.println(today);
System.out.println(JapaneseDate.from(today));
System.out.println(MinguoDate.from(today));
System.out.println(ThaiBuddhistDate.from(today));
System.out.println(HijrahDate.from(today));
```

上記の実行結果は以下のようになるはずです。上記の例の基準日である 2014 年 3 月 21 日は、和暦で表現すると平成 26 年 3 月 21 日です。その点に注目して実行結果を見てください。

```
2014-03-21
Japanese Heisei 26-03-21
Minguo ROC 103-03-21
ThaiBuddhist BE 2557-03-21
Hijrah-umalqura AH 1435-05-20
```

>Date and Time API の拡張ライブラリである Threeten Extra では、Java SE API のサイズの関係で割愛されたローカル暦が収録されています。ユリウス暦 (グレゴリオ暦以前の標準的な暦)、コプト暦 (エジプトで使用されていたキリスト教系の暦)、IFRS 会計年度、ディスコルディア暦 (Linux の `ddate` コマンドで表示されるジョークの暦)、いくつかの 13 の月を持つ暦などです。暦フレームワークの柔軟性を示す例と言えるでしょう。

暦フレームワークは柔軟で強力である反面、アプリケーションに複数の暦が持ち込まれ、日付処理が不必要に複雑化する可能性があります。また、ISO 8601 は異なるシステム間で日付・時刻を受け渡しする方法として国際標準化されていますが、ローカル暦は Date and Time API の暦フレームワーク独自のものであり、システム間での受け渡しには不向きです。こうしたことから、ローカル暦の使用はアプリケーション内の必要最小限にとどめ、原則として ISO 8601 に準拠した日付・時刻の処理を行うのが確実と言えるでしょう。

#### 13.4.4.2. 和暦

和暦における `ChronoLocalDate` は `JapaneseDate` クラスとなります。`JapaneseDate` はいくつかのファクトリ・クラスでインスタンスを取得します。主なファクトリ・メソッドを以下に示します。

|メソッド名|引数|説明|
|----|--------|--------|
|`now`|N/A|現在日時から和暦の日付を取得する|
|`of`|`JapaneseEra, int, int, int`|元号・年・月・日から和暦の日付を取得する|
|`from`|`TemporalAccessor`|他の暦の日付から和暦の日付を取得する|

`JapaneseEra` は元号を表し、明治 = `JapaneseEra.MEIJI`、大正 = `JapaneseEra.TAISHO`、昭和 = `JapaneseEra.SHOWA`、平成 = `JapaneseEra.HEISEI` と定義されています。

`JapaneseDate.toString()` メソッドにより "Japanese Heisei 26-03-21" のような形式の文字列表現を取得できます。和暦の表現は JIS X 0301 規格にて "NYY.MM.DD" (N:元号) のように定められており、残念ながら `JapaneseDate` の標準的な書式は JIS 規格に適合していません。これを解決するには独自に `DateTimeFormatter` を定義する必要があります。以下のサンプルコードを参照してください。

```java
// JIS X 0301 準拠の DateTimeFormatter
DateTimeFormatter japaneseFormat = DateTimeFormatter.ofPattern("GGGGGyy.MM.dd");

// 和暦日付 (任意)
JapaneseDate date = JapaneseDate.of(JapaneseEra.HEISEI, 26, 3, 21);

// NYY.MM.DD 形式で標準出力へ表示
System.out.println(date.format(japaneseFormat));
```

## 13.5. Date and Time API のまとめ

Date and Time API を一言で表すなら、「日付と時刻の標準規格 ISO 8601 をモデリングした API」です。 クラス数は少なくありませんが、一つひとつの基本的な使い方は難しくありません。

日付・時刻に関する演算が充実しており、それは旧来の `java.util.Data` や `java.util.Calendar と比べると一目瞭然です。 特に `TemporalAdjuster` は、例えば「次の土曜日」のように抽象的な日付を算出できる強力な機能です。

また、多数の拡張ポイントを備えていることも特徴です。 暦サポートクラスを収めた `java.time.chrono` パッケージを見て、その柔軟性を確認してください。

Date and Time API は初学者にとって敷居の高い API の一つとみなされています。 しかし、手順を踏んで学べば決して難しいものではありません。 日付や時刻といった日常にあるものを表現している分だけイメージはしやすいはずです。以下に学習指針を示しますので、まずはこの通りに進めてみてください。

1. ISO 8601 (または JIS X 0301) を学ぶ。
2. LocalDate クラスの使い方をマスターする。
3. なぜ Local/Offset/Zoned の３種類のクラスが存在するのかを知る。
4. 自分達の仕事で重点的に使う範囲を絞り込む。
5. あとはトライ・アンド・エラー！
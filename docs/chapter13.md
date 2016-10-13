# 13. Date and Time API

>【バージョン】 Date and Time API は Java SE 8 で導入された API です。Java SE 6/7 では Date and Time API の開発者が提供する互換ライブラリ "ThreeTen backport" を追加することでほとんどの機能を利用することが可能です。

## 13.1. 「時」に関する基礎事項

### 13.1.1. 「時」とは？

時の基本単位は「秒」と定められています。 「秒」は７つある SI 基本単位（他の単位の組み合わせでは表せない単位）の１つとなっています。 「秒」を基本として、時刻は次のように定義されています。

- 1 分 = 60 秒
- 1 時間 = 60 分 = 3,600 秒
- 1 日 = 24 時間 = 86,400 秒

もっとも、歴史的には逆で、１日の半分を占める日の出～日没（または日没～日の出）を 12 等分したものを 1 時間、 1 時間の 60 分の 1 が 1 分、1 分の 60 分の 1 が 1 秒とされ、数千年に渡って使用されてきました。

>分や秒に 100 等分でなく 60 等分が採用されているのは、現在の「時」を作り上げた古代メソポタミア文明のバビロニア王朝において 60 進法が日常的に使われていたためと言われています。また、1 日を 10 でなく 12 等分としているのは、インドで 0 の概念が発見される前に「時」が成立していたことによります。 0 が発見されるまで 10 進法はマイナーなもので、古くから 2、3、4、6 のいずれでも割り切れる 12 が位取りとして世界各所で好まれていました。

>現在のように「秒」を時の基準とするようになったのは 20 世紀半ば以降です。 観測技術が進み 1 日の長さが厳密には一定でないことがわかり、代わりに秒を超高精度に刻むことが可能な原子時計を基準とすることにしました。原子時計に関する詳細は割愛しますが、天体観測の結果より桁違いに精度が高く体感的な時刻とかけ離れてしまうことから、現在では原子時計の時刻に「うるう秒」を加えて天体観測の結果に近づけた UTC (協定世界時) が基準として用いられています。

>Date and Time API では、うるう秒は考慮しません（仕様検討時には考慮するモードもありました）。

### 13.1.2. 地方時と時差

現在、世界の標準時として使用されているのは UTC (協定世界時) で、これは本初子午線 (経度 0 度) において太陽が南中した時刻を 12 時 (正午) とする GMT (グリニッジ標準時) と概ね一致します。

私たちが使用している時刻は、例えば日本では JST (日本標準時) であり、UTC と異なることが少なくありません。太陽が南中した時刻をその土地の 12 時とする時刻を地方時と言い、JST は地方時の１つです。JST は UTC よりも 9 時間進んでいるため、UTC で 0 時ちょうどの時、JST では 9 時となります。さらにアメリカ西海岸の地方時である PST は UTC より 8 時間遅れているため、UTC で 0 時ちょうどの時、PST では前の日の 16 時 (夏時間の場合は 15 時) となります。これが時差と呼ばれるものです。

ISO 8601 では UTC からのずれで時差を表現しますが、Java では同じ時差を持つ地域であるタイムゾーンを Time Zone Database (tz database) としてデータベース化して利用しています。tz database は時差に加え夏時間の情報、さらにはこれらの履歴をも保持しています。tz database の原本はインターネットの標準化団体である ICANN と IANA による管理の下で常に更新が行われており、Java 実行環境 (JRE) のアップデート時に最新版が反映されるようになっています。Date and Time API では ISO 8601 に基づく時差と tz database が定めるタイムゾーンの双方を利用できるようになっています。

## 13.2. ISO 8601

### 13.2.1. ISO 8601 について

Date and Time API は、日付と時刻の表記に関する国際標準である "ISO 8601" をモデリングした API です。ISO 8601 はコンピュータ上で確実に取り扱うことを第一義として定められています。 ISO 8601 は一般に用いられているグレゴリオ暦を用います (一部のキリスト教会で用いるユリウス暦ではないことに注意してください)。日本では ISO 8601 を翻訳した上で日本独自の和暦表記の規則を追加した "JIS X 0301" という日本標準規格を制定しており、その他の国々も ISO 8601 ベースの標準規格を定めています。 また、ISO 8601 のサブセットとしてインターネット標準として用いられている RFC 3339 があります。

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

### 13.2.7. 日付と時間の量 (Duration)

|情報        |表現         |例|
|-----------|-------------|----------------|
|日付量      |nYnMnD       |1Y3M22D         |
|時間量      |nHnMnS       |9H30M45S        |
|日付・時間量|nYnMnDTnHnMnS|1Y3M22DT9H30M45S|

### 13.2.8. 日付間隔 (Period)

1. YYYY-MM-DD/YYYY-MM-DD (start/end)
2. YYYY-MM-DD/PnYnMnD (start/duration)
3. PnYnMnD/YYYY-MM-DD (duration/end)
4. PnYnMnD (duration)

### 13.2.9. 週と曜日の定義

|番号 |曜日             | 
|:--:|:----------------|
|1   |月曜日 (Monday)|
|2   |火曜日 (Tuesday)|
|3   |水曜日 (Wednesday)|
|4   |木曜日 (Thursday)|
|5   |金曜日 (Friday)|
|6   |土曜日 (Saturday)|
|7   |日曜日 (Sunday)|

- 1 週は 7 日からなります。また 1 年は 52 週または 53 週からなります。
- 年の第 1 週は、その年の最初の**木曜日**が含まれる週となります。
- 第 1 週は前年末の何日かを、また最終週は翌年初の何日かを、それぞれ含む場合があります。そのような性格から、ISO 8601 では週を厳密な日時表現に使わないことを推奨しています。

## 13.3. Date and Time API の基本

### 13.3.1. パッケージ

|パッケージ         |説明           |頻度|
|------------------|---------------|---------|
|`java.time`       |基本的なクラス  |高       |
|`java.time.format`|フォーマット関連|部分的に高|
|`java.time.chrono`|暦サポート      |部分的に高|
|`java.time.temporal`|低水準 API    |低       |
|`java.time.zone`  |低水準 API      |低       |

### 13.3.2. Date and Time classes

|クラス           |日付/時刻|タイムゾーン表現|対応する ISO 8601 書式|
|:--------------:|:-------:|:------------:|-------------------------|
|`LocalDate`     |Date     |N/A           |YYYY-MM-DD|
|`LocalDateTime` |Date/Time|N/A           |YYYY-MM-DDThh:mm:ss|
|`LocalTime`     |Time     |N/A           |hh:mm:ss|
|`OffsetDateTime`|Date/Time|`ZoneOffset`  |YYYY-MM-DDThh:mm:ss±hh:mm|
|`OffsetTime`    |Time     |`ZoneOffset`  |hh:mm:ss±hh:mm|
|`ZonedDateTime` |Date/Time|`ZoneId`      |N/A|

### 13.3.3. Factory methods

|メソッド|説明                             |例                         |
|:-----:|---------------------------------|---------------------------|
|`of-`  |フィールドから日付/時刻を生成する   |`LocalDate.of(2015, 7, 11)`|
|`now`  |現在時刻から日付/時刻を生成する     |`LocalDate.now()`|
|`from` |他の日付/時刻から日付/時刻を生成する|`LocalDate.from(LocalDateTime.now())`|
|`parse`|日付文字列から日付/時刻を生成する   |`LocalDate.parse("2015-07-11")`|

### 13.3.4. "of" methods (examples)

- `LocalDate.of(2015, 7, 11)`
- `LocalDateTime.of(2015, 7, 11, 13, 30, 45, 250)`
- `LocalTime.of(13, 30, 45, 250)`
- `OffsetDateTime.of(2015, 7, 11, 13, 30, 45, 250, ZoneOffset.ofHours(9))`
- `OffsetTime.of(13, 30, 45, 250, ZoneOffset.ofHours(9))`
- `ZonedDateTime.of(2015, 7, 11, 13, 30, 45, 250, ZoneId.of("Asia/Tokyo"))`

### 13.3.5. Convertion methods

|メソッド|説明              |例                                                       |
|:-----:|------------------|---------------------------------------------------------|
|`at-`  |フィールドを拡張する|`LocalDate.of(2015, 7, 11).atTime(13, 30)`|
|       |                  |`LocalDateTime.of(2015, 7, 11, 13, 30, 45).atOffset(ZoneOffset.ofHours(9))`|
|`to-`  |フィールドを縮小する|`LocalDateTime.of(2015, 7, 11, 13, 30, 45).toLocalDate()`|

### 13.3.6. Obtain/Modify methods

|メソッド|説明|例|
|:----:|--------|--------|
|`get-`|フィールドの値を取得する|`LocalDate.now().getYear()`|
| | |`LocalDate.now().get(YEAR)`|
|`with-`|フィールドの値を変更し、コピーを返す|`LocalDate.now().withYear(2016)`|
| | |`LocalDate.now().with(2016, YEAR)`|

|ChronoField|Obtain|Modify|
|:------------:|:--------:|:--------:|
|`YEAR`|`getYear`|`withYear`|
|`MONTH_OF_YEAR`|`getMonthValue`|`withMonth`|
|`DAY_OF_MONTH`|`getDayOfMonth`|`withDayOfMonth`|
|`DAY_OF_WEEK`|`getDayOfWeek`|`N/A`|
|`HOUR_OF_DAY`|`getHour`|`withHour`|
|`MINUTE_OF_HOUR`|`getMinute`|`withMinute`|
|`SECOND_OD_MINUTE`|`getSecond`|`withSecond`|
|`NANO_OF_SECOND`|`getNano`|`withNano`|

### 13.3.7. Arithmetric methods

|メソッド|説明|例|
|:----:|--------|--------|
|`plus-`|フィールドの値を加算し、コピーを返す|`LocalDate.now().plusDays(7)`|
| | |`LocalDate.now().plus(7, DAYS)`|
|`minus-`|フィールドの値を減算し、コピーを返す|`LocalDate.now().minusDays(7)`|
| | |`LocalDate.now().minus(7, DAYS)`|

|ChronoUnit|Add|Subtract|
|:--------:|:--------:|:--------:|
|`YEARS`|`plusYears`|`minusYears`|
|`MONTHS`|`plusMonths`|`minusMonths`|
|`DAYS`|`plusDays`|`minusDays`|
|`WEEKS`|`plusWeeks`|`minusWeeks`|
|`HOURS`|`plusHours`|`minusHours`|
|`MINUTES`|`plusMinutes`|`minusMinutes`|
|`SECONDS`|`plusSeconds`|`minusSeconds`|
|`NANOS`|`plusNanos`|`minusNanos`|

### 13.3.8. Compare methods

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

### 13.3.9. Format/Parse methods

|メソッド|説明|
|--------|--------|
|`toString`|デフォルトのフォーマッタを使用して文字列化する|
|`format(DateTimeFormatter f)`|指定したフォーマッタを使用して文字列化する|
|`parse(String s)`|デフォルトのフォーマッタを使用して文字列から日付/時刻を生成する (ファクトリ・メソッド)|
|`parse(String s, DateTimeFormatter f)`|指定したフォーマッタを使用して文字列から日付/時刻を生成する (ファクトリ・メソッド)|

DateTimeFormatter

1. `ofPattern` ファクトリ・メソッドを使用する *e.g.* ofPattern("uuuu/MM/dd")
2. 定義済みパターンから選択する
  - `ISO_LOCAL_DATE`
  - `ISO_OFFSET_TIME`
  - `ISO_ZONED_DATE_TIME`
3. DateTimeFomatterBuilder

ResolverStyle

- One of formatter option (enum)
- LENIENT, SMART (default), STRICT
- Modifing method: withResolverStyle
- If it is STRICT mode, Pattern 'y' must be used with 'G' *e.g.* NO: yyyy/MM/dd, OK: Gyyyy/MM/dd

## 13.4. 高度なトピック

この章の内容は高度なトピックのため、最初は飛ばしてしまっても構いません。

### 13.4.1. Instant と Clock

スライドを参照してください。

### 13.4.2. ZoneId と ZoneOffset

スライドを参照してください。

### 13.4.3. Temporal オブジェクトとは？

スライドを参照してください。

### 13.4.4. 暦のサポート

スライドを参照してください。

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
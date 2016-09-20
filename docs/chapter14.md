# 14. 標準 API を活用しよう

## 14.1. Properties

Java VM や Java アプリケーションの設定情報を保持するために使用するクラスです。JDK 1.0 の頃から存在する最古参の API で、今ではあまり使われなくなった `Hashtable` クラスのサブクラスでもありますが、数度の改訂を重ね (開発中の Java SE 9 でも改訂予定があります) いまだに現役の API として活用されています。

### 14.1.1. Properties クラスの基本構造


### 14.1.2. システムプロパティ


### 14.1.3. Properties クラスとプロパティ・ファイル


## 14.2. JAXB

XML ファイルと Java のクラス (JavaBeans) の相互変換を実現するための API です。Java には XML を扱う API が複数存在していますが、JAXB はその中でも異色の存在で、プログラマが XML パーサーの存在を意識することなく使用できるメリットがあります。変換速度が他の XML API より高速である点も見逃せません。

### 14.2.1. 事前準備


**参考: Java 技術最前線「Java SE 6完全攻略」**

- [第73回 JAXB その1](http://itpro.nikkeibp.co.jp/article/COLUMN/20080530/305406/)
- [第74回 JAXB その2](http://itpro.nikkeibp.co.jp/article/COLUMN/20080606/306845/)
- [第75回 JAXB その3](http://itpro.nikkeibp.co.jp/article/COLUMN/20080613/307995/)
- [第76回 JAXB その4](http://itpro.nikkeibp.co.jp/article/COLUMN/20080620/308966/)
- [第77回 JAXB その5](http://itpro.nikkeibp.co.jp/article/COLUMN/20080627/309642/)
- [第78回 JAXB その6](http://itpro.nikkeibp.co.jp/article/COLUMN/20080704/310147/)
- [第79回 JAXB その7](http://itpro.nikkeibp.co.jp/article/COLUMN/20080711/310608/)
- [第80回 JAXB その8](http://itpro.nikkeibp.co.jp/article/COLUMN/20080711/310608/)

## 14.3. Pattern & Matcher

Java の正規表現実装です。正規表現そのものを表す `Pattern` クラスと、正規表現にマッチするかを判定する `Matcher` クラスから構成されます (`Matcher` クラスはマッチの判定だけでなく、マッチ箇所を他の文字列に置換する機能も持ちます)。`String` クラスなどにも正規表現を扱うメソッドが存在していますが、`Pattern` と `Matcher` はその裏方として働きます。ある意味当然のことですが、`Pattern` と `Matcher` を直接使用した方がパフォーマンス上優れています。

### 14.3.1. 簡単なパターンマッチング


### 14.3.2. 正規表現を用いた文字列置換


### 14.3.3. String クラスのメソッドとの対応


### 14.3.4. CSV ファイルを正規表現で解析するには


## 14.4. MessageDigest

データのメッセージ・ダイジェスト (ハッシュ値) を算出するための API です。現在のシステムでは、パスワードを暗号化してデータベースなどに保存するのではなく、パスワードのメッセージ・ダイジェストを保存して、入力されたパスワードのメッセージ・ダイジェストと照合する方法が一般的に用いられています (パスワードの暗号化は情報量が大きくなるため)。これからアプリケーションを実装する上で、メッセージ・ダイジェストの使い方は是非抑えておきたいものです。

### 14.4.1. 使ってみましょう


### 14.4.2. ユーティリティ・クラスを自作する


## 14.5. Logger

Java の標準 API にはログ出力 API が含まれています。Java の黎明期にはログ出力ライブラリとして "Apache Log4J" が普及しており、現在でも標準ではないが高機能な "Logback" や "JBoss LogManager" などが指示されています。Java 標準のログ出力 API (パッケージ名から `java.util.logging` やその略称の "JUL" でも呼ばれます) は "Logback" や "JBoss LogManager" に比べると機能は限定されますが、必要最低限の機能は有しており、何より標準で使用できるというメリットがあります。

### 14.5.1. Logger の基礎知識


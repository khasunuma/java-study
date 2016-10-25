# Java プログラミング速習テキスト

## 1. このテキストについて

Java の入門あるいは再入門テキストは世の中に多数で回っていますが、最新の Java SE 8 に対応したものや、Java の思わぬ落とし穴についてきちんと触れているものは非常に少ないです。このテキストは Java SE 8 をターゲットとすることで最新の API を活用した生産性の高いアプリケーション開発ができるようになること、意外と知られていない Java の落とし穴について紹介することを主な柱として執筆したものです。

全くのプログラミング初心者に対する説明を行う余裕はないため、対象とする読者には構造化プログラミングに関する基本的な知識を前提としました。C/C++、JavaScript、Python、Visual Basic などの経験が多少なりともあれば読み進めることはできるでしょう。また、研修で Java を習ったけれどもよく分からなかったという読者にとっても、復習教材として活用できるのではないかと思います。

**2016 年 10 月 26 日現在、このテキストは執筆途中です。あらかじめご了承ください。**

## 2. 参考文献

この資料は新たに書き下ろしたものですが、執筆に当たっては以下の資料を参考にしました。

1. James Gosling、Bill Joy、Guy Steele、Gilad Bracha、Alex Buckley：「[The Java Language Specification, Java SE 8 Edition](https://docs.oracle.com/javase/specs/jls/se8/html/index.html)」、Oracle Corporation、2015 年 2 月
2. 櫻庭祐一：「現場で使える [最新] Java SE 7/8 速攻入門」、技術評論社、2015 年 11 月
3. Brian Goetz、Tim Peierls、Joshua Bloch、Joseph Bowbeer、David Holmes、Doug Lea：「Java 並行処理プログラミング」、ソフトバンク・クリエイティブ、2006 年 11 月
4. 蓮沼賢志：「[JSR 310 "Date and Time API" への招待 III](http://www.slideshare.net/khasunuma/jsr310-3-61112729)」、関西 Java エンジニアの会「Java 8 徹底再入門」発表資料 、2015 年 7 月
5. 吉田真也：「きつねさんと学ぶ Lambda 式 & Stream API ハンズオン」、関西 Java エンジニアの会「Java 8 徹底再入門」ハンズオン資料、2015 年 7 月
6. 蓮沼賢志：「[Collections Framework Beginners Guide](http://www.slideshare.net/khasunuma/collections-framework-61112720)」、社内勉強会資料
7. 伊賀俊樹：「[入門から実践までJavaで学べる「ログ」の常識](http://www.atmarkit.co.jp/ait/articles/0801/08/news128.html)」、@IT 連載記事、ITmedia、2008 年 1 月

## 3. 著者について

### 蓮沼賢志 (HASUNUMA Kenji)

日本国内における著名な Java エンジニアのひとり。専門は Java EE (特に JAX-RS) であり、日本人 2 人目の Java Champion である寺田佳央氏の弟子に当たる。国内外で [Payara Community](http://www.payara.fish/) の外部協力者として知られており、[Payara Server の推薦文](http://www.payara.fish/testimonials)も寄稿している。

クライアントサイド技術にも造詣があり、Java SE 8 で導入された 'Date and Time API' ([JSR 310](https://jcp.org/en/jsr/detail?id=310)) の初期から仕様策定メンバーの末席に名を連ねている。日本で最も 'Date and Time API' を知る人物として、日本人最初の Java Champion である櫻庭祐一氏からも一目置かれている。「設計者 Stephen Colebourne には敵わないが、保守リーダーの Roger Riggsよりは深く理解している」つもり、とのこと。

そのキャリアに比して代表的な作品は存在していないが、常日頃から Java のエバンジェリスト的な活動を行っており、国内の主要なカンファレンス (JJUG CCC 等) では常連の登壇者である。目標とする人物は、ドイツの著名な Java Champion である [Adam Bien](http://www.adam-bien.com/) 氏である (彼も Java EE と JavaFX の双方について造詣が深いことで知られている)。

出自が C/C++ プログラマだったことも影響してか、いまだに Java の言語仕様についてケチを付けることがまれにある。基本的に思いつきで深い意味はないため、スルーするのが得策である。

**【所属学会・団体】**

- [ACM (Association for Computing Machinery)](https://www.acm.org/), Member
- [JCP (Java Community Process)](https://jcp.org/), Associate Member
- [JJUG (Japan Java User Group)](http://www.java-users.jp/), Member
- [GlassFish Users Group Japan](http://www.glassfish.jp/), President

**【関連リソース】**

- ブログ "Promised Land" -- http://www.copppermine.jp/docs/promised/
- 旧ブログ "Programming Studio" -- http://www.copppermine.jp/docs/programming/
- SlideShare -- https://www.slideshare.net/khasunuma
- GitHub -- https://github.com/khasunuma

**【著書・査読付き論文】**

- Elliotte Rusty Harold「[JavaMail API](https://www.oreilly.co.jp/books/9784873116631/)」、オライリー・ジャパン、2014 年 -- 日本語版 (電子書籍) 付録「JavaMail API で日本語を扱う際に気を付けること」の執筆を担当

## 4. 著作権表記

&copy; 2016 HASUNUMA Kenji ([Studio Coppermine](http://www.coppermine.jp/))

Contact : k.hasunuma@coppermine.jp

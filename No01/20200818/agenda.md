---
layout: default
title: 20200818
---
[TOPへ](/group-tokyo/)

## 基本情報

|開催日|開催時間|開催場所|
|---|---|---|
|2020-08-18|19:00-20:30|オンライン|

## 発表者
- メインセッション：ゆで卵 さん

## 参加者
[Connpassページ](https://databaseinternals.connpass.com/event/184985/)を参照のこと。

## 司会（管理者）
- こば

## アジェンダ

|No|時間|議題|説明|
|---|---|---|---|
|1|19:00-19:20|自己紹介|各自|
|2|19:20-20:10|（発表）5. Transaction Processing and Recovery(3.Consistency)||
|3|20:10-20:30|（議論）|本文内容にこだわらず自由に|


## 当日出た質問と回答（サマリ）

```
・分離レベルってどう選ぶの？
　⇒DBによってデフォルトがあり、それをまずは使う。
　⇒特殊な要件次第で変えることもあるという理解。
　⇒MySQLはRepeatable Read
　⇒Oracle、PostgresはRead Committed
　⇒MySQLではNextキーロック、GAPロック

・Scalar DBはSIとSerializableをサポート
　⇒Jepsenでテストしているのか？
　⇒どういう時にSerializableを使うのかを聞きたい。
　⇒Jepsen以外にも独自のテストをしている。
　⇒SIGMODでDBAを対象にアンケートを取ったら、ほとんど使ってない。

・OCCのところ、Validationに入る判断はどうする？
　⇒各トランザクションごとでは？
　⇒ValiとWrite、は不可分でatomicに行えないといけない。
　⇒End Transaction、のタイミングでValiで行う。
　⇒Valiは直列に行う論文があったが、今は並列で行う。
　⇒Readフェーズではトランザクションローカルなストレージに読み書きを行う
　⇒スレッドローカルなストレージ＝メモリ

・OCCでReadフェーズで大量のレコードが読み取られると
　Valiで時間がかからない？何か工夫されてるの？
　⇒ロングトランザクションで使うべきでない。
　⇒NewSQLでもロングに対応するために、PCCをサポートしている。
　⇒Scalar DBはOCCだけど短いトランザクションを想定している。
　⇒Valiが長くかかるので、
```


## 当日のチャットログ

```
- ここは難しいと思う。
- Optimistic concurrency controlとかPessimistic concurrency controlを実装しているデータベースって例えば何があるのでしょうか。MVCCは聞いたことはあるのですが、ほかはあまりなじみがないです。
- postgresがそうかなと思ってました。
- TiDBやCockroachDBなんかも最近は両方ですね。
- 分類が難しくて，pessimistic な MVCC，optimistic な MVCC なんかがあります．
- 複数のconcurrency controlを実装しているプロダクトがあるということなんですね。
- > 分類が難しくて，pessimistic な MVCC，optimistic な MVCC なんかがあります
- commitしていないトランザクションがスケジュールに存在すると，すべての操作を含んでいるとはいえないので，completeではありませんね
- これですよね。
- 製品については http://www.vldb.org/pvldb/vol10/p781-Wu.pdf の Table 1 がわかりやすいです．
- ここでもいろいろ調べれますね
- https://dbdb.io/browse
- Invariant = 不変条件だと思ってました
- 別ではないような。Repeatable ReadとSnapshot Isolationは並ぶ位置付け、という理解で良いのか。
- 正確にいえない、、
- https://arxiv.org/ftp/cs/papers/0701/0701157.pdf のTable4.ですね
- Snapshot IsolationはANSIの分離レベルが定められた以後に提案された実装なので，ANSIのベン図にうまくハマらなくて，それで別書きされているのだと思います
- RepeatableReadとSnapshot Isolationは別物だが、互いに強弱がつけられない関係にあるくらいの理解でした
- Validationフェーズは直列なのかなと読みました。
- > Snapshot IsolationはANSIの分離レベルが定められた以後に提案された実装なので
- 楽観的同時実行制御においてValidtion Phaseに遷移する判断ってどうやって行われるのかあんまりよくわかっていないです...
- ポスグレですね。
- コミットされてない値を最大でも1つに保つというのはよくわからなかったです。
- 上の本はMVCCにおけるスケジュールの実行例が載っていてよかったです
- 競合？
- 競合ですかね
- 2PL がデッドロックを解決するものではないような？
- WAIT DIE とかいろいろ deadlock free なものはありますが．
- 2PLであってもdeadlockは起こりえるはず...
- この後って基本的にロックについてですかね
- blink treeは少し聞きたいかも、、、
- 次回もし時間ありそうならLT枠にしてもいいかもです
- ですね
- 丁度btree variantsですし
- latch crabbing
- 蝶番のイメージ
- nikezonoさんの解説資料
- https://the-weekly-paper.github.io/jekyll/update/2017/06/25/An-empirical-evaluation-of-in-memory-multi-version-concurrency-control.html
- MySQLで、my.cnfにグローバルなデフォルト分離レベルが指定できた気がしています。名前が思い出せない・・
- MySQLだとこの辺か ... https://dev.mysql.com/doc/refman/5.6/ja/set-transaction.html
- SERIALIZABLEは、現実的でなくて、トランザクションを無用にabortしてしまうので設定しないほうがいいという認識でした
- ACIDrainという問題があり、CockroachDBはSERIALIZABLEのみをサポートしている。それ以外は危険らいしです。
- isolation levelで結構性能とか変わるんですか？同一DBのisolation level間の性能比較とかあるとみてみたいかも、、、
- それがこの論文か
- ACIDRainは，Serializableにしていてもなお起こる問題を扱った論文ですね．すごく面白かったです
- まだ読んでない・・・ http://www.bailis.org/papers/acidrain-sigmod2017.pdf これですか？
- serializableつかったことないです。
- CockroachDBはSERIALIZABLEのみ。
- 使わないですね。
- Scalar DB トランザクションの検証テストはこちらです
- https://github.com/scalar-labs/kelpie-test/tree/master/scalardb-test
- ないですね、、
- それです > acidrain
- 素人なんですけどなんで不要なんですか？
- 不要というか
- そんなに使わない
- SELECT FOR UPDATEは半ば常識でしたね
- https://gyazo.com/bfdf49f2012eb58154ed2ae38a1936cf
- RedisみたいにAtomic Updateの機能があれば別ですが。
- SIGMODの話，スクショとってたのありました
- NewSQL系だと常識が異なるのかも
- NewSQL 系は timetravel anomaly が〜みたいな話があったような
- 分散システムだとタイムスタンプに誤差があるから、みたいな話なんですかね
- 逆に、SERIALIZABLでなくてだいじょうぶかを、どうやアプリのレビューで確認しているのでしょう？
- そこはatomicなんでしょうね
- 実装しらないですか
- 条件チェックと書き込みがアトミック出なかったら、かきこみのが正しく行えないというイメージで認識してました
- writeは競合起こさなければ並列で走る？
- 分離レベルを使い分けるための方法を説明した、アプリ開発者向けの本を読みたいです。見つかってません。
- 各トランザクションでの書き込み（UPDATE等）はローカルメモリ上でやるのではないでしょうか？
- OCCの場合
- Write PhaseがCOMMIT的なイメージなのでしょうか・・・？
- OCCは多数の行を読み書きするバッチ処理には向かないのですかね。どの行を読み書きしたのかを覚えてないといけないため。
- occはconflict起こすものに対しては弱いですよね
- そうです。OCCではトランザクションは短くすべき、とされてます。
- ちゃんというと、
- まちがえました。
- HTMとか使っているDBありますか？
- OCCでも、競合がないとしておｍ，長いトランザクションは無理でしょうね。読み書きした多数のレコードを覚えておくメモリが足りなくなって。
- HTM使ってるDB，聞いたことないですね・・・．
- ありがとうございます。
- あ，いえ，私もあったら誰か教えてほしいなと　Concurrency Controlがいろいろサボれそうですよね
- https://blog.acolyer.org/2016/02/24/a-critique-of-ansi-sql-isolation-levels/
- STM なら存在するんでしょうか？
- データベースそのものがSTMか．．
- いわゆる，コテコテのSTMを使ったDB
- Snapshot isolationのsnapshotって、どういうものとして捉えると良さそうなんでしょうか。結構抽象的な表現で聞くことが多くで、実装イメージが湧かずにいました・・・
- 過去のある時点でのデータベースの状態を読み込むことができる，というのがスナップショットのイメージですかね．
- 私はデータベースを完全に（物理的に）止めた瞬間の断面というか，stateというか，というイメージで捉えてますね
- D論かな？2014に一応書かれているみたいです。今探したので違うかもしれませんが、、、https://www.researchgate.net/publication/324546430_Scalable_Database_Concurrency_Control_using_Transactional_Memory
- Snapshotはカメラのシャッター押して、実際にその時点のデータが必要だったら、なんとかしてその写真を再作成するようなイメージでした。なんかうまく話せてませんが。。。
- ありがとうございます :bow: ちょっとアバウトすぎる質問ですみません・・・
- PostgreSQLの場合、スナップショットはスナップショットを取得した時に実行中のトランザクションIDの集合です。それを使うことで各データを見てよいかどうかを判断しています。
```

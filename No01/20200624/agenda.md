---
layout: default
title: 20200624
---
[TOPへ](/group-tokyo/)

## 基本情報

|開催日|開催時間|開催場所|
|---|---|---|
|2020-06-24|19:00-20:30|オンライン|

## 発表者
- メインセッション：nobusue さん
- LT：Hirotaka Yoshioka さん

## 参加者
[Connpassページ](https://databaseinternals.connpass.com/event/177241/)を参照のこと。

## 司会（管理者）
- こば

## アジェンダ

|No|時間|議題|説明|
|---|---|---|---|
|1|19:00-19:30|自己紹介|各自|
|2|19:30-20:10|（発表）3. File Formats|サマリの発表を想定、都度質問もOK|
|3|20:10-20:20|（議論）3. File Formats|本文内容にこだわらず自由に|
|4|20:20-20:40|LT |Hirotaka Yoshioka さん「TCP-Hを実行してみた」|


## 当日出た質問と回答（サマリ）

```
議論：Slotted Pageについて
 - ページの両端からつかっていく形式自体は他にもみたことあるんですが、ああいうのを全部 slotted page っていうんでしょうか
 - おそらくそう。OracleやSQLiteでも同様。
 - ポスグレのソースのコメントにSlotted Pageと書いてある。

議論：ページヘダーへの修正について
 - ページヘダーに何か追加したり変更したりすると、影響が大きそうなんですが、そうした修正は結構起きるのでしょうか。
 - pg_upgradeなど複数バージョンの考慮が必要なモジュールもあり簡単ではないが、ページヘダーの不使用部分の意味を変えて使うことはある。CRCを持たせるなどがそうだったはず。
 - ページヘッダーの修正はPostgreSQL の場合、8.2 -> 8.3 のタイミングと、9.2 -> 9.3 のタイミングでヘッダーの内容が変更されているようだ。

議論：ページの更新頻度、ディスク同期について
 - レコードへのポインタをソートしなおすなど、ページ全体へのデータ変更は常にやっているのか。
 - postgresでは結構やっている。ページはメモリ上にあるので、変更はそこまで重くはない。
 - ディスク上でもやるのか。
 - ディスクへ反映するのはチェックポイント、これは非同期で通常はバッファ上のページ更新のみ。

議論：ページ破損の考慮とその対応について
 - ページの更新途中に壊れたりするので、同じページをMutableに書き変えるのはまずいのではないか。
 - バッファ内であれば問題ない。Checkpointのfsyncのときにエラーで落ちるとまずい。
 - それに対応するためにpostgresではfull_page_writesという仕組みでページ全体をWALに書き込むオプションがある。
 - MySQLだとDouble write buffer。full_page_writesと比較してどちらが良いと思うか。
 - ディスク書込みのタイミングが異なる。Double Write BufferだとCheckpoint時、full_page_writesはcommit時。
 - そのため、postgresではcommit時のレイテンシが大きくなってしまう。その点ではDouble Write Bufferの方が良い。

議論：DBMS以下のレイヤでのページ破損を回避する方法について
 - DBMSでページ破損を避けるのではなく、ファイルシステム側で防げないのか？
 - ZFSのatomic write？
 - かつてioDrive+独自のデバイスドライバでMySQLのAtomic Writeを実現する仕組みがあった。Double Writeを不要に出来る。
 - ファイルシステムレイヤでは、ジャーナルなどでファイルの破損を検出するのが目的だったりする。

（以下LT後の議論）
議論：なぜMariaDB？
 - MariaDBでTPC-Hをやった理由は？MariaDBのメリットはOSバンドルであることぐらいな気がする。
 - なんとなく。TPC-Hを使ってみたかった。

議論：MariaDBのチューニングについて
 - MySQLだとバッファプールに80%まで割り当てても良いはず。
 - バッファプール、70-80%を割り当てて良い。セッションごとのメモリやソートバッファのチューニングすると良いかも知れない。あとはログにflushするタイミングの調整。

議論：MySQL系での分析ワークロードについて
 - MySQLユーザに聞いてみたいが、DWH系クエリの検証やったことはあるか？
 - ない。おすすめできない。必要であればElasticSearchなど別のデータストアを使う。

議論：デフラグの具体的な内容について
 - ページ内でデフラグを具体的にどうやるかがこの本に書かれてないように思う。重要じゃないのか？
 - Vacuumなどは4章で出てくる。
 - postgresではページ内のデフラグはやっているはず。空き領域が多ければWALに書く量も減らせるので。
 - レコードを書き込むタイミングでやったほうが効率良さそう。

議論：B-Treeなどのベンチマークについて
 - B-Treeなどで新しい実装をした際にどんなベンチマークが必要か？の疑問が前回合った。その回答をこちらのブログに書いた。
 - https://tombo2-progress.hatenablog.com/entry/2020/06/24/193006
 - 現在はB-Treeのベンチマークシナリオを持っておいて、リグレッションテストをするなどはしてなさそう。
```

## 当日のチャットログ

```
- B-Treeの複雑さはmutabilirtyに起因する、書いてあったっけ。
- この章だったか思い出せない。
- > Since most of the complexity in B-Trees comes from mutability
- p46 の冒頭かと思います
- この辺、メモリをデータベースのブロック(ページ)のキャッシュと最初捉えて混乱してました。
- なるほど、メモリの話少し唐突感ありますもんね。
- RedisだとActive Defragmentationのような機能があってメモリ上のフラグメンテーションを意識していますが、インメモリDBだからこそ意識するって感じなんですかね。
- RDBだとメモリ上もページ管理だと思ってます。
- キャッシュもページ単位です。
- NWの話を結構示唆してきますが、本の後半でそんな所があるんでしたっけ。
- x86はたぶんlittle endianですかね
- 86はlittleです
- エンディアンの話、久々に聞きました。
- powerがbigでしたね
- ポケコン？
- BCD - binary coded decimal
- 今どきの言語しか分かりませんｗ
- 今どきの言語分かりませんｗ
- このよくみる構造にUCSDとかPascalとか名前がついてるのはじめて知りました
- UCSD Pascalが語源なのかな
- 確かにそうですね
- 言語はCならちょっと喋れる
- すえさん、今回の発表にぴったりだ。すごい。
- カーネル内なら一般的ですね、、、
- kind of a combination of packed booleans and enums. と言われるともにょりますね。普通に　packed boolean ではって感じが
- kernelとDBMSのページはサイズも違うし、ページというべきか、ブロックと呼ぶべきか、いつも悩む。
- Oracleだとデータ・ブロック、ポスグレだとページというイメージ。他はどうなんでしょう。
- MySQL(InnoDB)はpageってよんでるみたいですね。https://dev.mysql.com/doc/internals/en/innodb-page-structure.html
- オリジナル論文（リプリント）
- Bayer R, McCreight E. Organization and maintenance of large ordered indexes. InSoftware pioneers 2002 (pp. 245-262). Springer, Berlin, Heidelberg.
- http://www.ccs.neu.edu/home/kathleen/classes/cs3200/B+TreesOMLO.pdf
- Slotted Pageの訳語は聞いたことがない。
- この本の翻訳が出るときもそのままな気がする。
- 両端からつかっていく形式自体は他にもみたことあるんですがああいうのを全部 slotted page っていうんでしょうか
- おそらくそうです。ポスグレ、Oracle、SQLiteなんかもそうです。
- 説明分かりやすく大変ありがたいです...!
- ポスグレのFree Space Mapとかの話ですかね。
- ページヘダーに何か追加したり変更したりすると、影響が大きそうなんですが、そうした修正は結構起きるのでしょうか。
- 逆の埋め方あるのか！
- ページヘッダーの修正有無軽く調べたみたんですが、PostgreSQL の場合、8.2 -> 8.3 のタイミングと、9.2 -> 9.3 のタイミングでヘッダーの内容が変更されてるみたいですね。
- ドキュメントはこの辺ですかね。https://www.postgresql.jp/document/9.3/html/storage-page-layout.html
- PostgreSQLの場合、ページヘッダーにもページのバージョンを保存していて現在はバージョン4みたいです
- ほぉー > ポスグレのヘッダー変更
- なぜMariaDBなんだろう。
- 実運用だと複製を別ノードでもつので、ディスク故障とか再起動のような破損するような現象が起きるとどのみちフェイルオーバーさせるからfull page writeをoffにすることもあるのかなと思いました。
- 確かに
- ページ破損を検出できないのが一番困りますね。その場合、Full Page WriteをONにしてても一緒ですが。
- 私はWAL領域にioDriveとか高速なSSD用意して、乗り切ってました。
- バッファサイズ10G。
- MySQLって物理メモリの80%ぐらい、DBバッファに使ってよいんじゃなかったっけ。
- > On dedicated servers, up to 80% of physical memory is often assigned to the buffer pool. そうみたいですね。
- 本日の資料こちらでした https://docs.google.com/presentation/d/16lTGXElzjHApK52P_wmGxyOh4bfEyjtruixaaRxoqik/edit?usp=sharing
- MySQLだとDirect I/O使うからOSのページキャッシュ消費量が少なくて、バッファプールにその分割り当てられるってことでよかったでしたっけ。
- https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_flush_log_at_trx_commit 
- これの調整をしていたパラメータを見た気がしていて、探していたらちょうど言及されてて助かりました
- https://tombo2-progress.hatenablog.com/entry/2020/06/24/193006
- ↑以前アドバイス頂いたPostgreSQLのメーリングリストを読んでみました
- メモ程度なので共有するほどではないのですが、議論が全部追えるのは本当に良いなと思いました
- multi-version concurrency control (MVCC)の話が後でちょろっと出てきますが、
- そこでdeleteデータの扱いでtupleをどう消すかがsloted pageと絡むと凡人にはわかりませぬ..
- https://database-internals-reading-group-japan.github.io/group-tokyo/
- PostgreSQL の場合は論理削除のようなイメージですかね。各 tuple がトランザクション ID (XID) の情報を持っていて、read する Tx の XID と tuple のヘッダに格納されてるトランザクション ID (t_xmin, t_xmax) の情報を比較して可視/不可視 (read する Tx から見て削除されてるか否か) を判断してる感じだったと思います。
```

## 司会者のいろいろ

### 感想

- とにかくsueさんの説明が分かりやすかった。
- バイナリエンコーディングやSlotted Pageの内容でこれだけ議論できるの凄い、って毎回言ってるか。
- 2章と3章はこれからB-Treeの実装を見ていく上での基礎。4章から本番なので、次回が楽しみ。

### 反省点

- 先日、postgresやMySQLでのCheckpoint時のfyncエラーへの対応などについて調べたばかりだったので、個人的にそこに食い付き過ぎた。
- ファイルフォーマットという観点ではHDFSやCassandraなども聞いてみたかった。そもそもどれぐらい気にしているのかなど。ここはLSM-Treeの回に期待。

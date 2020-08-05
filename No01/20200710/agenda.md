---
layout: default
title: 20200710
---
[TOPへ](/group-tokyo/)

## 基本情報

|開催日|開催時間|開催場所|
|---|---|---|
|2020-07-10|19:00-20:30|オンライン|

## 発表者
- メインセッション：tkokamo さん
- LT：tom__bo さん

## 参加者
[Connpassページ](https://databaseinternals.connpass.com/event/177241/)を参照のこと。

## 司会（管理者）
- こば

## アジェンダ

|No|時間|議題|説明|
|---|---|---|---|
|1|19:00-19:30|自己紹介|各自|
|2|19:30-20:10|（発表）4. Implementing B-Trees|サマリの発表を想定、都度質問もOK|
|3|20:10-20:20|（議論）4. Implementing B-Trees|本文内容にこだわらず自由に|
|4|20:20-20:40|LT |tom__bo さん [「B Tree実装してみた」](https://speakerdeck.com/tombo/b-trees)|


## 当日出た質問と回答（サマリ）

```
（議論）XMAX値ってなんだろう。
　- この資料のXMAXはページ単位のもの。
　- Page単位のVacuumをやっていいのかを判断している。

（議論）RDB以外だと圧縮の状況はどうなんだろうか。
　- HBaseだと圧縮アルゴリズムを選べる。Cassandraだとデフォルトで圧縮。
　- カラムナだと圧縮が通常。
　- ファイルシステムだとZFSやbtrfsとかも。

（議論）Node High Keyは何が入るのか？
　- high keyはそのページ内の最大値ではなく、そのページに格納可能な最大値だったような気がする。
　- つまり、右隣のページの最小値で、ページ分割のときにのみ更新される、とかだった気がする。

（議論）Node High Keyが入ると何がうれしいのか。
　- 並列なSplitを検知して、リンクを辿れたりする。

```



## 当日のチャットログ

```
- 前章までとのつながり、整理してもらえるの良いですね。
- お、postgresの情報。篠田虎の巻かな。
- XMAX値ってなんだろう
- SQLiteユーザていますかね。
- 虎の巻には毎年お世話になってます。
- MVCC 関係の数字だと思います．
- 可視性の判断のために使われる値ですかね > コミッター様
- PostgreSQL 虎の巻ですか？
- https://community.hpe.com/t5/hpe-blog-japan/%E7%AF%A0%E7%94%B0%E3%81%AE%E8%99%8E%E3%81%AE%E5%B7%BB-%E7%AC%AC10%E5%BC%BE-%E5%85%AC%E9%96%8B-postgresql-11-%E3%81%AE%E6%AD%A3%E5%BC%8F%E7%89%88%E3%81%AB%E5%AF%BE%E5%BF%9C/ba-p/7023249?profile.language=ja#.XwhCspP7RTY
- xmax は PostgreSQL で tuple を削除した Tx の ID のことですね。
- ↓ のドキュメントにちょっとだけ説明があります。
- https://www.postgresql.jp/document/12/html/ddl-system-columns.html
- 仕事でメモリダンプ見るのつらい。
- そうそう、Range ScanにSibling Link必須だと思ってます。
- このチャットの内容を後で見直したいから (slack を補助的に使うと便利そう)
- 図が多くてめちゃくちゃわかりやすい。。
- ただ、親ノードに上がって検索するDBもあるって書いてありましたね。
- Sibling Links があれば Delete の pull-down の実装でちょっと楽ができそうな気がします...
- そもそもよくわからないのですが、High key って何かに使えるんですか？
- sibling links と node high-key の二つを同時に使うデータ構造には b-link-tree ってイメージがあります。どちらかだけを使う実装はあるんですかね
- Overflow PagesってpostgresのTOASTという理解で良いんですかね。
- Key は固定長なんじゃないでしょうか
- ノードは可変長としても
- postgresはBTStackと書かれてましたね。聞いたことはないのですが。
- なんでパンくずリストって言うようになったんでしょうね（経路保存でいいのに）
- 童話（赤ずきん）のパンくず？
- あーすごく納得しました
- パンくずってWeb画面のナビゲーションでも良くいいますね。
- あーなるほど
- そして赤ずきんではなく、たぶんヘンデルとグレーテル。
- ヘンゼルとグレーテルでした。
- 確かに，なんで納得できてしてしまったんでしょう…
- レベルが増えるのは怖い。
- 性能的にですね。
- リバランスは、リバランス処理自体もタダでできるわけではないと思うのですが、トレードオフを判断したりするのでしょうか。
- Bulk loadingはDBでよくある高速データロードの中身、なのかな。
- そんな雰囲気ですね > Bulk loading
- 自分も LOAD DATA INFILE '~~~' なんかで使われているものだと考えてました。
- 圧縮はpredictabilityが下がるので、あまり好きじゃない（個人の感想
- 性能予測可能性のことですか？ > predictability
- PostgreSQLとかだとどういう感じで圧縮してるんだろう
- 下のストレージシステムでも圧縮や重複排除したりするので、DBレイヤでやるかは良し悪しですね
- PostgreSQL での圧縮は、TOAST でやってる圧縮処理がここで説明されてる内容に該当するんですかね...？
- 他にデータを圧縮するような処理があるのだろうか。
- LSM-TreeのCompactionはいろいろアルゴリズムありそう。
- column-wiseでの圧縮というのは、Parquetでやってるみたいに、同じcolumnだとデータタイプなどが一緒なので圧縮しやすいということがあるんでしょうかね。
- PostgreSQL だと、ラージオブジェクトって機能で「ポインタだけ保存してデータは別のところに保存」みたいなことをしてくれるんでしたっけ。
- MySQLは昔インデックスのキーが768byteまでしかもてなくって、その辺の制限が最近(5.7かな)解除された記憶があります。
- なので、https://dev.mysql.com/doc/refman/5.6/en/innodb-file-space.html　このへんでしょうか。
- CMU の 講義資料に overflow の例として TOAST への言及がありますね (P15)
- → Postgres: TOAST (>2KB)
- → MySQL: Overflow (>½ size of page)
- → SQL Server: Overflow (>size of page) https://15445.courses.cs.cmu.edu/fall2019/slides/04-storage2.pdf
- https://engineer.retty.me/entry/columnar-storage-format
- zfsはlz4圧縮できます
- ファイル圧縮はRHEL8でVDOが入っていますね。
- BtrfsはRHEL7まで対応してましたが、RHEL8で外れてます。。
- 予め split することで上向きの伝搬をなくすことができるんですね。なるほど。
- テストまで。
- Ryzenいいすよね。コスパ最高。
- Ryzenの無駄遣いｗ
- スペック凄い...
- これLTで終わるのもったいないですね。
- B-tree のテストの方法難しいですよね...挿入順序で構造が変わるので

```


## 司会者のいろいろ

### 感想

- やっと実践的な内容になってきた。ここからが本番です。

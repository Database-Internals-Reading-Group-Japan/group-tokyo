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

## 当日出た質問と回答（サマリ）

```
議論：Mutable/Immutableの話
 - 2章だけ見ると唐突感があるが、1章からのつながっている話。
 - Part1 Conclusion でもMutableをBufferd、Orderedと同レベルの軸の一つとして整理している。

議論：SSD、FTLについて
 - 追記型、ガベージなどLSM Treeに近い構造といえるかもしれない。一時期、SSDの一部にSLCをキャッシュとして使う構成が流行った。
 - OpenChannel SSDはFTLなしなのか。
 - あれは特殊なデバイス。

議論：B-link-Treeについて
 - B-Treeの同時実行性を高めた派生版がB-link-Tree。ロック粒度など。
 - PostgreSQLではSplitやマージも変更されていて魔改造されている。

議論：B-Tree派生の評価実験のやり方は？過去にそうしたやり取りがあって、ポスグレのコミュニティで見れたりするのか？
 - PostgreSQLではやり取りが全てMLで保管されているので見れる。
 - 今6章に向けて、その辺りを作ってみてる。評価方法も参考になる。

議論：マージをしないということの動機はなんなのか？
 - 閾値ぎりぎりでマージが繰り返されないようになっている。

議論：インデックスのリバランス（マージ、スプリット）を実際の運用で気にしたことがあるか？モニタリングしてるか。
 - 気にしたことがない。モニタリングもしたことがない。


議論：SLCをSSD内のキャッシュに使うのは耐久性の観点か？性能？
 - SLCはMLCなどと比べて、書込み耐久性も全然違う。性能も違う。
 - SLCとMLCの違いは識別する電圧ですよね？
 - その通り。SLCはゼロかそうでないかで判別できるが、MLC以降は判定も複雑になるので劣化しやすくなり耐久性も落ちる。
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


### 反省点


---
layout: default
title: 20200828
---
[TOPへ](/group-tokyo/)

## 基本情報

|開催日|開催時間|開催場所|
|---|---|---|
|2020-08-28|19:00-20:30|オンライン|

## 発表者
- メインセッション：tom__bo さん

## 参加者
[Connpassページ](https://databaseinternals.connpass.com/event/184988/)を参照のこと。

## 司会（管理者）
- こば

## アジェンダ

|No|時間|議題|説明|
|---|---|---|---|
|1|19:00-19:20|自己紹介|各自|
|2|19:20-20:10|（発表）6. B-Tree Variants||
|3|20:10-20:30|（議論） | 本文内容にこだわらず自由に|


## 当日出た質問と回答（サマリ）

```
・mongodbはなぜWiredTiger？update bufferが他のDBで使わないのは何故？
　⇒レプリケーションで苦労していたはずで、それを解決するための手段だったはず。

・ARTってなに？
　⇒ARTはAdaptive Radix Tree

・LSM TreeとかCassandraから見るとB Tree Variantはどうなのか。
　⇒CassandraはReadがつらい。
　
・LSM tree
　⇒bloom filterはRead Amplificationの対策になってる。
```

## 当日のチャットログ

```
- https://tombo2.hatenablog.com/entry/2020/08/23/212607
- ブログこちら。
- 話者以外ミュートするようお願いします．
- この図凄いと思うんですよ、ほんと。
- サーベイすごい
- メニ―コアだからラッチフリーか、なるほど。
- randomアクセス遅いはHDDのほうがひどいと思うんですが、これがNAND Flash向けなのはwrite単位を意識して何でしょうか？
- FTLなしのNAND Flashアクセスへの最適化かなと思いました。2010年前後流行ってたと思います
- raw NANDはFTLなしなのですね、、、
- random write遅いは不揮発性メモリも同じですね。
- random write が遅いのは NAND flash の上書きできない特性のせいですかね。書き込み単位が小さく、上書きできる速いNVMが出てくると思います
- LSM Treeの話聞いてる気がしてきた。
- ですよね。最初見たときLSM treeでは？と思いました
- 時代的にも改善版なのでしょうね。
- LSM treeの実装にfractional cascadingが使われているかわからないですが、コンセプトは似てるのかな、と
- このlatchって前回やったものとは違うニュアンスですよね。
- これ丁度今みてました
- これページ上のフラグではなく CAS でやるのですね... しかもそれがちゃんと性能に出ているという
- lock freeはバグが怖い...
- Optimistic Lock Coupling
- http://sites.computer.org/debull/A19mar/p73.pdf
- blockサイズを考慮しなくても良い？
- これ難しいです。
- で、次回がLSM Treeという流れですね。
- Read Amplificationもある。
- ART はあついですよねー
- めちゃくちゃキャッシュを意識したデータ構造
- ARTはAdaptive Radix Tree
- https://qiita.com/fetaro/items/050a20f695652c6acc94
- これをみてると、インデックスとデータを別ディレクトリにすることができるようになった。メモリ使用量の制限ができるようになったということも書かれていました。
- 「Bw-tree ダメじゃん」論文は結局 indirection がボトルネックになるよねーという話だった記憶
- 図等がなくても各論文のモチベーションと工夫点がまとまってるだけで，とても役立つ思います
- PMEM?
- これ難しすぎて読めてないけど skip list で write-optimized する話もあるみたいですねー https://dl.acm.org/doi/10.1145/3034786.3056117
- 方向性が全然違いますが，The Case for Learned Index Structures https://dl.acm.org/doi/10.1145/3183713.3196909
- https://tikv.org/deep-dive/key-value-engine/b-tree-vs-lsm/
- ここにRead/Write/Space Amplificationの話があります。
```

## 司会者のいろいろ

### 感想

- LMDB、結構良さそう。
- B-Tree VariantでWrite/Space Amplificationを整理して、7章でLSM-Treeの流れが美しい。

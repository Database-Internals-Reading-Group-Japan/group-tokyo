---
layout: default
title: 20200604
---
[TOPへ](/group-tokyo/)

## 基本情報

|開催日|開催時間|開催場所|
|---|---|---|
|2020-06-04|19:00-20:30|オンライン|

## 発表者
- メインセッション：po3rin さん

## 参加者
[Connpassページ](https://databaseinternals.connpass.com/event/177235/)を参照のこと。

## 司会（管理者）
- こば

## アジェンダ

|No|時間|議題|説明|
|---|---|---|---|
|1|19:00-19:10|自己紹介|各自|
|2|19:10-19:40|（発表）2. B-Tree Basics|サマリの発表を想定、都度質問もOK|
|3|19:40-20:10|（議論）2. B-Tree Basics|本文内容にこだわらず自由に|

※LTは今回応募なし

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
- Fanoutってすんなり入ってこなかった概念です。
- 用語として
- メッセージングの世界だとよく使いますね>Fanout
- 扇の要から分岐するイメージ
- Introの文章の意図がわかっていないのですが、この章は mutable または inplace update ができるdbが対象の話なのでしょうか
- https://www.quora.com/What-is-fan-in-and-fan-out-in-logic-circuits
- 確かに唐突感ありましたね。Mutableなものをどうやって効率的に格納するかという話だと理解しました>tomtomさん
- 「1か所に限定」云々は、データの複数バージョンを持てるようにしたMVCCを意識した説明と理解しました。
- コメントありがとうございます。immutableな場合は、btree以外にも良い方法があったりするのでしょうか。
- Read-Modify-Writeの話ですね。
- 消去やアクセス単位の制限は、SSDというより、NANDフラッシュメモリの特性ですね
- 教科書的な導入だとデータベースの実装＝B木のようなイメージだったので Intro は違和感を感じませんでした。mutable or in-place update 可能な DB って一般的な認識とずれるものなんでしょうか？
- immutable なデータ構造については、追加も更新も削除もイベントとして追記し続ける、みたいな使い方の例が chap1 にあった気がします
- LevelDBとかはLSM-treeですよね(immutable)
- Paged Binary Treesは何ででてきたんだっけかな。
- postgres も追記型DBだったような
- 兄弟というか隣のリーフノードへのポインタかな。
- 双方向リンクリストですかね
- ディスクベースのストレージエンジンをimmutable(appendonly)とmutable(in place update)の2種類に分類されていて，前者の代表例がB-Tree、後者の代表例がLSM-treeであると認識しています。LSM-treeはこの本のあとの章で登場しますね。
- 範囲述語、かな。
- DBの人、述語という言葉をけっこ使う気がする。
- 述語、あまり馴染みないですね・・・
- ちなみに PostgreSQL は B-link-tree
- https://github.com/postgres/postgres/tree/master/src/backend/access/nbtree
- LevelDB->RocksDB->MyRocksストレージエンジンで、FBで使ってますね。　https://qiita.com/yebihara/items/7921d3e4b5c3d3ff9215
- B-Link-Tree？知らなかった。どう違うか、後で話せます？
- OK です，記憶が曖昧ですが覚えてる範囲で話しますねー
- splitとmergeは理解してもすぐ忘れる、、、
- b-link-treeは並列アクセスに強かったような気がします。しばらくみていないので構造は忘れてしまいました
- split と merge をトリガする要素数って流派があるような気がするのですが実装はどうなっているのでしょうか
- 実装してみないと忘れそう、、
- のちのリバランスの箇所で触れられるのかもですが、高さに余裕をもたせた状態で初期化したいケースや、merge をすぐにしたくないようなケースもありそうだなーと思いました
- Key は全て同じビット幅である前提なのでしょうか？
- リバランスは避けたいんですけど、ログに出たりしないですよね。
- ノードに要素が詰まってるほうが有利なこともあって，2/3でトリガするB+treeの亜種をknuthがB*treeという名前で提案してたりしますね
- ファイルは3章で議論ですね
- みんな意識してるのかな。
- > merge をすぐにしたくないようなケースもありそうだなー あえてmergeを遅延させるような実装もあったと思いますー
- B+Treeで定番のOSS実装ってあるんでしょうか？
- 実装をみてみたいです。
- 定番というかMySQLのInnoDB実装はB+Treeですね。　https://blog.jcole.us/2013/01/10/btree-index-structures-in-innodb/
- 余談ですがそのブログ書いてたJeremy ColeはGizzardっていうMySQLのSharding Framework作ってましたね。
- B-tree を可視化しているサイトを見つけました https://www.cs.usfca.edu/~galles/visualization/BTree.html
- openchannel SSDというものはFTLないです笑
- アルゴリズムイントロダクションの第2巻に実装の説明があってよかったです。
- PMDK の libpmemkv には B+-tree の実装があるようです＞B+木の OSS 実装https://github.com/pmem/pmemkv#storage-engines
- SLCをキャッシュに使うというのは初耳でした。ありがとうございます。
- SLCとMLCでデータ階層化みたいなことをやってるんですね
- Pmem 関連の btree は最近サーベイが出ていましたね
- http://www.vldb.org/pvldb/vol13/p574-lersch.pdf
- FPtreeが強いですよね
- 5章のBlink-treeありますよ～
- 結構古いですけど、B-Link_treeno https://www.slideshare.net/myui/blinktree-presentation
- mergeってPostgreSQLもしないんですね... Oracleもしなかったはずです。
- 右の方を90%で割るのは、追加がけっこう見込めるから疎な状態でつくると次の split がおきにくくてうれしい、みたいなことだと理解しました
- https://www.postgresql.org/message-id/flat/56AB6D30.2040900%40postgrespro.ru
- Btree のサイズを効率化する改造を入れたときの議論です
- 2015年から議論が開始されて，2020年にコミット・・・
- すごい...ありがとうございます！
- MySQLはマージしますね。。。　https://www.percona.com/blog/2017/04/10/innodb-page-merging-and-page-splitting/
- merge / split とかはwebアプリケーションつくってるうえでまったく気にしたことがないです(RoR + mysql です
- treeレベルのリバランスも嫌なんですか？
- 開発者視点では、特定のクエリーが早くなったり遅くなったり、予測不能な振る舞いをすると「なんで?」って思うくらいですかね。
- そういえばSLCをキャッシュに使うのは耐久性にすぐれているから？
- 性能もよいんですか？
- MySQL 5.7.xからマージの閾値をMERGE_THRESHOLDで指定できますね。デフォルトは50 https://dev.mysql.com/doc/refman/5.7/en/index-page-merge-threshold.html
- 50(%)あいたらマージするって結構アグレッシブに感じましたが、そういうものなんですね。
- SLC cache の簡単な測定はこちらなどにありますね https://ascii.jp/elem/000/001/860/1860708/4/
```

## 司会者のいろいろ

### 感想

- フラッシュメモリベンダの方がいたり、ファーム書いてた人がいたりとこの第2章にふさわしいメンバーで知識が深まった。
- 著者から頂いたステッカーは紹介できたが、まだ配れないのであった。

### 反省点

- 第3回のURLを参加者に配信してしまい大混乱。そして肝心な時に配信が遅延するConnpassメール。本当にごめんなさい。
- 自己紹介は参加者に喋ってもらう機会であり、かつ司会者が各自のバックグラウンドを知って議論に活かせる貴重な機会。
- ただ、いつも司会者が時間に追われてしまう意識があり、もったいない気がしている。多少掘り下げて会話しても良いかも知れない。
- 今回7-8名ほどの補欠者が出ていて、YouTube Liveに回ってもらった。しかし、その程度の人数であればオンラインMtgに招待した方が良かったかもしれない。
- そうすると、自己紹介の時間が足りなくなるという罠がある。
- テキストベースで回答してもらうなどのやり方があるが、それはそれで声が聴けないという欠点もあり難しい。

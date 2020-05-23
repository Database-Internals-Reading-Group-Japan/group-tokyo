---
layout: default
title: TOP
---

# Database Internals輪読会
For Database Internals Reading Group in Japan-Tokyo

## 開催趣旨
**Database Internals - A Deep Dive into How Distributed Data Systems Work**（目次は[こちら](https://www.oreilly.com/library/view/database-internals/9781492040330/)）の輪読をしていく勉強会です。

2019年に出版されたDatabase Internalsではこれまでのデータベース関連の出版物としては珍しく、ストレージエンジンと分散システムを対象に、各種論文や実際のDBMSの実装などが紹介されています。

こちらの書籍を詳細に読み進め、皆で議論をしながら、データベースにDeep Diveしていきましょう。

## スケジュール

|No|開催日|輪読対象|ボリューム※|開催場所|発表者|
|-----|-----|-----|-----|-----|:-----|
|1|[2020-05-21](./No01/20200220/agenda.html)|Preface / 1. Introduction and Overview|597|オンライン|yahonda さん|
|2|2020/6|2. B-Tree Basics|335|オンライン|po3rin さん|
|3|2020/6|3. File Formats|311|オンライン| nobusue さん|
|4|2020/7|4. Implementing B-Trees|304|TBD|tkokamo さん|
|5.1|2020/7|5. Transaction Processing and Recovery(2.Recoveryまで)|687|TBD|fujii_masao さん|
|5.2|2020/8|5. Transaction Processing and Recovery(3.Concurrency Control)|687|TBD|ゆで卵さん|
|6|2020/8|6. B-Tree Variants|320|TBD|tom__bo さん|
|7.1|2020/9|7. Log-Structured Storage / Part I Conclusion(前半)|650|TBD|ikedamsh さん|
|7.2|2020/9|7. Log-Structured Storage / Part I Conclusion(後半)|650|TBD|Yuji Ito さん|
|8|2020/|II. Distributed Systems / 8. Introduction and Overview|589|TBD|TBD|
|9|2020/|9. Failure Detection|174|TBD|TBD|
|10|2020/|10. Leader Election|158|TBD|hamaken さん|
|11|2020/|11. Replication and Consistency|604|TBD|TBD|
|12|2020/|12. Anti-Entropy and Dissemination|279|TBD|TBD|
|13|2020/|13. Distributed Transactions|421|TBD|TBD|
|14|2020/|14. Consensus / Part II Conclusion|698|TBD|TBD|

※発表者名はConnpass表示名より。
※ボリュームはkindleの位置ナンバーより概算。

## 参加条件

- **Database Internalsをお持ちの方**
- 上記書籍を中心に著作権の侵害を行わないことを約束頂ける方

## 想定する参加者

- データベースの内部構造に深い関心のある方
- 分散データベースを学びたい方
- データベース管理や開発・設計の実務経験がある方
- データベースの開発・研究・調査に従事されている方
- 「データ指向アプリケーションデザイン」等でデータストアについて関心を持った方

## 進め方

- 各自でDatabase Internals（紙 or 電子書籍）を準備し、その日の対象章を読んでおいて下さい。
- 各回ごとに1章、または2章ずつ読み進め、議論をしていきます。
- 1章ごとに発表者を1名立てます。難解な章については2名に分けることも考えます。
- 発表後に疑問点や気になる点について議論を行います。議論したいテーマを各自でも準備頂けるとスムーズです。
- 発表、議論は基本的に日本語で行います。
- 進め方や発表者の選定は第一回で打ち合わせをしたいと思います。
- 懇親会は枠内で実施しません。可能であれば、お菓子ぐらいは準備できればと思っています。

## LT枠について
データベースに関するLTをしたい方がいれば、一人5分を上限として申し込みを受け付けます。
オンライン中は各回1人で試してみます。
タイトルは申し込み時に仮でも良いので記入をお願いします。

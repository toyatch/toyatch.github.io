---
layout: post
title:  "保守性を計測できるか？"
date:   2021-09-20 10:05:00 +0900
categories: git
---

## 背景

gitのプルリクエストのマージ履歴から、何か保守性に関する数値がとれないか考えていました。  

一つのソフトウェアに機能追加を繰り返していくと、複雑化により保守性が低下します。  
この複雑度は雪だるま式に大きくなっていくので、技術負債とも呼ばれます。
負債はできるだけ早く返したい。でも、負債に気づけないことも多い。負債の兆候に気づく指標がほしい。  

githubのログから何かできるか・・・？  
という検討のメモです。  

* 参考  
ここのお話がとても面白く参考になりました。  
https://anchor.fm/textafm/episodes/6--1on1-in-Public-e1078pn

---

githubのマージコミットを解析して、いつどんなプルリクがマージされたかの数値をとってみます。  

前提として、ブランチを切って開発してプルリクを作成する、githubフロー？の場合を想定してます。  
本流のマージはnoffオプションなどで、ログに残るようになっている想定です。  

### 1. マージコミットのログを参照します
```
git log --merges -m --name-status
```

結果
```
commit <<marge-commit-hash>> (from <<from-hash>>) (HEAD -> master)
Merge: xxxxxxx yyyyyyy
Author: aaaaaa <bbbbbb>
Date:   Sun Sep 19 14:10:47 2021 +0900

    Merge branch 'b001'

M       sample.rb
```

### 2. マージコミットの適用内容をチェック

```
git log --no-merges <<from-hash>>..<<merge-commit-hash>>
````

結果
```
commit <<hash1>> (b001)
Author: aaaaaa <bbbbbb>
Date:   Sun Sep 19 14:10:14 2021 +0900

    test2

commit <<hash2>>
Author: aaaaaa <bbbbbb>
Date:   Sun Sep 19 14:09:47 2021 +0900

    test1
```

Date部分を抜き出せば、最終コミット日と初回コミット日がとれる。


### 3. マージコミットの差分行数もとれる。

```
git diff -w --stat <<from-hash>> <<merge-commit-hash>>
```

結果
```
 sample.rb | 2 ++
 1 file changed, 2 insertions(+)
```

---

うまく組み合わせると自動で数値をとれそう。  

これらを組み合わせてできたのがこれ。  
[https://github.com/toyatch/git-merge-analysis](https://github.com/toyatch/git-merge-analysis)

このあたりを収集できる。

* 初回コミットからプルリクを本流マージするまでのリードタイム
* プルリク当たりの変更回数（レビュー指摘の多さに近似するかも？)
* プルリク当たりの変更行数

以上。

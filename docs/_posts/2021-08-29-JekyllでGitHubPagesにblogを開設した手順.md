---
layout: post
title:  "JekyllでGitHub Pagesにblogを開設した手順"
date:   2021-08-29 20:35:00 +0900
categories: jekyll github
---

個人的な技術の学習メモや考えた事などの備忘録としてGitHub Pagesに(シンプルに)記事を残しておきたい。
ということでGithub Pagesはじめました。  

開設手順をまとめておきます。  

---

## 前提条件

別途構築済のRubyの開発環境があったためそのまま使っています。  
Rubyのバージョンは2.7.2を使用しています。(Ruby3.0ではうまくいかず)  

## 手順

### 1. Wellcome to Jekyll!!まで勧める
GitHubドキュメントに丁寧な説明があるので、その通り実施して「Wellcome to Jekyll!」の表示までを行います。

[Jekyll を使用して GitHub Pages サイトを設定する](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll)

以後、ここで作成したdocsをカレントディレクトリとして話をします。

### 2. SEO対策

個人的なメモを配置するので、今のところ「発信」を意識していません。  
クローラはどなたさまも無視してください、という内容でrobots.txtを作成します。  

* robots.txt
```
User-agent: *
Disallow: /
```

### 3. タイトルや執筆者情報設定

以下のファイルを修正します。

* _config.yml
```
title: Your awesome title         <--- ここにブログ名を記載します
#email: your-email@example.com    <--- コメントアウトすると非表示になります
description: >- # this means to ignore newlines until "baseurl:"
  ここにブログの説明を記載します。
...
twitter_username: jekyllrb        <--- 必要に応じて設定
github_username:  jekyll          <--- 必要に応じて設定
```

### 4. _post以下に記事を配置します

* _posts/YYYY-MM-DD-記事名.md
```
---
layout: post
title:  "記事タイトル"
date:   2021-08-29 20:35:00 +0900
categories: タグ1 タグ2
---
本文....
```

※ _psts/hogeYYYY-MM-DD-記事名.mdのように、directoryを追加してファイルの整理もできるようです。

### 5. publish

gh-pagesブランチにcommit&pushで完了です。
```
git add -A
git commit
git push origin gh-pages

```

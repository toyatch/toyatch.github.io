---
layout: post
title:  "lsp-modeでtypescript環境構築(in proxy)"
date:   2023-12-22 21:00:00 +0900
tags: emacs
---

lsp-modeでtypescriptのlanguage-serverを使いたい。
通常であれば、lsp-modeを起動して.tsファイルを開けばts-lsを選択してインストールできる。

しかしproxy環境ではこのインストールがうまくいかない事がある。
npmで手動インストールする方法を記録しておく。

```
npm -g --prefix ~/.emacs.d/.cache/lsp/npm/typescript-language-server install typescript-language-server
npm -g --prefix ~/.emacs.d/.cache/lsp/npm/typescript install typescript
```

以上

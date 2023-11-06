---
layout: post
title:  "EmacsでGitHub Copilitを使う"
date:   2023-11-06 21:33:00 +0900
tags: emacs copilot
---

# 前提
GithubアカウントがCopilotが利用可能な状態になっていること

# 準備

## node.js
node.jsが必要なのでインストール

https://toyatch.github.io/2022/08/06/nvm.html
nvm official: https://github.com/nvm-sh/nvm

```
$ node -v
v20.9.0
```

## copilot.el

straight.elのインストールがうまくいかないので、gitから直接インストールする

```
cd ~/.emacs.d
git clone https://github.com/zerolfx/copilot.el
```
## .emacs.d/init.el

以下を追加

```elisp
;; copilot
(package-install 'editorconfig)
(package-install 'dash)
(package-install 's)

(add-to-list 'load-path "~/.emacs.d/copilot.el")
(require 'copilot)
(use-package copilot
  :init
  (setq copilot-node-executable "~/.nvm/versions/node/v20.9.0/bin/node")
  :bind
  (:map copilot-mode-map
        ("M-p" . copilot-previous-completion)
        ("M-n" . copilot-next-completion)
        ("TAB" . copilot-accept-completion)))
```

# 使い方

初回はM-xでcopilot-loginを実行して、GitHubのアクセストークンを入力する

M-x copilot-modeでON/OFF

以上


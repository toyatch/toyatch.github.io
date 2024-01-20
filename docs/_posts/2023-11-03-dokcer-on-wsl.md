---
layout: post
title:  "WSL ubuntu22.04でdocker-ceが使えない時の対策"
date:   2023-11-03 22:20:00 +0900
tags: WSL docker
---

# 環境
ubuntu-22.04 on WSL

# 問題点

```
$ sudo docker run hello-world
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```

Docker立ち上げようとしても、起動しない。
```
$ sudo service docker start
 * Starting Docker: docker
   ...done
$ sudo service docker status
 * Docker is not running
```

# 原因

ubhuntu22.04だとiptablesの設定の変更が必要らしい。

# 対策

```
sudo update-alternatives --config iptables
```

iptables-legacyを選択。

以上


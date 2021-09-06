---
layout: post
title:  "WSL上でvagrantを使いたい話"
date:   2021-09-03 21:00:00 +0900
categories: enviroment
---

WindowsがWSL2に対応して以来、自宅用PCではWSL2を使っています。
Windows起動からほぼシームレスにlinuxのshell(それは即ちemacsが使える事を意味する)が使えるのはやはり快適です。

とはいえ・・・
WSL2用のLinuxディストリビューションは選択肢が限られているので、CentOSの環境を再現できない点が不満でした。
仕事でよく使う環境が再現できない(仕事の為の学習に使いづらい)ので学習がはかどらない。  

やはりVagrant&VirtualBox環境に戻ろうかと思いつつ、それではlinuxのshellまでの距離が遠くなる・・・。
どうしたものがと考えていましたが、WSL2から仮想環境を起動(vagrant up)できれば良い事に思い至りました。  

調べてみると、既にこのような事をまとめている方がおられました。

[https://zenn.dev/lapi/articles/2021-06-10-vagrant_wsl2](https://zenn.dev/lapi/articles/2021-06-10-vagrant_wsl2)

ということで早速！


## 前提条件

```
Windows10
Ubuntu20.04LTS on WSL
VirtualBox 6.1.14
Vagrant 2.2.9
```

[https://www.virtualbox.org](https://www.virtualbox.org)
[https://www.vagrantup.com](https://www.vagrantup.com)

## WSL2環境にVagrantをインストール

Windowsと同じバージョンである必要があるそうです。

```
wget https://releases.hashicorp.com/vagrant/2.2.18/vagrant_2.2.18_x86_64.deb
sudo dpkg -i vagrant_2.2.18_x86_64.deb
```

VirtualBoxに接続するために環境変数を設定する必要があるようです

```
echo 'export VAGRANT_WSL_ENABLE_WINDOWS_ACCESS="1"'               >> ~/.bashrc
echo 'export PATH="$PATH:/mnt/c/Program Files/Oracle/VirtualBox"' >> ~/.bashrc
source ~/.bashrc
```

WSLでvagrant sshするためにプラグインが要るようです。
```
vagrant plugin install virtualbox_WSL2
```

## 実験

```
vagrant box add centos/7
mkdir centos7
cd centos7
vagrant init 'centos/7'
vagrant up
```

## ところが・・・

default: SSH auth method: private key
から進まない・・・。  
(近日対応予定)

---

以上。


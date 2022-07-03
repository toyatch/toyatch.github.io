---
layout: post
title:  "VirualBox + VagrantのVM(centos)のディスクサイズを変更する方法"
date:   2022-07-02 17:53:00 +0900
categories: Vagrant VirtualBox
---

## やりたいこと

VirtualBox + VagrantのVMのディスクサイズを変更したい。
vagrant-disksizeでサイズを指定しただけでは、ゲストOS内のディスク容量は増えないので、
ゲストOS内でパーティション設定を行い、増えたディスク領域を利用可能な状態にしなければならない。

## 準備

vagrant-disksizeを利用して、ディスクサイズ60GBのVMを作成する(デフォルトだと40GB)。

### vagrant-disksizeプラグインをインストール

```
vagrant plugin install vagrant-disksize
```

### Vagrantfile作成

centos7のVagrantfileを作成する。

```
vagrant init centos/7
```

disksizeを設定する

```
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.disksize.size = '60GB' # <- 追記
end
```

vagrantupしてSSHログイン

```
vagrant up
vagrant ssh
```

## ディスクサイズの確認

```
$ df -h

Filesystem      Size  Used Avail Use% Mounted on
～略～
/dev/sda1        40G  3.0G   38G   8% /
～略～
```

→40Gしかない。

## パーティションの設定

### fdiskでパーティションの作り直しを行う

一度パーティションを削除して作り直すことで、最大サイズの割り当てとなる。

```
$ sudo fdisk /dev/sda

Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Command (m for help): p

Disk /dev/sda: 64.4 GB, 64424509440 bytes, 125829120 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0009ef1a

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048    83886079    41942016   83  Linux

Command (m for help): d
Selected partition 1
Partition 1 is deleted

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-125829119, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-125829119, default 125829119):
Using default value 125829119
Partition 1 of type Linux and of size 60 GiB is set

Command (m for help): p

Disk /dev/sda: 64.4 GB, 64424509440 bytes, 125829120 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0009ef1a

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1            2048   125829119    62913536   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```

### VMを再起動する

```
vagrant reload
vagrant ssh
```

### xfs_growfsを実行

```
$ sudo xfs_growfs /dev/sda1
meta-data=/dev/sda1              isize=512    agcount=4, agsize=2621376 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=10485504, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=5119, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 10485504 to 15728384
```

↓結果6/dev/sda1が60GBになる

```
$ df -h

Filesystem      Size  Used Avail Use% Mounted on
～略～
/dev/sda1        60G  3.0G   58G   5% /
～略～
```

以上

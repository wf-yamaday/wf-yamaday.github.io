---
layout: post
title: "Raspberry pi zero w"
date: 2018-03-11 22:47:10 +900
categories: blog,raspberry pi
author: yamaday
---

# Raspberry Pi zeroのセットアップ

Raspberry Pi zero wを手に入れたのでセットアップをしたいと思います．  


## 1.準備したもの

* raspberry pi zero w 本体
* MicroSD 8gb
* MicroUSBケーブル 
* RaspbianのOSイメージ
* macbook air

## 2.OSイメージの書き込み

### OSイメージのダウンロード

OSのダウンロードは[こちら](https://www.raspberrypi.org/downloads/raspbian/)から行います．  
今回はとりあえずサーバーにしようと思うので，GUI環境のないRaspbian LITEをダウンロードしようと思います．  
ダウンロードが完了したら，ターミナルでダウンロード先のディレクトリに移動し解凍しておきましょう．

```
$ cd Downloads 
$ unzip 2017-07-05-raspbian-jessie-lite.zip
```

### OSイメージのインストール

次にOSをインストールするMicroSDカード（以下SDカード）を挿します．そして以下のコマンドで挿されている場所を確認します．

```
$ diskutil list
/dev/disk2 (internal, physical):
...省略...
```

これで`/dev/disk2`にSDカードがあることが確認できました．  
次にフォーマットを行います．

```
$ diskutil eraseDisk FAT32 RPI /dev/disk2 
```

フォーマット後，SDカードをアンマウントします．

```
$ diskutil unmountDisk /dev/disk2
```

これでSDカードにOSをインストールする準備ができました．  
以下のコマンドでSDカードにインストールを行いましょう．

```
$ sudo dd bs=1m if=2017-11-29-raspbian-stretch-lite.img of=/dev/rdisk2 conv=sync
Password:
1772+0 records in
1772+0 records out
1858076672 bytes transferred in 151.495804 secs (12264872 bytes/sec)
```

## 3.Zero Over USB方式の設定

今回はMacとRaspberry pi zeroをUSBケーブル1本で接続するZero Over USB方式でセットアップを行うためいくつか設定を加えます．  
具体的には以下のファイルに設定を追加します，

* `/boot/config.txt`
* `/boot/cmdline.txt`

これらをvimで編集します．

```
$ vim /Volumes/boot/config.txt
```

最後の1行に`dtoverlay=dwc2`を追加．

```
$ vim /Volumes/boot/cmdline.txt
```

rootwaitとquietの間に`modules-load=dwc2,g_ether`を追加．  
catコマンドで確認すると以下のようになる．

```
$ cat /Volumes/boot/cmdline.txt
dwc_otg.lpm_enable=0 console=serial0,115200 console=tty1 root=PARTUUID=37665771-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait modules-load=dwc2,g_ether quiet init=/usr/lib/raspi-config/init_resize.sh
```

最後にsshできるように`/boot`にsshの名前の空ファイルを作成します．

```
$ touch /Volumes/boot/ssh
```

## 4.起動

Raspberry pi zero本体にSDカードを挿入し，USBケーブルでMacbookと接続します．  
Macの「システム環境設定」→「ネットワーク」に`RNDIS/Ethernet Gadget`のネットワークインタフェースが現れていればインストールは無事に完了しています．

### SSH接続

SSHでログインしてみましょう．  
※初回は時間がかかります．

```
$ ssh pi@raspberrypi.local
```

あとはこれにjupyterでも入れて簡易的なデータ分析に使おうかなと考えてます．



#### 参考文献
今井 一雅，[Raspberry Pi ZeroのよるIOT入門-Zero W 対応-](https://www.amazon.co.jp/Raspberry-Pi-Zero%E3%81%AB%E3%82%88%E3%82%8BIoT%E5%85%A5%E9%96%80-Zero-%E5%AF%BE%E5%BF%9C/dp/4339009016/ref=sr_1_1?ie=UTF8&qid=1520776371&sr=8-1&keywords=raspberry+pi+zero%E3%81%AB%E3%82%88%E3%82%8Biot%E5%85%A5%E9%96%80)
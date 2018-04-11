---
title: 反编译和重新签名apk
date: 2017-04-06 
tags:
---

## 反编译资源文件

#### 工具---apktool
[下载地址](https://ibotpeaches.github.io/Apktool/install)

反编译命令：

    apktool d toDecompile.apk

重新打包：

    apktool b toDecompile -o new_test.apk

重新签名：

    jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore 签名文件名 -storepass 签名密码 待签名的APK文件名 签名的别名

## 反编译代码

#### 工具----dex2jar

[下载地址](https://sourceforge.net/projects/dex2jar/files/)

将要反编译的apk文件重命名为zip格式并解压缩，注意其中的classes.dex文件，它存放了全部的java代码，将classes.dex文件拷贝到dex2jar解压后的根目录下。

进入dex2jar解压后的根目录，执行命令：

    d2j-dex2jar classes.dex

要查看java代码，还需要下载jd-gui这个工具，下载地址：http://jd.benow.ca/ ，
目前最新版是1.4.0，下载完后解压缩，并用jd-gui.exe打开上边反编译出来的jar文件

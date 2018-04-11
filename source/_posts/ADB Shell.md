---
title: ADB Shell
date: 2016-07-09 
tags:
---

# ADB Shell 学习

> 又是一个周末了,最怕闲来无事的周末，于是给自己找点事情做，ADB shell这一块一直没有系统的学习，正好趁此机会学习。

主要通过[学习资料](http://adbshell.com/commands)和adb --help文档学习

### 怎么使用ADB shell命令

首先找到Andorid的SDK安装路径或者单独安装ADB组件，sdk中adb路径在<Android SDK&gt;platform-tools 文件夹下。

**Windows下**

```shell
cd <adb-path>
#执行adb命令
adb shell
```

**Mac下**

```shell
#配置adb环境变量
vi ~/.bash_profile
#编辑该文件，添加你的adb路径
export ADB_PATH= your_adb_path
export PATH=${PATH}:${ADB_PATH}

#退出保存再执行命令使得环境变量生效
source .bash_profile
```

### **ADB Debugging命令**

#### **adb devices**

列出连接的设备  

>*adb devices [-l]* '-l'

参数用于指定需要列出的设备

```shell
#打印连接的设备
adb devices
```

返回结果

```shell
#执行命令返回设备的序列数字和状态
2b70fc6a        device
```

#### **adb forward**

重定向连接,需要开启设备的USB debugging模式  

>*adb forward &lt;local&gt; &lt;remote&gt;*   
 *adb froward --no-rebind &lt;local&gt; &lt;remote&gt;*     
  
作用同上，但是如果已经连接就会失败  

>*adb forward --remove &lt;local&gt;*

删除指定连接的设备  

>*adb forward --remove-all*


```shell
#映射本地的8000端口到设备的端口9000
adb forward tcp:8000 tcp:9000
```

#### **adb kill-server**

终止adb服务进程  如果服务在运行则终止

```shell
adb kill-server
```

### **无线连接命令**

#### **adb connect**

> 通过WIFI使用ADB  

*adb connect &lt;host&gt; [:&lt;port&gt;]*

*第一步* 通过USB连接设备

*第二步* 使用命令查看连接的设备

```shell
adb devices
```

 **注意：** 以上步骤不可忽略

*第三步* 以TCP模式重启端口：5555

*第四步* 查看Android设备的IP地址：设置-&gt;关于手机-&gt;状态-&gt;IP地址，将该IP地址以 #.#.#.# 的格式记录下来

*第五步* 执行命令

```shell
#   #.#.#.# 为刚刚记录下来的ip地址
adb connect #.#.#.#
```

*第六步* 拔掉usb连接线，确认设备是否依然可连接

```shell
adb devices
```

返回结果

```shell
#.#.#.#:5555 device
```

> **注意**: 确保本地和设备连接的wifi为可访问的同一个局域网

#### **adb disconnect**

断开通过TCP/IP连接的设备  

>*adb disconnect [&lt;host&gt; [:&lt;port&gt;]]*  

不带参数则断开所有TCP/IP连接的设备

#### **adb usb**

重启USB模式的 ADB

```shell
adb usb
```

### **App包的管理命令**

#### **adb install**

安装Android应用到设备，需要指定需要安装的 .apk 文件的全路径  

>*adb install [option] <path&gt;*


```shell
adb install test.apk
```

```shell
# 给apk上锁，发布 apk 到 android market上时，可以设置相关标志位来保护你的 app。
adb install -l test.apk
```
```shell
# 重新安装apk
adb install -r test.apk
```

```shell
# 允许测试
adb install -t test.apk
```

```shell
# 在sdcard上安装
adb install -s test.apk
```

```shell
#允许低版本代码
adb install -d test.apk
```

```shell
#授予所有运行权限
adb install -g test.apk
```

#### **adb install-multiple**

一次安装多个apk文件  

>*adb install-multiple [-lrtsdpg] <file...&gt;*  

参数用法和`adb install`相同

#### **adb uninstall**

从设备中卸载app  

>*adb uninstall [-k] <package&gt;*  

参数 -k 表示保留缓存和数据

```shell
adb uninstall com.test.app
```
```shell
adb uninstall -k com.test.app
```

#### **adb shell pm list packages**

打印出设备安装的所有包信息，可选参数用于过滤要显示的包名  

>*adb shell pm list packages [options] <FILTER&gt;*  

```shell
adb shell pm list packages
```

```shell
#查看相关的文件
adb shell pm list packages -f
```

```shell
#只显示禁用的packages
adb shell pm list packages -d
```
```shell
# 只显示可用的packages
adb shell pm list packages -e
```
```shell
#只显示系统级别的packages
adb shell pm list packages -s
```
```shell
#只显示第三方的packages(非系统)
adb shell pm list packages -3
```
```shell
#查看安装器(比如google play)
adb shell pm list packages -i
```
```shell
# 包括卸载的packages
adb shell pm list packages -u
```
```shell
#查询用户空间
adb shell pm list packages --user <USER_ID&gt;
```

#### **adb shell pm path**

打印制定APK的路径  

>*adb shell pm path &lt;PACKAGE&gt;*

```shell
adb shell pm path com.android.phone
```

返回结果

```shell
package:/system/priv-app/TeleService/TeleService.apk
```

#### **adb shell pm clear**

删除所有有关的数据  

>*adb shell pm clear <PACKAGE&gt;*

```shell
adb shell pm clear com.test.abc
```

返回结果

```shell
clearing app data, cache
```

### **文件管理**

#### **adb pull**

从设备下载指定的文件到电脑

> **adb pull &lt;remote&gt; [local]**

```shell
#下载 /sdcard/demo.mp4文件到 <android-sdk-path>/platform-tools 目录下
adb pull /sdcard/demo.mp4
```

```shell
#下载demo.mp4到 /Users/bsty/Desktop/
adb pull /sdcard/demo.mp4 /Users/bsty/Desktop/
```

#### **adb push**

从电脑上传指定文件到设备

> **adb push &lt;local&gt; &lt;remote&gt;**

```shell
#复制 <android-sdk-path>/platform-tools/test.apk 到设备的 /sdcard目录下
adb push test.apk /sdcard
```

```shell
#复制 /Users/bsty/Desktop/test.apk 到 /sdcard 目录下
adb push /Users/bsty/Desktop/test.apk /sdcard
```

#### **adb shell ls**

列出目录类容

> ** ls [option] &lt;directory&gt;**

第一步.

```shell
adb shell
```

第二步

```shell
ls
```

```shell
#显示隐藏的文件
ls -a 
```

```shell
#打印每个文件的序号
ls -i
```

```shell
#以块的形式打印出每个文件的大小
ls -s
```

```shell
#列出详细信息 UIDs和GIDs
ls -n
```

```shell
#列出所有子目录(递归)
ls -R 
```

**提示:** 按 Ctrl+C 终止

#### **adb shell cd**

切换目录

> **cd &lt;directory&gt;**

第一步

```shell
adb shell
```

第二步

```shell
cd /system
```

#### **adb shell rm**

删除文件或者目录

>**rm [option] &lt;files or directory&gt;**

第一步

```shell
adb shell
```

第二步

rm /sdcard/test.txt

```shell
#不需要提示强制删除
rm -f /sdcard/test.txt 
```

```shell
#删除所有子文件夹内容
rm -r /sdcard/tmp
```

```shell
#删除一个目录，即使不是空目录
rm -d /sdcard/tmp
```

**提示：** rm -d 和rmdir命令相同

```shell
#在删除前会有提示信息
rm -i /sdcard/test.txt
```

#### **adb shell mkdir**

创建文件夹

>**mkdir [options] &lt;directory name&gt;**

```shell
mkdir /sdcard/tmp
```

```shell
#设置权限
mkdir -m 777 /sdcard/tmp
```

```shell
#当需要时创建父目录
mkdir -p /sdcard/tmp/sub1/sub2
```

#### **adb shell touch**

创建空文件或者修改文件的时间戳

>**touch [options] &lt;file&gt;**

第一步
```shell
adb shell
```

第二步

```shell
touch /sdcard/tmp/test.txt
```

ls /sdcard/tmp

#### **adb pwd**

打印当前目录的位置

```shell
adb shell

pwd
```

#### **adb shell cp**

复制文件和目录

>**cp [options] &lt;source&gt; &lt;dest&gt;**

第一步

```shell
adb shell
```

第二步

```shell
cp /sdcard/test.txt /sdcard/demo.txt
```

#### **adb shell mv**

移动或者重命名文件

>**mv [options] &lt;source&gt; &lt;dest&gt;**

第一步

```shell
adb shell
```

第二步

```shell
#移动
mv /sdcard/tmp /system/tmp
```

```shell
#重命名
mv /sdcard/tmp /sdcard/test
```

### **网络**

#### **adb shell netstat**

网络统计

>**netstat**

第一步

```shell
adb shell
```

第二步

```shell
netstat
```

#### **adb shell ping**

测试两个网络之间的连接和延迟

第一步

```shell
adb shell
```

第二步

```shell
ping www.google.com
```

#### **adb shell netcfg**

通过配置文件管理和配置网络

>**netcfg [&lt;interface&gt; {dhcplupdown}]**

第一步

```shell
adb shell
```

第二步

```shell
netcfg
```

#### **adb shell ip**

显示，处理路由，设备，策略路由和隧道

>**ip [ OPTIONS ] OBJECT**

OBJECT := { link | addr | addrlabel | route | rule | neigh | ntable |tunnel | tuntap | maddr | mroute | mrule | monitor | xfrm |netns | l2tp }

OPTIONS := { -V[ersion] | -s[tatistics] | -d[etails] | -r[esolve] |-f[amily] { inet | inet6 | ipx | dnet | link } |-l[oops] { maximum-addr-flush-attempts } |-o[neline] | -t[imestamp] | -b[atch] [filename] |-rc[vbuf] [size]}

第一步

```shell
adb shell
```

第二步

```shell
#显示wifi IP地址
ip -f inet addr show wlan0
```

### **日志**

#### **adb logcat**

在屏幕上打印日志

>**adb logcat [option] [filter-specs]**

```shell
adb logcat
```

>**提示:** 按 Ctrl-C 停止

```shell
#最低优先级，只显示Verbose级别的日志
adb logcat *:V 
```

```shell
#只显示 Debug级别的日志
adb logcat *:D 
```

```shell
#只显示 info 级别的日志
adb logcat *:I 
```

```shell
#只显示Warning级别的日志
adb logcat *:W
```

```shell
#只显示Error级别的日志
adb logcat *:E
```

```shell
#只显示Fatal级别的日志
adb logcat *:F
```

```shell
# 最高优先级，没有打印过的日志
adb logcat *:S 
```

>adb logcat -b &lt;Buffer&gt;

```shell
#查看包含radio/telephony相关的消息缓冲区
adb logcat -b radio
```

```shell
# 包含事件相关的缓冲区
adb logcat -b event
```

```shell
#默认
adb logcat -b main
```

```shell
#清除整个日志并退出
adb logcat -c
```

```shell
#转储日志到屏幕并退出
adb logcat -d
```

```shell
#将日志信息写入test.logs文件
adb logcat -f test.logs
```

```shell
#打印指定日志buffer的大小并退出
adb logcat -g 
```

```shell
#设置日志的最大数
adb logcat -n <count>
```

>**提示：** 默认的值是4. 需要 -r 参数

```shell
#Rotates the log file every <kbytes> of output
adb logcat -r <kbytes>
```

>**提示：** 默认值为16， 需要-f参数

```shell
#设置默认 过滤器为 silent
adb logcat -s 
```

>**adb logcat -v &lt;format&gt;**

```shell
# 显示优先级/标签并在过程中发出消息（默认格式）的PID。
adb logcat -v brief
```

```shell
# 只显示 PID
adb logcat -v process
```

```shell
#只显示优先级和标签
adb logcat -v tag
```

```shell
#显示原始日志信息，没有其他数据字段
adb logcat -v raw
```

```shell
#显示日期，调用时，优先级/标签，进程发出消息的PID。
adb logcat -v time
```

```shell
#显示日期，调用时间，优先级，标记，和该线程发出消息的PID和TID
adb logcat -v threadtime
```

```shell
#显示所有元数据字段和空行分开的消息
adb logcat -v long
```

#### **adb shell dumpsys**

转储系统数据

>**adb shell dumpsys [options]**

```shell
adb shell dumpsys
```

```shell
adb shell dumpsys battery
```

```shell
#搜集设备的电池信息
adb shell dumpsys batterystats
```

```shell
#清除旧信息
adb shell dumpsys batterystats -reset 
```

#### **adb shell dumpstate**

转储状态

```shell
adb shell dumpstate
```

```shell
#转储信息存到一个文件
adb shell dumpstate > state.logs
```

### **截屏**

#### **adb shell screencap**

>**adb shell screencap &lt;filename&gt;**

```shell
adb shell screencap /sdcard/screen.png
```

从设备下载一个文件

```shell
adb pull /sdcard/screen.png
```

#### **adb shell screenrecord**

录制屏幕，android4.4(api 19)以上可用

>**adb shell screenrecord [options] &lt;filename&gt;**

```shell
adb shell screenrecord /sdcard/demo.mp4
```

(按 Ctrl-C停止录屏)

从设备下载该录像文件

```shell
adb pull /sdcard/demo.mp4
```

>**提示：**按Ctrl-C停止录屏，默认3分钟自动停止，也可以添加参数 --time-limit 设置录制时间

```shell
#设置视频大小：1280*720. 默认为设备分辨率，最好使用设备支持的分辨率
adb shell screenrecord --size <WIDTH*HEIGHT>
```

```shell
#设置视频的bit比，默认4Mbps，可以增加比例提升视频清晰度，但是也会增大文件大小,
#例子: bit比为5Mbps， *** adb shell screenrecord --bit-rate 5000000 /sdcard/demo.mp4
adb shell screenrecord --bit-rate <RATE>
```

```shell
#设置最长（秒），默认为180秒（3分钟）
adb shell screenrecord --time-limit <TIME>
```

```shell
#旋转输出90度。实验性功能
adb shell screenrecord --rotate
```

```shell
#控制台显示日志信息，如果没有设置该参数，不会在录屏是显示任何信息
adb shell screenrecord --verbose
```

### **系统**

#### **adb root**

以root权限重新启动adbd守护进程

```shell
adb root
```

>**注意：**在生产环境中adbd不能以root模式执行,只能用于测试

#### **adb sideload**

恢复Andr​​oid update.zip包。

```shell
adb sideload <upload.zip>
```

#### **adb shell ps**

打印进程状态信息

>**ps [options]**

第一步

```shell
adb shell
```

第二步

```shell
ps
```

ps -p

#### **adb shell top**

显示顶层的cpu进程

>**top [options]**

第一步

```shell
adb shell
```

第二步

```shell
top
```

>**提示：** 按Ctrl-C停止**

```shell
#以线程形式展示
top -t 
```

#### **adb shell getprop**

通过property service获取设备属性

>**getprop [options]**

第一步

```shell
adb shell
```

第二步

```shell
getprop

getprop ro.build.version.sdk

getprop  ro.chipname

getprop | grep adb 

```

#### **adb shell setprop**

设置property service

>**setprop &lt;key&gt; &lt;value&gt;

第一步

```shell
adb shell
```

第二步

```shell
setprop service.adb.tcp.prot 5555
```


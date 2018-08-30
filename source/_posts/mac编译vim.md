---
title: mac编译vim
date: 2018-08-30 17:28:19
tags:
---

## VIM安装

### 卸载brew 安装的vim

    brew uninstall vim

### 下载源码包

[vim 8.1.0197 下载地址](https://codeload.github.com/vim/vim/tar.gz/v8.1.0197)

### 编译安装

    # 解压
    $ tar -xzvf ~/Downloads/vim-8.1.0197.tar.gz

    $ cd vim-8.1.0197

    # 查看编译支持的选项
    $ ./configure -h

    # 编译选项配置: python2/3 perl ruby lua
    ./configure \
    --enable-multibyte \
    --enable-perlinterp=dynamic \
    --enable-rubyinterp=dynamic \
    --with-ruby-command=/usr/local/bin/ruby \
    --enable-pythoninterp=dynamic \
    --with-python-config-dir=/usr/lib/python2.7/config \
    --enable-python3interp \
    --with-python3-config-dir=/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/config-3.7m-darwin \
    --enable-luainterp \
    --with-lua-prefix=/usr/local/Cellar/lua/5.3.5_1 \
    --enable-cscope \
    --enable-gui=auto \
    --with-features=huge \
    --enable-fontset \
    --enable-largefile \
    --disable-netbeans \
    --enable-fail-if-missing \
    --prefix=/usr/local/vim8

    # 编译安装
    $ make && make install

    # 确认是否安装成功: 并可以检查是否支持了 python2/3 lua等解释器
    # 由于设置了 --prefix=/usr/local/vim8 所以全路径是这样
    $ /usr/local/vim8/bin/vim --version

    # 查看 老的vim路径, 并删除掉
    $ ll `which vim`
    $ rm /usr/local/bin/vim

    # 创建软链
    $ ln -s /usr/local/vim8/bin/vim /usr/local/bin/vim

    # 再次查看版本号, 确定是否已经支持
    $ vim --version

> 注意python 和lua的本地路径是否对应
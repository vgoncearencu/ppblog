title: Deploy PPMessage on Mac
date: 2016-03-08 10:08:55
tags:
---
### 前言
> 部署 PPMessage 对 Mac OS X 系统版本并无要求，建议使用最新版本。
>
> 以下说明在 OS X EI Captain 10.11.2 测试通过。

*** 

### 具体部署流程

1. 安装 brew
    
        ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

2. brew 安装依赖
        
        brew install hg autoconf libtool automake redis libmagic mysql libjpeg libffi fdk-aac lame mercurial
        brew tap homebrew/services
        brew tap homebrew/nginx
        brew install nginx-full --with-upload-module
        brew install ffmpeg --with-fdk-aac --with-opencore-amr --with-libvorbis --with-opus

3. pip 安装 Python 模块

    安装命令：
       
        sudo pip install -i http://pypi.douban.com/simple axmlparserpy beautifulsoup4 biplist evernote filemagic geoip2 green git+https://github.com/senko/python-video-converter.git hg+https://dingguijin@bitbucket.org/dingguijin/apns-client identicon ipython jieba paramiko paho-mqtt pillow ppmessage-mqtt pyipa pypinyin pyparsing python-dateutil python-gcm python-magic qiniu qrcode readline redis rq supervisor sqlalchemy tornado xlrd numpy matplotlib scipy scikit-learn
    
    *说明1：类似 `git+https://github.com/senko/python-video-converter.git` 模块的安装方法与普通 pip 安装方法无异。例如：*
       
        sudo pip install git+https://github.com/senko/python-video-converter.git
       
    *说明2：可以通过国内 pip 源进行安装，下载速度更快。例如使用豆瓣源安装 axmlparsepy。*
   
        sudo pip install -i http://pypi.douban.com/simple axmlparsepy
        
    *说明3：在 Mac OS X Captain 下安装 green 可能出现以下错误：*
            
        OSError: [Errno 1] Operation not permitted: '/var/folders/nj/ky4gzkdn0db_wdxxyph3j93h0000gn/T/pip-xoS3tF-uninstall/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/six-1.4.1-py2.7.egg-info'
    *可用以下命令安装 green*
        
        sudo pip install green --ignore-installed six
        
    *安装其他模块也可能出现类似错误，在这种情况下，只需要使用 '--ignore-installed xxx' 即可*
   
4. 下载、编译、安装其他依赖

    其他依赖包括 mysql-connector-python, libmaxminddb
    1. 安装 mysql-connector-python
        
            wget http://cdn.mysql.com//Downloads/Connector-Python/mysql-connector-python-2.1.3.tar.gz
            tar -xzvf mysql-connector-python-2.1.3.tar.gz
            cd mysql-connector-python-2.1.3
            sudo python setup.py install
    
    2. 安装 libmaxminddb
    
            git clone --recursive https://github.com/maxmind/libmaxminddb
            cd libmaxminddb
            ./bootstrap
            ./configure
            make && sudo make install

***

### 快速部署脚本

    #! /bin/bash

    # version: 0.1
    # maintainer: Jin He <jin.he@ppmessage.com>
    # description: a shell script to deploy PPMessage on Mac

    MYSQL_CONNECTOR_PYTHON_VERSION=2.1.3

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

    brew install \
        autoconf \
        automake \
        fdk-aac \
        hg \
        libtool \
        libmagic \
        libjpeg \
        libffi \
        lame \
        mysql \
        mercurial \
        redis
     
    brew tap homebrew/services
    brew tap homebrew/nginx
    brew install nginx-full --with-upload-module
    brew install ffmpeg --with-fdk-aac --with-opencore-amr --with-libvorbis --with-opus


    # In Mac OS X EI Captain, if your encount below error when install green,

    # OSError: [Errno 1] Operation not permitted: '/var/folders/nj/ky4gzkdn0db_wdxxyph3j93h0000gn/T/pip-xoS3tF-uninstall/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/six-1.4.1-py2.7.egg-info'

    # try this command to install green:

    # sudo pip install green --ignore-installed six

    # Installing other package might have similar problem. In that case, use "--ignore-installed xxx" should do the trick.

    sudo pip install -i http://pypi.douban.com/simple \
        axmlparserpy \
        beautifulsoup4 \
        biplist \
        evernote \
        filemagic \
        geoip2 \
        green \
        git+https://github.com/senko/python-video-converter.git \
        hg+https://dingguijin@bitbucket.org/dingguijin/apns-client \
        identicon \
        ipython \
        jieba \
        paramiko \
        paho-mqtt \
        pillow \
        ppmessage-mqtt \
        pyipa \
        pypinyin \
        pyparsing \
        python-dateutil \
        python-gcm \
        python-magic \
        qiniu \
        qrcode \
        readline \
        redis \
        rq \
        supervisor \
        sqlalchemy \
        tornado \
        xlrd \
        numpy \
        matplotlib \
        scipy \
        scikit-learn

    cd /tmp
    wget http://cdn.mysql.com//Downloads/Connector-Python/mysql-connector-python-$MYSQL_CONNECTOR_PYTHON_VERSION.tar.gz
    tar -xzvf mysql-connector-python-$MYSQL_CONNECTOR_PYTHON_VERSION.tar.gz
    cd mysql-connector-python-$MYSQL_CONNECTOR_PYTHON_VERSION
    sudo python setup.py install

    cd /tmp
    git clone --recursive https://github.com/maxmind/libmaxminddb
    cd libmaxminddb
    ./bootstrap
    ./configure
    make && sudo make install

    echo "Finish deployment successfully, have fun with PPMessage"

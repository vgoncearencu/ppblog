title: Deploy PPMessage on Linux
date: 2016-03-08 10:23:00
tags:
description: Deploy PPMessage on Linux
---
### 前言
> 推荐的 Linux 发行版是 Debian:jessie / Ubuntu 14.04，在这两个系统上部署 PPMessage 的流程大同小异。
> 
> 以下说明以 Debian:jessie 为例，实际情况下部分命令可能需要 `sudo` 才可执行。

***

### 具体部署流程

1. 更新软件源

    在国内，使用国内软件源比使用官方源下载速度要快很多，因此首先更新 `/etc/apt/sources.list`
        
        deb http://mirrors.163.com/debian/ jessie main non-free contrib
        deb http://mirrors.163.com/debian/ jessie-updates main non-free contrib
        deb http://mirrors.163.com/debian/ jessie-backports main non-free contrib
        deb http://mirrors.163.com/debian-security/ jessie/updates main non-free contrib
    
    然后执行
        
        sudo apt-get update
        
2. apt-get 安装依赖

   所有需要安装的依赖润软件如下 (在Ubuntu下需要把 libjpeg62-turbo-dev 替换为 libjpeg8-dev)：
        
       sudo apt-get install -y apt-file apt-utils autoconf automake gcc git g++ libblas-dev liblapack-dev libatlas-base-dev gfortran libffi-dev libfdk-aac-dev libfreetype6-dev libjpeg62-turbo-dev libmagic1 libmp3lame-dev libncurses5-dev libopencore-amrwb-dev libopencore-amrnb-dev libopus-dev libpng12-dev libpcre3-dev libssl-dev libtool mercurial mysql-server openssl pkg-config python python-dev python-pip redis-server wget

3. pip 安装 Python 模块

    安装命令：
       
        sudo pip install -i http://pypi.douban.com/simple axmlparserpy beautifulsoup4 biplist evernote filemagic geoip2 green git+https://github.com/senko/python-video-converter.git hg+https://dingguijin@bitbucket.org/dingguijin/apns-client identicon ipython jieba paramiko paho-mqtt pillow ppmessage-mqtt pyipa pypinyin pyparsing python-dateutil python-gcm python-magic qiniu qrcode readline redis rq supervisor sqlalchemy tornado xlrd numpy matplotlib scipy scikit-learn
    
    *说明1：类似 `git+https://github.com/senko/python-video-converter.git` 模块的安装方法与普通 pip 安装方法无异。例如：*
       
        sudo pip install git+https://github.com/senko/python-video-converter.git
       
    *说明2：可以通过国内 pip 源进行安装，下载速度更快。例如使用豆瓣源安装 axmlparsepy。*
   
        sudo pip install -i http://pypi.douban.com/simple axmlparsepy
   
4.  手动下载、编译、安装其他依赖
    
    其他依赖包括 mysql-connector-python, ffmpeg, nginx, libmaxminddb
    1. 安装 mysql-connector-python
    
            wget http://cdn.mysql.com//Downloads/Connector-Python/mysql-connector-python-2.1.3.tar.gz
            tar -xzvf mysql-connector-python-2.1.3.tar.gz
            cd mysql-connector-python-2.1.3
            sudo python setup.py install
 
    2. 安装 ffmpeg
        
            wget http://ffmpeg.org/releases/ffmpeg-2.8.5.tar.bz2
            tar -xjvf ffmpeg-2.8.5.tar.bz2
            cd ffmpeg-2.8.5
            ./configure --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-version3 --enable-nonfree --disable-yasm --enable-libmp3lame --enable-libopus --enable-libfdk-aac
            make && sudo make install
            
    3. 安装 nginx
    
            wget http://nginx.org/download/nginx-1.8.0.tar.gz
            git clone https://github.com/vkholodkov/nginx-upload-module.git
            cd nginx-upload-module && git checkout 2.2 && cd ../
            tar -xzvf nginx-1.8.0.tar.gz
            cd nginx-1.8.0 
            ./configure --with-http_ssl_module --add-module=../nginx-upload-module 
            make && sudo make install
    4. 安装 libmaxminddb
        
            git clone --recursive https://github.com/maxmind/libmaxminddb
            cd libmaxminddb
            ./bootstrap
            ./configure
            make && sudo make install
***

### 快速部署脚本
将整个部署流程合并为一个脚本：[deploy-ppmessage-on-linux.sh](https://github.com/PPMESSAGE/ppmessage/blob/master/ppmessage/deploy/deploy-ppmesage-on-linux.sh)
  
    
***
### Have fun with PPMessage !

title: Run PPMessage on Mac OS X
date: 2016-03-04 15:47:19
tags:
---

>PPMessage is `An Open Source Plug & Play Online Customer Service Platform`. Base on PPMessage, you can chat with your customer via Web and mobile real time. 

# Run PPMessage on Mac OS X

> Want to know more (install PPMessage on other OS platform, configuration etc.), please go to [PPMessage Site](http://ppmessage.com) get more documents about PPMessage.

## Requirements

* Homebrew
  * Download and install from [http://brew.sh](http://brew.sh)
  ```Bash
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
  ```
  
  * Install command
  ```Bash
    brew install hg autoconf libtool automake redis libmagic mysql libjpeg libffi fdk-aac lame mercurial
    brew tap homebrew/services
    brew tap homebrew/nginx
    brew install nginx-full --with-upload-module
    brew install ffmpeg --with-fdk-aac --with-opencore-amr --with-libvorbis --with-opus
  ```

  * Or use `ppmessage/scripts/install_brews.py` to install automatically

* Manual download
  * Apns-client source
  ```Bash
    hg clone https://dingguijin@bitbucket.org/dingguijin/apns-client
    cd ./apns-client; sudo -H python setup.py install; cd -
  ```
  * Geolite2 library source
  ```Bash
    git clone --recursive https://github.com/maxmind/libmaxminddb
    cd libmaxmindb; ./bootstrap; ./configure; make; make install; cd -
  ```

  * Geolite2 DB (assuming you have cloned the ppmessage source, and under the root `ppmessage` directory.)
  ```Bash
    cd ppmessage/api/geolite2
    wget http://geolite.maxmind.com/download/geoip/database/GeoLite2-City.mmdb.gz
    gunzip GeoLite2-City.mmdb.gz
    cd -
  ```
  > sh ppmessage/scripts/download_geolite2.sh is an alternative scripts way which PPMessage provides.

  * Mysql connector python
  ```Bash
    wget -c http://dev.mysql.com/get/Downloads/Connector-Python/mysql-connector-python-2.1.3.tar.gz
    tar zxvf mysql-connector-python-2.1.3.tar.gz
    cd mysql-connector-python-2.1.3
    sudo python setup.py install
    cd -
  ```

* Python pip
  * Install command
  ```Bash
    sudo -H pip install AxmlParserPY beautifulsoup4 biplist certifi cffi chardet cryptography evernote filemagic geoip2 green identicon ipaddr jieba matplotlib maxminddb numpy paho-mqtt paramiko Pillow ppmessage-mqtt pyOpenSSL pyparsing pypinyin python-dateutil python-gcm qiniu qrcode readline redis requests rq scikit-learn scipy six SQLAlchemy supervisor tornado xlrd
  ```
  * Or use `ppmessage/scrips/install_pips.py` to install automatically

## Config mysql and redis

* Mysql user and password (replace DB_PASSWORD with what you want to set as db password)

```Bash
    brew services list
    brew services start mysql
    mysqladmin -uroot password DB_PASSWORD
```

* Redis

  * Start redis
```Bash
    brew services list
    brew services start redis
```
  * Config redis 
```Bash
    vim /usr/local/etc/redis.conf and comment all save lines
    > # save 900 1
    > # save 300 10
    > # save 60 10000
```

## Config and prepare PPMessage

> Assuming you have clone ppmessage and under ppmessage directory.
> In the directory, you can see `dist.sh` `README.md` files and `ppmessage` directory.

Before config PPMessage check the requirement is done or not.

```Bash
    python ppmessage/scripts/require.py
```

```Bash
    vim ppmessage/bootstrap/config.py
    ...
    python ppmessage/scripts/table.py
    python ppmessage/scripts/bootstrap.py
    python ppmessage/scripts/db2cache.py
```

### Generate PPCom - web version

```Bash
    cd ppmessage/ppcom/web
    bower install
    cd -
    cd ppmessage/ppcom/web/gulp
    npm install
    gulp
```

### Generate PPKefu - web version

```Bash
    cd ppmessage/ppkefu/ppkefu
    bower install
    npm install
    gulp
```

### Generate PPConsole

```Bash
    cd ppmessage/ppconsole
    bower install
    cd -
    cd ppmessage/ppconsole/gulp
    npm install
    gulp
```

### Start/Stop PPMessage server

```Bash
    sh dist.sh start
    sh dist.sh stop
```

### See log

```Bash
    sh dist.sh log
```

> Check PPMessage status
> sh dist.sh proc


### Check PPCOM

Use your brower open your server url which has been configed, suggest using Chrome browser.

> PPCONSOLE/PPCOM
```Bash
http://server_name:nginx_listen_port
```

> PPKEFU
```Bash
http://server_name:nginx_listen_port/ppkefu
```

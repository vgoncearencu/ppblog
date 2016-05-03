title: Deploy PPMessage with Docker
date: 2016-03-04 20:07:21
tags:
description: deploy ppmessage with docker

---
要想使用或者开发 PPMessage，首先必须搭建 PPMessage 开发环境。即使对于 PPMessage 团队的开发人员，从头开始搭建开发环境也是一件费时费力的事情。幸运的是，借助 Docker 的魔力，这些困难已经迎刃而解。

*** 

### 前言：Docker 是什么 ？
> Docker allows you to package an application with all of its dependencies into a standardized unit for software development. Docker containers wrap up a piece of software in a complete filesystem that contains everything it needs to run: code, runtime, system tools, system libraries – anything you can install on a server. This guarantees that it will always run the same, regardless of the environment it is running in.

简而言之，Docker 的作用就是将软件和它运行时的所有依赖打包成为一个标准的 Docker 镜像。借助于此 Docker 镜像，该软件可以在任何支持 Docker 的机器上运行，并且效果完全一样。

***

### 使用 Docker 部署 PPMessage
使用 Docker 部署 PPMessage，可以降低使用和开发 PPMessage 的门槛。部署过程如下所述：

#### 1. 了解、安装 Docker
首先仔细阅读 Docker 的官方引导文档 [docker getting started](https://docs.docker.com/mac/) ，了解 Docker 的基本概念和使用方法。进入下一步之前确认你已经了解以下概念:

* Docker Terminal：Linux 下打开系统 Terminal 即可，Mac 和 Windows 下必须通过 Docker Quickstart Terminal 打开。只有在 Docker Terminal 里才可以使用 docker 命令。
* docker pull some-image : 从 dockerhub 下载某个 docker 镜像

#### 2. 下载 PPMessage Docker 镜像
PPMessage Docker 镜像提供了 PPMessage 运行环境。 

打开 Docker Terminal，输入命令：
    
    docker pull ppmessage/ppmessage


#### 3. 下载 PPMessage 源代码
PPMessage 的源代码托管在 [github](https://github.com/PPMESSAGE/ppmessage) 上。

假设你将 PPMessage 下载到 `~/Documents` 下。

    cd ~/Documents
    git clone git@github.com:PPMESSAGE/ppmessage.git


#### 4. 启动 PPMessage
1. 首先配置 PPMessage，打开并编辑 `~/Documents/ppmessage/bootstrap/config.py`，示例如下：
        
        BOOTSTRAP_CONFIG = {
            # your team
            "team": {
                "app_name": "ppmessage",
                "company_name": "YOURUI",
            },
            
            # the super user
            "user": {
                "user_language": "zh_cn", # zh_cn, en_us, zh_tw
                "user_firstname": "",
                "user_lastname": "",
                "user_fullname": "",
                # email is user account
                "user_email": "",
                "user_password": "",
            },

            "mysql": {
                "db_host": "127.0.0.1",
                "db_user": "root",
                "db_pass": "test",
                "db_name": "ppmessage",
            },

            "server": {
                "name": "", # `` PPMessage use the host ip address otherwise fill it with host name like `ppmessage.com`/`www.ppmessage.com`
                "identicon_store": "/usr/local/opt/ppmessage/identicon",
                "generic_store": "/usr/local/opt/ppmessage/generic",
            },

            "js": {
                "min": "no", # `yes` or `no` for minimized the PPCOM/PPKEFU javascript file
            },
    
            # nginx conf 
            "nginx": {
                "nginx_conf_path": "/usr/local/nginx/conf/nginx.conf",
                "server_name": ["ppmessage.com", "www.ppmessage.com"],
                "listen": "8080", #80

                "upload_store": "/usr/local/opt/ppmessage/uploads 1",
                "upload_state_store": "/usr/local/opt/ppmessage/upload_state",

                "ssl": "off", # off/on
                "ssl_listen": "443",
                "ssl_certificate": "",       # certificate
                "ssl_certificate_key": "",   # certificate key
            },

            # apns push certificate, dev for developer, pro for production
            "apns": {
                "name": "",      # iOS app id
                "dev": "",       # iOS app development certificate
                "pro": "",       # iOS app production certificate
            },

            # google cloud message
            "gcm": {
                "api_key": "",   # google cloud message api key
            },

            # iOS app code signing
            "ios": {
                "code_sign_identity": "",     # iOS app code sign identity 
                "provisioning_profile": "",   # iOS app provisioning profile
            },
        }

2. 然后打开 Docker Terminal, 输入命令:

        docker run -it -p 8080:8080 -v ~/Documents/ppmessage:/ppmessage ppmessage/ppmessage
        
   `-p 8080:8080` 表示将本地的8080端口映射到 docker 容器的8080端口，如此就可以通过本地的8080端口访问在 docker 中运行的 PPMessage。
   
   `-v ~/Documents/ppmessage:/ppmessage` 表示将本地的PPMessage 源码目录映射到 docker 容器的/ppmessage 目录，如此才可实现在本机开发源码，在docker里运行代码。
   
   也可以通过运行脚本 `~/Documents/ppmessage/ppmessage/docker/run-ppmessage.sh` 来启动 PPMessage。

3. 访问 PPMessage

    * 在Linux 下，你可以直接通过 `http://localhost:8080` 访问 PPMessage，例如：

            http://localhost:8080/api
            http://localhost:8080/ppkefu
            http://localhost:8080/pphome
            ...
    * 在 Mac 和 Windows下，需要先获取 docker 虚拟机 IP。打开 Docker Terminal，输入命令：
    
            docker-machine ip default
            
        这会给出 docker 虚拟机的 IP 地址，例如 `192.168.99.100`。然后通过 `http://192.168.99.100:8080`访问 PPMessage
    
#### 4. 关闭、重新启动 PPMessage

通过 docker run 启动 PPMessage 总是会新建一个 docker 容器，然后在新容器中初始化并运行 PPMessage。实际上我们总是希望可以重用第一次创建的 docker 容器，因为这样才可以保留 PPMessage 使用过程中产生的数据。

* 查看所有容器

        docker ps -a
    这会给出所有docker 容器的列表，你需要记住 PPMessage 容器的 id，以便对其进行关闭、启动、重启操作
    
* 关闭容器
        
        docker stop container_id
        
* 启动容器

        docker start container_id
        
* 重启容器

        docker restart container_id

* 删除容器

        docker rm contaniner_id



#### Have fun with PPMessage!
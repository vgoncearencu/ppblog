title: Deploy PPMessage with Docker
date: 2016-03-04 20:07:21
tags:
description: 使用 Docker 搭建 PPMessage 开发环境

---
要想使用或者开发 PPMessage，首先必须搭建 PPMessage 开发环境。即使对于 PPMessage 团队的开发人员，从头开始搭建开发环境也是一件费时费力的事情。幸运的是，借助 Docker 的魔力，这些困难已经迎刃而解。

***

### 前言：Docker 的作用 ？
> Docker allows you to package an application with all of its dependencies into a standardized unit for software development. Docker containers wrap up a piece of software in a complete filesystem that contains everything it needs to run: code, runtime, system tools, system libraries – anything you can install on a server. This guarantees that it will always run the same, regardless of the environment it is running in.

简而言之，Docker可以将程序运行时的所有依赖软件打包成为一个标准的 Docker 镜像。借助于此 Docker 镜像，该程序可以在任何支持 Docker 的机器上运行，并且效果完全一样。

### 搭建PPMessage开发环境
使用 Docker 搭建 PPMessage 开发环境，可以降低使用和开发 PPMessage 的门槛。

#### 关于 Docker
首先仔细阅读 Docker 的官方引导文档 [docker getting started](https://docs.docker.com/mac/) ，了解 Docker 的基本概念和使用方法。

当 Docker 安装完毕后，打开 Terminal 验证 Docker 是否可用。

**Mac 下**， 点击 **启动面板(LaunchPad)** 里的 Docker Quickstart Terminal 打开 Terminal。等待docker启动完毕，输入命令

    docker info

如果没有显示错误，说明 Docker 已经可用。需要注意的是，在Mac下必须通过Docker Quickstart Terminal 打开的 Terminal才可以使用Docker命令。

**Linux 下**，打开系统Terminal， 输入命令

    docker info

如果提示以下错误：

    Cannot connect to the Docker daemon. Is 'docker -d' running on this host?

则先尝试重启 Docker 服务

    sudo service docker restart

如果无效的话，则执行

    sudo usermod -aG docker $USER

然后重新登录当前用户，在打开系统Terminal，就应该可以使用 Docker 命令了。

#### 下载 PPMessage Docker镜像
PPMessage Docker镜像托管在Docker Hub上，镜像大小约为480MB。

要下载它，打开 Terminal，输入命令：

    docker pull ppmessage/ppmessage

下载过程可能比较漫长，请继续看下面两个步骤：**下载 PPMessage 源码**， **配置PPmessage**

#### 下载 PPMessage 源码
PPMessage 源码托管在 [github](https://github.com/PPMESSAGE/ppmessage) 上。

假设你将 PPMessage 下载到 `~/Documents` 下。

    cd ~/Documents
    git clone git@github.com:PPMESSAGE/ppmessage.git

#### 配置 PPMessage
在启动 PPMessage之前，你需要先生成PPMessage的配置文件。

进入目录 `~/Documents/ppmessage/bootstrap`，然后执行命令：

    cp config.template.py config.py

请按照配置文件里的注释修改`config.py`：

    # -*- coding: utf-8 -*-
    #
    # Copyright (C) 2010-2016 PPMessage.
    # Guijin Ding, dingguijin@gmail.com
    # All rights reserved
    #

    """
    BOOSTRAP_CONFIG is the first place for developer edit before run PPMessage.

    required:
    "team", to run PPMessage needing a team who is the first service team of the PPMessage
    "user", to run PPMessage needing a user who create the first service team and admin the whole PPMessage system
    "mysql", database config
    "nginx", nginx config
    "js",   client javascript minimization

    optional:
    "apns", apple push service
    "gcm",  google clould message

    """

    BOOTSTRAP_CONFIG = {
        # This team is the super admin's team
        "team": {
            "app_name": "your-team-name",            # your service team name
            "company_name": "your-company-name",     # your company name
        },

        # This user is the super user, who has two roles:
        # 1. the team admin of first service team
        # 2. the super admin of all service teams
        "user": {
            "user_language": "zh_cn", # zh_cn, en_us, zh_tw
            "user_firstname": "your-first-name",
            "user_lastname": "your-last-name",
            "user_fullname": "your-fullname",
            "user_email": "your-login-email",
            "user_password": "your-password",
        },

        "mysql": {
            "db_host": "127.0.0.1",
            "db_user": "root",
            "db_pass": "test",    # If you use docker to launch ppmessage, please set db_pass to 'test'
            "db_name": "ppmessage",
        },

        "server": {
            "name": "", # server's ip, like '192.168.0.110', PPKefu need this to know which server to connect
            "identicon_store": "/usr/local/opt/ppmessage/identicon",
            "generic_store": "/usr/local/opt/ppmessage/generic",
        },

        "js": {
            "min": "no", # `yes` or `no` for minimized the PPCOM/PPKEFU javascript file
        },

        # nginx conf
        # In Ubuntu/Debian/Docker, "nginx_conf_path" is "/usr/local/nginx/conf/nginx.conf"
        # In Mac, "nginx_conf_path" is "/usr/local/etc/nginx/nginx.conf"
        "nginx": {
            "nginx_conf_path": "/path-to-your-nginx.conf",
            "server_name": ["ppmessage.com", "www.ppmessage.com"],
            "listen": "8080", #80

            "upload_store": "/usr/local/opt/ppmessage/uploads 1",
            "upload_state_store": "/usr/local/opt/ppmessage/upload_state",

            # on for https, off for http
            "ssl": "off", # off/on
            "ssl_listen": "443",
            "ssl_certificate": "/path-to-your-ssl-cert",
            "ssl_certificate_key": "/path-to-your-ssl-server-key",
        },

        # optional, apns push certificate, dev for developer, pro for production
        "apns": {
            "name": "your-app's-bundle-id",
            "dev": "/path-to-your-apns-development.p12",
            "pro": "/path-to-your-apns-production.p12",
        },

        # optional, google cloud message
        "gcm": {
            "api_key": "your gcm apikey",
            "sender_id": "your gcm sender id",
        },
    }

在编辑，保存配置文件之后，移动到目录`~/Documents/ppmessage`


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

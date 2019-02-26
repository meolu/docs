title: Docker安装
---
## 鸣谢
此篇由[焦富涛](https://github.com/owen-carter)贡献，为社区提供了 Docker 版本。人人为我，我为人人！而且极其注重细节，甚至细腻到中英文之间空格的控制。

## About Docker
Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。


## [Install Docker](https://docs.docker.com/install/linux/docker-ce)
我们重点说明docker在centos和ubuntu操作系统下的安装和使用，Mac下安装比较简单，直接下载dmg镜像安装即可，使用和centos，ubuntu基本无异，
不推荐在windows下部署，会有文件路径问题抽风。
- [Centos系统](https://docs.docker.com/install/linux/docker-ce/centos)
```bash
sudo yum install -y yum-utils  device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo  https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce -y
sudo systemctl enable docker
sudo systemctl start docker
```


- [Ubuntu系统](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
```bash
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update   
sudo apt-get install docker-ce
```

- [Mac系统](https://docs.docker.com/docker-for-mac/install/#install-and-run-docker-for-mac)

[https://docs.docker.com/docker-for-mac/install/#install-and-run-docker-for-mac](https://docs.docker.com/docker-for-mac/install/#install-and-run-docker-for-mac)

- [Windows](https://docs.docker.com/docker-for-windows/install/#start-docker-for-windows)

[https://docs.docker.com/docker-for-windows/install/#start-docker-for-windows](https://docs.docker.com/docker-for-windows/install/#start-docker-for-windows)



## [Install docker-compose](https://docs.docker.com/compose/overview/)

install [docker-compose](https://docs.docker.com/compose/overview/)
```bash
pip install docker-compose -i https://mirrors.aliyun.com/pypi/simple/
```

## NEW environment file
在docker-compose.yml同级目录新建walle.env，连接数据库MYSQL_USER默认使用root,如需使用其他用户，需自建用户更改walle.env文件
vi walle.env
```shell
# Set MySQL/Rails environment
MYSQL_USER=root
MYSQL_PASSWORD=walle
MYSQL_DATABASE=walle
MYSQL_ROOT_PASSWORD=walle
```

## Prepare Before Deploy
vim docker-compose.yml
```yaml
version: '3.7'
services:
  web:
    image: alenx/walle-web:2.0
    container_name: walle-nginx
    hostname: nginx-web
    ports:
    # 如果宿主机80端口被占用，可自行修改为其他port(>=1024)
    # 0.0.0.0:要绑定的宿主机端口:docker容器内端口80
      - "80:80"
    links:
      - python
    depends_on:
      - python
    networks:
      - walle-net
    restart: always

  python:
    # 默认使用alenx/walle-python:2.0
    # maven工程使用alenx/walle-java:2.0; maven:3.6.0, jdk:1.8.0_181
    image: alenx/walle-python:2.0
    #    image: alenx/walle-java:2.0
    container_name: walle-python
    hostname: walle-python
    env_file:
      # walle.env需和docker-compose在同级目录
      - walle.env
    command: bash -c "cd /opt/walle-web/ && /bin/bash admin.sh migration && python waller.py"
    expose:
      - "5000"
    volumes:
      - /tmp/walle/codebase/:/tmp/walle/codebase/
      - /tmp/walle/logs/:/opt/walle-web/logs/
      - /root/.ssh:/root/.ssh/
    links: 
      - db
    depends_on:
      - db
    networks:
      - walle-net
    restart: always

  db:
    image: mysql
    container_name: walle-mysql
    hostname: walle-mysql
    env_file:
      - walle.env
    command: --default-authentication-plugin=mysql_native_password
    ports:
      - "3306:3306"
    expose:
      - "3306"
    volumes:
      - /data/walle/mysql:/var/lib/mysql
    networks:
      - walle-net
    restart: always

networks:
  walle-net:
    driver: bridge
```


## Start

- 一键启动（快速体验）
```bash
docker-compose up -d && docker-compose logs -f
# 打开浏览器localhost:80
```
初始登录账号如下，开启你的walle 2.0之旅吧：）
```
超管：super@walle-web.io \ Walle123
所有者：owner@walle-web.io \ Walle123
负责人：master@walle-web.io \ Walle123
开发者：developer@walle-web.io \ Walle123
访客：reporter@walle-web.io \ Walle123
```


- 常用操作
```bash
# 构建服务
docker-compose build
# 启动服务,启动过程中可以直接查看终端日志，观察启动是否成功
docker-compose up
# 启动服务在后台，如果确认部署成功，则可以使用此命令，将应用跑在后台，作用类似 nohup python waller.py &
docker-compose up -d
# 查看日志,效果类似 tail -f waller.log
docker-compose logs -f
# 停止服务,会停止服务的运行，但是不会删除服务所所依附的网络，以及存储等
docker-compose stop
# 删除服务，并删除服务产生的网络，存储等，并且会关闭服务的守护
docker-compose down
```

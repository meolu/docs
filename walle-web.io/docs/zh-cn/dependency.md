title: 依赖
---

* Bash(git、ssh)
    * 意味着不支持win、mac的zsh
* LNMP/LAMP(php5.4+)
    * php需要开启pdo_mysql，exec函数执行
* Composer
    * 如果国内环境安装极慢，可以直接下载[vendor](http://pan.baidu.com/s/1c0wiuyc)解压到项目根目录
* ansible

## 安装

1、宿主机安装 ansible

```
yum install ansible # RHEL/CentOS/Fedora

apt-get install ansible # Debian/Ubuntu

emerge -avt ansible  # Gentoo/Funtoo

pip install ansible # will also install  paramiko PyYAML jinja2
```

2、宿主机无需其他配置，兼容 ~/.ssh/config 名称、证书配置

3、目标机无需额外配置

## walle
 1. 项目配置 中 开启Ansible
 2. (可选) config/params.php 配置 ansible_hosts 文件存放路径
 3. 按正常流程发布、上线代码，传输文件、远程执行命令均会通过ansible并发执行

## php5.6环境CentOS安装

- 删除老的安装包

    ```yum remove php.x86_64 php-cli.x86_64 php-common.x86_64 php-gd.x86_64 php-ldap.x86_64 php-mbstring.x86_64 php-mcrypt.x86_64 php-mysql.x86_64 php-pdo.x86_64```

- 更新源

    ```
    CentOs 6.x
    rpm -Uvh http://mirror.webtatic.com/yum/el6/latest.rpm
    CentOs 7.X
    rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm
    ```

- 安装php 5.6相关组件

    ```yum install php56w.x86_64 php56w-cli.x86_64 php56w-common.x86_64 php56w-gd.x86_64 php56w-ldap.x86_64 php56w-mbstring.x86_64 php56w-mcrypt.x86_64 php56w-mysql.x86_64 php56w-pdo.x86_64 php-theseer-fDOMDocument.noarch php-phpunit-exporter.noarch```

- 安装php-fpm 5.6

    ```yum install php56w-fpm```

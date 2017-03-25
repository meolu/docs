title: 安装
---

1.简洁安装指南
============
```
git clone https://github.com/meolu/walle-web.git
cd walle-web
vi config/web.php # 设置mysql连接
composer install  # 如果缺少bower-asset的话， 先安装：composer global require "fxp/composer-asset-plugin:*"
./yii walle/setup   # 初始化项目
配置nginx/apache的webroot指向walle-web/web
```

2.最最最详细安装指南
===============

以下安装，均在**宿主机**（一台配置了LAMP/LNMP的linux机器，并且安装git/svn）上操作，如有问题，详见[Q&A](/faq.md)。

如果还没有安装php 5.4+环境的，请先安装php5.4+，详情看[php 5.6安装](/瓦力/2.安装/依赖.html)。


1.代码检出
----------
```
mkdir -p /data/www/walle-web && cd /data/www/walle-web  # 新建目录
git clone https://github.com/meolu/walle-web.git .          # 代码检出
```



2.设置mysql连接
--------------
```
vi config/local.php +14
'db' => [
    'dsn'       => 'mysql:host=127.0.0.1;dbname=walle', # 新建数据库walle
    'username'  => 'username',                          # 连接的用户名
    'password'  => 'password',                          # 连接的密码
],
```

3.安装composer，如果已安装跳过
---------------------------
```
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer                # PATH目录
```

4.安装vendor
-----------
```
cd walle-web
composer install --prefer-dist --no-dev --optimize-autoloader -vvvv
```
安装速度慢或失败，可直接下载[vendor](http://pan.baidu.com/s/1c0wiuyc)解压到项目根目录

5.初始化项目
----------
```
cd walle-web
./yii walle/setup # 需要你的yes
```


6.配置nginx
-----------------
**凡是在第7步刷新页面看到50x均是前5步安装不完整，自行检查**

**凡是在第7步刷新页面看到404均是nginx配置不当，自行检查**

**nginx**简单配置
```
server {
    listen       80;
    server_name  walle.compony.com; # 改你的host
    root /the/dir/of/walle-web/web; # 根目录为web
    index index.php;

    # 建议放内网
    # allow 192.168.0.0/24;
    # deny all;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        try_files $uri = 404;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```


7.恭喜：）
--------
访问地址：localhost

当然，可能你配置nginx时的server_name是walle.company.com时，配置本地hosts之后，直接访问：walle.company.com亦可。




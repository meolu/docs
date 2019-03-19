title: 安装
---
## 0.安全
务必部署在内网！  
务必部署在内网！  
务必部署在内网！  

## 1.请给此项目标个星star
克隆项目前，请到[github - walle](https://github.com/meolu/walle-web)给此项目标个星star，不吝支持我们。

反过来正是你的微不足道的支持，让我们坚持免费开源做下去，反馈于你，感谢你的信任。

## 2.Clone code
```
git clone https://github.com/meolu/walle-web.git
```

## 3.Nginx config
别忘了要重启nginx，注意标出可能需要改的地方
```
upstream webservers {
    server 0.0.0.0:5000 weight=1; # 负载设置
}

server {
    listen       80;
    server_name  admin.walle-web.io; # 域名设置
    access_log   /usr/local/nginx/logs/walle.log main;
    index index.html index.htm; # 日志目录

    location / {
        try_files $uri $uri/ /index.html;
        add_header access-control-allow-origin *;
        root /walle-web/fe; # 前端代码已集成到walle-web，即walle-web/fe的绝对路径
    }

    location ^~ /api/ {
        add_header access-control-allow-origin *;
        proxy_pass      http://webservers;
        proxy_set_header X-Forwarded-Host $host:$server_port;
        proxy_set_header  X-Real-IP  $remote_addr;
        proxy_set_header    Origin        $host:$server_port;
        proxy_set_header    Referer       $host:$server_port;
    }

    location ^~ /socket.io/ {
        add_header access-control-allow-origin *;
        proxy_pass      http://webservers;
        proxy_set_header X-Forwarded-Host $host:$server_port;
        proxy_set_header  X-Real-IP  $remote_addr;
        proxy_set_header    Origin        $host:$server_port;
        proxy_set_header    Referer       $host:$server_port;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;

        # WebScoket Support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

## 4.Hosts
```
vi /etc/hosts

# 新增一行
127.0.0.1  admin.walle-web.io # 与nginx配置一致
```

## 5.Install
安装**Python 2.7** + `pip`。
```
bash admin.sh init

# 注意：安装mysqlclient失败，需要先安装libmysqlclient-dev(ubuntu)
# 注意：安装失败请指定python路径. mac 可能会有用anaconda的python，找到自己系统的python 2.7追加参数指定 -p /usr/bin/python2.7 即可
# vi admin.sh +20
# virtualenv --no-site-packages -p /usr/local/bin/python2.7 venv
```

## 6.Config setting
带 **@TODO** 就是可能要修改的地方
```
vi walle/config/settings_prod.py
```

## 7.Data Migration
7.0 ** MySQL 5.6.5以上**，否则会在安装时报错。在MySQL 5.6.5版本之前，Automatic Initialization and Updating只适用于TIMESTAMP，而且一张表中，最多允许一个TIMESTAMP字段采用该特性。从MySQL 5.6.5开始，Automatic Initialization and Updating同时适用于TIMESTAMP和DATETIME，且不限制数量。

7.1 创建一个数据库 walle
```
mysql  -hxx -uxx -p -e'CREATE SCHEMA walle'
```
7.2 Data Migration
```
bash admin.sh migration
```

## 8.Start
8.1 启动
```
bash admin.sh start
```
8.2 重启、升级、Migration
```
sh admin.sh restart # 重启
sh admin.sh upgrade # 升级walle，升级完需要重启walle服务。升级前最好 git stash 暂存本地修改，升级后git stash pop弹出暂存，然后重启服务。
sh admin.sh migration # Migration
```


## 9.开启walle 2.0之旅
恭喜你，大功告成！浏览器打开`http://admin.walle-web.io`（因nginx配置域名而定）。初始登录账号如下，开启你的walle 2.0之旅吧：）
```
超管：super@walle-web.io \ Walle123
所有者：owner@walle-web.io \ Walle123
负责人：master@walle-web.io \ Walle123
开发者：developer@walle-web.io \ Walle123
访客：reporter@walle-web.io \ Walle123
```

## 其它
walle运行过程，以及部署过程出错了，具体日志可以查看`logs/runtime.log`，别说你在启动walle的时候没注意到？

如果出现任何打开页面出现404或者只显示'wall-web 2.0'等，都是nginx配置或者服务没有启动，细心检查。

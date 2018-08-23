title: 部署出错
---

1.上线至全量更新服务器时出错：`mv -fT`
---------------------------------

原因分析：更新目标机群是以软链方式来更新webroot，如果提前在目标机群创建了webroot目录，软链覆盖将会失败。

解决办法：直接删除目标机群webroot目录，确定其父目录有读写的权限即可，由瓦力系统生成webroot软链接。

2.上线出错，想知道到底发生了什么事情
-------------------------------

有些错误walle捕捉不到，默认操作日志在`/tmp/walle/`下，具体可在`config/local.php`里`log.dir`配置路径，`tail`着日志，部署看日志。

3.`/tmp/walle`下无日志文件
------------------------
原因centos 7 yum 安装的php-fpm默认`/tmp`目录不可写：`/usr/lib/systemd/system/php-fpm.service` 中的 `PrivateTmp=true` 禁止了向tmp目录写日志

解决：

```
vi /usr/lib/systemd/system/php-fpm.service
PrivateTmp=false

systemctl daemon-reload
systemctl reload php-fpm
```

4.上线部署卡在`同步至服务器`,F12调试start_deploy为502
-------------------------------
调整超时配置大一点
* 注意检查php-fpm.conf的配置
```
request_terminate_timeout = 7200
```
* 注意检查php.ini的配置
```
max_execution_time = 0
```
* 注意检查nginx.conf的配置
```
fastcgi_connect_timeout 300;     // 时间改为  600
fastcgi_send_timeout 300;        // 时间改为  600
fastcgi_read_timeout 300;        // 时间改为  600
```


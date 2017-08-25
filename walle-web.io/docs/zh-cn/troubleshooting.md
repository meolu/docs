title: 问题解答
---


### 提问之前

- 懂得配置lnmp，不懂请百度google，莫在群里提问lnmp相关问题
- 具备 PHP 基础知识：php 5.4与5.3基本区别，composer 
- 会使用 git 操作：git pull, git stash
- 熟悉 PHP 常见的知识：composer 的使用
- 了解基本的 HTTP 协议，Header 头、请求方式（GET\POST\PUT\PATCH\DELETE）等
- 基本的 Debug 技能，查看 php_error 日志，nginx 4xx 5xx日志等
- 基于上一条，40x、50x错误，一律不回答，请按[最详细nginx php配置](installation.html)一一检查。

如果你不具备这些知识，请不要使用，因为这是开源项目，没有义务每天帮你解决一些常识性的问题，毕竟我们都要工作。


我们能保证你提的问题，90%可能都在常见错误列表里给列出来了，或者是太低级的问题。所以发问之前先看完所有错误列表（Ctr + F），如果没有，带上你的代码，去群：482939318里发个问题，重现流程，大家有空的会帮忙你解答。谢谢合作！:pray:


最后，请 **不要在QQ单独找我提问**，除非你是企业身份或者服务费。
### 检测、上线出错

检测、上线出错，想知道到底发生了什么事情？我能告诉你的就是，有些错误walle捕捉不到，默认操作日志在`/tmp/walle/`下，具体可在`config/local.php`里`log.dir`配置路径，`tail -f /tmp/walle/walle-YYYYmmdd.log`着日志，部署看日志。**一切检测、上线的错误均可被发现和解决。**

### 宿主机检测出错

- 宿主机代码检出检测出错，请确认php进程用户{user}有代码存储仓库{path}读写权限，且把ssh-key加入git的deploy-keys列表。详细错误：{error}

    - 问题：**请确认php进程用户{user}有代码存储仓库{path}读写权限**

        ```
        没有权限，是因为用户{user}对目录{path}没有读写权限，给权限即可
        ll {path}
        chown {user} -R {path}
        chmod 755 -R {path}
        ```
    - 问题：**把ssh-key加入git的deploy-keys列表**

        ```
        su {user} && cat ~/.ssh/id_rsa.pub
        打开 github/gitlab/bitbucket 网站, 添加 ssh-key 到ssh-keys列表
        ```
### 目标机检测出错

- 目标机器部署出错，请确认php进程{local_user}用户ssh-key加入目标机器的{remote_user}用户ssh-key信任列表，且{remote_user}有目标机器发布版本库{path}写入权限。详细错误：{error}
    - 问题：**请确认php进程{local_user}用户ssh-key加入目标机器的{remote_user}用户ssh-key信任列表**

        ```
        添加机器信任，还是没理解请百度吧（因为太多的同学问这问题，实在没办法只能这么啰嗦）
        su {local_user} && ssh-copy-id -i ~/.ssh/id_rsa.pub remote_user@remote_server
        # need remote_user's password
        # 为什么我把{local_user}的ssh-key加到远程机器的{remote_user}下的~/.ssh/authorized_keys还是不能免密码登录
        # 免密码登录需要远程机器权限满足以下三个条件：
           /home/{remote_user} 755
           ~/.ssh 700
           ~/.ssh/authorized_keys 644 或 600
        ```
    - 问题：**{remote_user}有目标机器发布版本库{path}写入权限**

        ```
        su remote_user
        ll {path}
        chown {remote_user} -R {path}
        chmod 755 -R {path}
        ```
### 上线至全量更新服务器时出错：`mv: 无法以非目录来覆盖目录 -fT /a/b/c /d/e/f`

原因分析：更新目标机群是以软链方式来更新webroot，如果提前在目标机群创建了webroot目录，软链覆盖将会失败。

解决办法：直接删除目标机群webroot目录: `rm -rf /d/e/f`，确定其父目录有读写的权限即可，由瓦力系统生成webroot软链接。

### 初始化walle时失败：could not find driver

缺少pdo扩展，解决办法：添加pdo扩展
```
ubuntu
apt-get install php5 php5-fpm php5-mysql

或者在源码包里编译
cd php-src/ext/pdo_mysql
phpize
./configure --with-php-config=/php/install/dir/bin/php-config
make && make install
vi php.ini # 添加pdo_mysql.so
restart php-fpm
```

### composer安装速度慢

好吧，我已经猜到会有人问有没有现成的，有！

下载[百度网盘](http://pan.baidu.com/s/1c0wiuyc)，解压vendor放到walle-web根目录即可。


### 第一次使用composer可能会出现的问题：1 没有添加git的token

>Could not fetch https://api.github.com/repos/jquery/jquery, please create a GitHub OAuth token to go over the API rate limit
Head to https://github.com/settings/tokens/new?scopes=repo&description=Composer+on+localhost+2015-10-08+1123
to retrieve a token. It will be stored in "/root/.composer/auth.json" for future use by Composer.
Token (hidden):

**解决办法：**

* 复制提示里的地址到浏览器，点击生成git token，如上面的：https://github.com/settings/tokens/new?scopes=repo&description=Composer+on+localhost+2015-10-08+1123
* 复制token到命令行，认证，继续

### 第一次使用composer可能会出现的问题：2 composer install 可能会出现的错误

>Loading composer repositories with package information
Installing dependencies (including require-dev)
Your requirements could not be resolved to an installable set of packages.
>
>  Problem 1
>    - yiisoft/yii2 2.0.x-dev requires bower-asset/jquery 2.1.*@stable | 1.11.*@stable -> no matching package found.
> ....

**解决办法**：`composer global require "fxp/composer-asset-plugin:*"`

### 如何添加用户key到git的ssh-keys列表
-------------------------------
```
su - www                 # 假如www为你的php进程用户
ssh-keygen -t rsa        # 如果你都没有生成过rsa_key的话
cat ~/.ssh/id_rsa.pub    # 复制
打开github/gitlab添加到你的ssh-keys或者deploy-keys里
```

### 如何添加用户ssh-key到目标机群部署用户ssh-key信任
**宿主机操作**
```
ps aux|grep php          # 假如www_php为你的php进程用户
su - www_php             # 切换用户
ssh-keygen -t rsa        # 如果你都没有生成过rsa_key的话，如果有则跳过
ssh-copy-id -i ~/.ssh/id_rsa.pub www_remote@remote_host  # 加入目标机群信任，需要输入www_remote密码
```


### nginx简单配置
```
server {
    listen       80;
    server_name  walle.company.com; # 改你的host
    root /the/dir/of/walle-web/web; # 根目录为web
    index index.php;

    # 建议放内网
    allow 192.168.0.0/24;
    deny all;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```


### 切换用户（www）时：this account is currently not available

```
cat /etc/passwd | grep www # 查看是否为 /sbin/nolgin
```

解决办法：
```
vim /etc/passwd
修改/sbin/nolgin为/bin/bash
```

### `The file or directory to be published does not exists: /data/www/walle-web/vendor/bower/jquery/dist`

新建此目录即可：`/data/www/walle-web/vendor/bower/jquery/dist`


### `/tmp/walle`下无日志文件
原因centos 7 yum 安装的php-fpm默认`/tmp`目录不可写：`/usr/lib/systemd/system/php-fpm.service` 中的 `PrivateTmp=true` 禁止了向tmp目录写日志

解决：

```
vi /usr/lib/systemd/system/php-fpm.service
PrivateTmp=false

systemctl daemon-reload
systemctl reload php-fpm
```

Call to undefined function yii\web\mb_parse_str()
------------------------------

缺少mbstring扩展，安装mbstring扩展重启php即可。mbstring扩展：http://php.net/manual/zh/mbstring.installation.php

项目中有配置文件解决方案
--------------------

- config-production 和 config-development 都提交到git仓库。这会存在一些小问题，比如数据库密码会让所有开发人员知道。你需要把数据库藏在服务器内网里，只能通过 ssh 跳板机登录，不给其他开发人员跳板机权限，知道数据库密码也没用。
    - Config::get 类读取配置时，根据 Apache/php-fpm 里面置入的 env 环境变量，判断是生产环境还是开发环境。
    - 在各环境部署项目的高级任务post-deploy里写相应的`mv config-production config`或者`mv config-development config`，代码直接读取`config`。
- 整理所有config有关多种环境的差异，其实并不多。大多数配置生产环境和开发环境都是一样的，只有数据库地址、缓存地址、API Secret 这些敏感信息有点不一样，这种不一样的单独放一个配置文件，放在webroot以外，软链一下就好。

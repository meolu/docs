title: 检测错误
---

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
        添加 ssh-key 到到项目的ssh-keys列表，根本不能解决该问题，在配置项目，做项目配置检测时，始终提示
        “宿主机代码检出检测出错，请确认把php进程用户www_php的ssh-key加入git的deploy-keys列表。”
        到底是什么原因？能不能有人分析下，网上一堆人乱七八糟的说了很多，估计那些人连是哪里出错导致了这问题都不知道。
        公钥生成是切换到{user}用户下操作的：su - {user} && ssh-keygen -t rsa -C '{user@host}' -b 4096，不加口令

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
        ````

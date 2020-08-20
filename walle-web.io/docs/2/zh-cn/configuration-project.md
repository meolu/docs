title: 项目配置
---

简单明了，零学习成本。

![permission](/docs/2/zh-cn/static/project_base.png)

# Git Repo
可以是https协议，如`https://github.com/meolu/walle-web.git`，可以是git协议，如`git@github.com:meolu/walle-web.git`

![permission](/docs/2/zh-cn/static/git-repo.png)

# 目标集群
从左侧勾选，点中间选择到右边。我也不知道为什么要写这种说明。

# 目标集群部署路径
一般为`webroot`，不能为已存在目录，walle会自行生成，但父目录需要已存在且有权限。如`webroot`为`/data/www/walle-java-tomcat`，则`/data/www/`需要存在且有权限

# 目标集群部署仓库
仓库存储每次发布的版本，用于储存、回滚等版本管理。

# 目标集群部署仓库版本保留数
保留版本数用以版本切换，以及回滚。别贪多，这么不自信；也别太小，太浪。


![permission](/docs/2/zh-cn/static/project_java_tomcat.png)

# 排除/指定文件
排除或指定打包同步至服务器的文件或目录。一行一个，支持正则，如：`*.log`

排除文件：
```
.git
*.log
```

指定文件：
```
walle-web.war
walle-web.jar
```

# 自定义全局变量

自定义全局变量可在高级任务中使用，一行一个，格式：VAR=value。

## 系统预置可用高级变量

| 高级变量             | 备注                               | 样例                           |
| -------------------- | ---------------------------------- | ------------------------------ |
| `${WEBROOT}`         | 目标集群部署路径                   | /data/www/walle-tiny-demo      |
| `${CURRENT_RELEASE}` | 目标集群部署仓库                   | /data/releases/walle-tiny-demo |
| `${VERSION}`         | 当前要发布目标集群部署仓库里的版本 | 29_78_20190225_190422          |
| `${BRANCH}`          | 当前部署分支                       | master                         |
| `${TAG}`             | 当前部署的tag                      | v1.0.12                        |
| `${COMMIT_ID}`       | 当前部署的commit_id                | abcde12                        |
| `${PROJECT_NAME}`    | 当前部署的项目名称                 | 大数据平台                     |
| `${PROJECT_ID}`      | 当前部署的项目id                   | 12                             |
| `${TASK_NAME}`       | 当前部署的任务名称                 | 增加图表样式                   |
| `${TASK_ID}`         | 当前部署的任务id                   | 11                             |
| `${DEPLOY_USER}`     | 当前部署的用户                     | 吴水永                         |
| `${DEPLOY_TIME}`     | 当前部署的时间                     | 2019-01-01 10:09:03            |

**注意：当项目配置中选择branch时，变量`${TAG}`不可用；当选择tag时，变量`${BRANCH}`和`${COMMIT_ID}`不可用**

# 高级任务
## 高级任务-Deploy前置任务
在宿主机未检出代码前的前置任务，常为安装依赖、配置环境变量等

## 高级任务-Deploy后置任务
在宿主机检出代码后的后置任务，常为编译、清除文件

```
# 打包编译
${MVN_HOME}/bin/mvn clean package -Dmaven.test.skip=true
cp target/walle-web.war .
```

## 高级任务-Release前置任务
在目标服务器同步代码到版本库后，服务切换的前置任务，常为停服、摘机器等

```
# 停服
${TOMCAT_HOME}/bin/shutdown.sh
```

## 高级任务-Release后置任务
在目标服务器新版本服务切换后的后置任务，常为启动服务、启动节点

```
# tomcat 更新服务
cp ${WEBROOT}/walle-web.war ${TOMCAT_HOME}/webapps/
# 服务启动
${TOMCAT_HOME}/bin/startup.sh
```

# 钉钉hook地址
在钉钉机器人管理页面选择“自定义”机器人，输入机器人名字并选择要发送消息的群。如果需要的话，可以为机器人设置一个头像，然后点击下一步

![permission](/docs/2/zh-cn/static/dingding-create-1.png)

头像可以使用下面图片，提在消息列表里高辨别度

![permission](/docs/2/zh-cn/static/default.jpg)

![permission](/docs/2/zh-cn/static/dingding-create-2.png)

点击“复制”按钮，即可获得这个机器人对应的 WebHooks 地址，其格式如下

`https://oapi.dingtalk.com/robot/send?access_token=xxxxxxxx`

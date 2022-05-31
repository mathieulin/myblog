---
title: "仿抖音项目开发遇到的问题"                         
author: "mathieu"  
description : "安卓开发安装依赖项+各种配置"    
date: 2022-05-31      
lastmod: 2022-05-31           

tags : [                                    
"配置",
]
categories : [                              
"java项目",
]
keywords : [                                
"仿抖音",
]

---

## 源码需要改动的地方（自己做的话）

tencentcloud.propeties的信息

SMSUtils.java

application-dev.yml

application-prod.yml里的各种



## 内网互通

### 1. 关闭本地防火墙

### 2. 连接到同一个WIFI

### 3. 查看本地IP

cmd命令行输入ipconfig

![](img1.jpg)

192.168.10.10



把application-dev.yml中的端口号改改，再把数据库改改

## 前端代码运行

将App.vue中的serverUrl改成本机IP

![](img2.jpg)



连接模拟器

[HBuilderX连接安卓模拟器 - 简书 (jianshu.com)](https://www.jianshu.com/p/f01efc0e4609)

## Ubuntu基础配置

VMWare安装，Ubuntu下载网上教程很全面了

安装后：

#### 1.中文

右上角settings->Region&Language-> managed install language -> install language -> chineses simplify->

Region formats 改为汉语 ->apply->重启->settings->'+'->中文->language->汉语->restart



#### 2.镜像源

[Ubuntu 20.04换国内源 清华源 阿里源 中科大源 163源 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/421178143)



#### 3.复制粘贴

[(99条消息) VMware Tools安装步骤（windows10）_萝北村的枫子的博客-CSDN博客_vmtools安装](https://blog.csdn.net/thy0000/article/details/122638884?ops_request_misc=%7B%22request%5Fid%22%3A%22165374071316782248582850%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165374071316782248582850&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-122638884-null-null.142^v11^pc_search_result_control_group,157^v12^control&utm_term=安装vmware+tools&spm=1018.2226.3001.4187)

[(99条消息) VMware虚拟机的安装VMware Tools是灰色无法点击的解决方法_BluePROT的博客-CSDN博客_vm虚拟机安装tools是灰色](https://blog.csdn.net/qq_41037945/article/details/104173550?ops_request_misc=&request_id=&biz_id=102&utm_term=重新安装vmware tools无法点击&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-104173550.142^v11^pc_search_result_control_group,157^v12^control&spm=1018.2226.3001.4187)





## 数据库使用



数据库数据的导出

在MySql的bin目录下执行

```plain
D:
cd D:\mysql-8.0.28-winx64\bin

mysqldump -uroot -p password imooc>C:\Users\15128\Desktop\大二下作业\java\java-homework\my-version\imooc-red-book-dev-chapter9-finished\imooc.sql
```



#### 我建了个云数据库

rm-bp12ec5b1y4n87tjtoo.mysql.rds.aliyuncs.com:3306

user:root

password:马赛克

database:imooc

## MariaDB安装（Ubuntu）其实用MySQL就好了

[(100条消息) 如何在Ubuntu上安装MariaDB_cunchi4221的博客-CSDN博客](https://blog.csdn.net/cunchi4221/article/details/107471755?ops_request_misc=&request_id=&biz_id=102&utm_term=ubuntu mariadb&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-107471755.142^v11^pc_search_result_control_group,157^v12^control&spm=1018.2226.3001.4187)

#### 安装yum

##### yum的总体安装

https://blog.csdn.net/qq_38690917/article/details/115261819

##### 不出意外应该安装失败

https://blog.csdn.net/pingyan158/article/details/121992571





##### 虚拟机访问数据库的问题

https://blog.csdn.net/Gaozier/article/details/115515440



#### 数据库工具的引入

更改generatorConfig.xml中的配置

application-yml中的spring配置项下的数据源



## 验证码验证

直接调用接口

[192.168.10.10:8099/sms](http://192.168.10.10:8099/sms)



### 腾讯云

```
tencent.cloud.secretId=AKIDQjoeFcWBdw3wZx73lGbrtgEXcS5TcXDe
tencent.cloud.secretKey=d2C8qMgojQwEII4ez6yb0B8sQkfV8Yls
```



## Linux下安装Redis

#### Redis安装运行

按上面的tools操作后可以直接拖动进来，就不需要连接SecureCRT

https://blog.csdn.net/qq_39135287/article/details/83474865

```cmd
sudo -s
cd /usr/local/redis-7.0.0/bin
./redis-server /usr/local/redis-7.0.0/etc/redis.conf
```

一定要关闭本地防火墙

然后虚拟机改成桥接模式（括号里也打勾）

```plain
ping 192.168.10.10
```

ping一下主机ip看看能不能连上

虚拟机ip 192.168.10.14不知道会不会变，redis端口号为6379

#### RDM下载

见云盘



## minio下载

[(99条消息) Windows中Minio的安装和使用_Viviana-0的博客-CSDN博客_windows安装minio](https://blog.csdn.net/weixin_45579930/article/details/107172781?ops_request_misc=%7B%22request%5Fid%22%3A%22165379292716782425142258%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165379292716782425142258&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-107172781-null-null.142^v11^pc_search_result_control_group,157^v12^control&utm_term=windows安装minio&spm=1018.2226.3001.4187)

下载到了D盘

```plain
.\minio.exe server E:\minio
```

控制台 http://192.168.10.10:9000

账号密码为minioadmin



## MongoDB

[最详细的Windows平台安装MongoDB教程 - TM0831 - 博客园 (cnblogs.com)](https://www.cnblogs.com/TM0831/p/10606624.html)

命令行执行

```plain
mongod --dbpath E:\MongoDB\data\db
```

127.0.0.1:27017



```plain
mongod --dbpath "E:\MongoDB\data\db" --logpath "E:\MongoDB\data\log\mongo.log" -install -serviceName "MongoDB"
```



## unicloud

好像用了这个就不需要民minio了呢？这样就可以少启用少部署一个服务了挺好的



## 每次调试需要启动的服务

+ 本地MySQL（开机自启） // 现在已使用云数据库，不需要了已经

  + ```plain
    rm-bp12ec5b1y4n87tjtoo.mysql.rds.aliyuncs.com:3306
    root
    马赛克
    ```

+ 模拟器

+ HBuilder项目

+ 虚拟机开启Redis服务 //云的太贵了，算咯

  ```cmd
  sudo -s
  cd /usr/local/redis-7.0.0/bin
  ./redis-server /usr/local/redis-7.0.0/etc/redis.conf
  ```

+ MinIO

  在java项目目录下（其实有了unicloud就不需要minio咯）

```plain
.\minio.exe server E:\minio
```

+ MongoDB

  这里我设置了开机自启

## 阿里云可以试用一个月服务器

实例密码

马赛克

远程连接密码

马赛克

安全组配置-》配置端口号

马赛克



#### MariaDB连接

netstat -apn |grep 3306查看MariaDB运行状态

[(101条消息) 为 MariaDB 配置远程访问权限_Y.zh的博客-CSDN博客_mariadb远程访问权限](https://blog.csdn.net/pythonyzh2019/article/details/108951629)

121.199.35.110:3306

root

马赛克



### MongoDB

121.199.35.110:27017

root

马赛克



#### Redis

cd /usr/local/redis/bin

./redis-server redis.conf



121.199.35.110:6379

马赛克



#### MinIO

121.199.35.110:9111

root

马赛克



#### 项目部署

cd /home

nohup java -jar imooc.jar >my.log 2>&1 &



#### 查看进程

ps -ef|grep imooc

lsof -i:8099


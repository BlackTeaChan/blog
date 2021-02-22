---
title: 简单记录FreeSwitchServer搭建过程
en_title: jian_dan_ji_lu_free_switch_server_da_jian_guo_cheng
date: 2021-01-28 14:30:58
categories:
tags:
keywords: FreeSwitch,融合通讯,Linux,CentOS7
---

近期应项目需要，开始研究融合通讯相关技术，使用到了开源的FreeSwitch，简单记录一下搭建以及使用的过程。
<!-- more -->

# 搭建FreeSwitch
## 官网
**[FreeSwitch](https://freeswitch.org/ "FreeSwitch官网")**  
官网是纯英文的，英语比较差，只能使用翻译插件来克服一下......
## 使用环境
FreeSwitch支持Linux、Windows、macOS等系统。  
Windows 10因为FSGUI无法安装的原因，本次使用了CentOS 7 x64来做为FreeSwitch服务器
## 安装
### CentOS 7
参考官网提供的**[搭建教程](https://freeswitch.org/confluence/display/FREESWITCH/CentOS+7+and+RHEL+7#CentOS7andRHEL7-CentOS7andRHEL7-Stable "点击前往")**
使用以下命令安装，第二步可能会比较久
```
yum install -y https://files.freeswitch.org/repo/yum/centos-release/freeswitch-release-repo-0-1.noarch.rpm epel-release

yum install -y freeswitch-config-vanilla freeswitch-lang-* freeswitch-sounds-*

systemctl enable freeswitch
```
经过一个晚上的等待，终于安装好了
![Terminal截图](安装FreeSwitch.png)
### Windows
Windows系统可以直接使用安装包进行安装，本次使用的是最新版1.10.5
- 官方下载地址：https://files.freeswitch.org/windows/installer/x64/
- 百度网盘地址：https://pan.baidu.com/s/10f5XFA4tU46IfnRxNsf92g  密码: 9lvl
![Windows下载页面](Windows下载页面.png)
下载好安装包，只需按照默认选项安装即可，安装类型建议选择**[Complete]**
![Windows下载页面](Windows安装界面.png)

## 启动
1. 使用`freeswitch -nc`命令后台启动FreeSwitch
启动后会输出对应的进程ID
![启动FreeSwitch](启动FreeSwitch.png)
2. 使用`fs_cli -rRS`来访问FreeSwitch

## 使用
### 常用命令
```
-nf                     -- no forking
-u [user]               -- 启动后以⾮ root ⽤户 user 身份运⾏
-g [group]              -- 启动后以⾮ root 组 group 身份运⾏
-help                   -- 显示本帮助信息
-version                -- 显示版本信息
-waste                  -- 允许浪费内存，FreeSWITCH 仅需 240K 的栈空间你可以使⽤ ulimit -s 240 限制栈空间使⽤，或使⽤该选择忽略警告信息
-core                   -- 出错时进⾏内核转储
-hp                     -- 以⾼优先级运⾏
-vg                     -- 在 valgrind 下运⾏，调试内存泄露时使⽤
-nosql                  -- 不使⽤ SQL，show channels 类的命令将不能显示结果
-heavy-timer            -- 更精确的时钟。可能会更精确，但对系统要求更⾼
-nonat                  -- 如果路由器⽀持 uPnP 或 NAT-PMP，则 FreeSWITCH可以⾃动解决 NAT 穿越问题。如果路由器不⽀持，则该选项可以使启动更快
-nocal                  -- 关闭时钟核准。FreeSWTICH 理想的运⾏环境是 1000 Hz 的内核时钟如果你的内核时钟⼩于 1000 Hz 或在虚拟机上，可以尝试关闭该选项
-nort                   -- 关闭实时时钟
-stop                   -- 关闭 FreeSWTICH，它会在 run ⽬录中查找 PID⽂件
-nc                     -- 启动到后台模式，没有控制台
-c                      -- 启动到控制台，默认
-conf [confdir]         -- 指定其它的配置⽂件所在⽬录，须与 -log、 -db 合⽤
-log [logdir]           -- 指定其它的⽇志⽬录
-run [rundir]           -- 指定其它存放 PID ⽂件的运⾏⽬录
-db [dbdir]             -- 指定其它数据库⽬录
-mod [moddir]           -- 指定其它模块⽬录
-htdocs [htdocsdir]     -- 指定其它 HTTP 根⽬录
-scripts [scriptsdir]   -- 指定其它脚本⽬录
```
### 添加用户
1. 前往目录`/usr/local/freeswitch/conf/directory/default`  
   创建`用户ID.xml`文件（可以直接复制1000.xml，替换1000为用户ID）
2. 找到以下文件
   ```
   /usr/local/freeswitch/conf/dialplan/default.xml
   /usr/local/freeswitch/conf/dialplan/public.xml
   ```
   修改文件内的正则表达式内
   ```
   <!-- 修改前 -->
   expression="^(10[01][0-9])$"
   <!-- 修改后（添加用户1234） -->
   expression="^(10[01][0-9]|1234)$"
   ```

# 安装FSGUI
## 说明
FSGUI为FreeSwitch提供了可视化的界面，使用Docker来快速部署FSGUI  
[参考教程](https://www.bilibili.com/video/BV1QE411P7DW)
## 安装并配置Docker（可选）
### 安装Docker
```
yum update -y

yum -y install docker-io

docker -v
```
![安装Docker](安装Docker.png)
### 启动Docker
```
systemctl start docker
```
### 设置开机自启
```
systemctl enable docker
```
## 安装FSGUI
### 拉取镜像
这一步速度取决于你的网速
```
sudo docker pull registry.cn-beijing.aliyuncs.com/qzlink/fsgui:2.10
```
![拉取FSGUI镜像](拉取FSGUI镜像.png)
### 启动测试
我这里把镜像的标签改了一下
```
docker run -d --net=host --name fsgui 【tag】:latest 
```



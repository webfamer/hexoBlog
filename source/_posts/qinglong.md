---
title: '部署青龙面板+自助扫码'
date: 2021-08-23 10:30:46
tags:
---

> 已CentOS为例

![](https://img11.360buyimg.com/ddimg/jfs/t1/131309/25/16329/16113/60b1f56bE4ee68cb9/edcfd8af572e05ff.png)

安装Docker
（已安装的可跳过）
![](https://img10.360buyimg.com/ddimg/jfs/t1/184054/36/6408/21177/60b1f666E0286e3f0/c332be19e7ccbbb1.png)
使用宝塔面板的可直接在软件商店安装宝塔安装Docker
1、更新Yum软件包

```shell
yum update
```
2、安装Docker
```shell
curl -sSL https://get.daocloud.io/docker | sh
```
3、添加Docker仓库

```shell
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo  
```
4、启动Docker
```shell
sudo systemctl start docker
```
### 安装青龙面板
[开源地址](https://github.com/whyour/qinglong)
1、拉取青龙面板
```shell
docker pull whyour/qinglong:latest
```
2、创建容器
```shell
docker run -dit \
-v $pwd/ql/config:/ql/config \
-v $pwd/ql/log:/ql/log \
-v $pwd/ql/db:/ql/db \
-v $pwd/ql/scripts:/ql/scripts \
-v $pwd/ql/jbot:/ql/jbot \
-p 5700:5700 \
-e ENABLE_HANGUP=true \
-e ENABLE_WEB_PANEL=true \
--name qinglong \
--hostname qinglong \
--restart always \
whyour/qinglong:latest
```
3、 开放青龙面板端口（没开防火墙可忽略）

- 宝塔面板可在" 安全 "选项中开放，不开放端口也可通过Nginx反向代理到域名
![](https://img14.360buyimg.com/ddimg/jfs/t1/171752/7/12189/23076/60b1fa8cE6e1062de/8da3ca70cb66246e.png)
```shell
firewall-cmd --zone=public --add-port=5700/tcp --permanent
```
4、查看面板密码

浏览器打开http://ip:5700或反向代理的域名

默认帐号密码均为admin，输入登录后提示查看密码

通过cat /ql/config/auth.json 查看随机密码

5、添加京东脚本仓库定时更新任务

请参照图片自己添加，添加成功后执行一下，会自动新增京东的脚本到青龙面板中
```shell
//更新longzhuzhu仓库
ql repo https://ghproxy.com/https://github.com/nianyuguai/longzhuzhu.git "qx"
//更新i-chenzi仓库
ql repo https://ghproxy.com/https://github.com/monk-coder/dust.git "i-chenzhe|normal|member|car" "backup"
//更新lxk仓库
ql repo https://ghproxy.com/https://github.com/chinnkarahoi/jd_scripts.git "jd_|jx_|getJDCookie" "activity|backUp" "^jd[^_]|USER"
//更新whyour仓库
ql repo https://ghproxy.com/https://github.com/whyour/hundun.git "quanx" "tokens|caiyun|didi|donate|fold|Env"
```
```
//更新时间（Cron表达式）
27 8,12,16,20,0 * * *
```
定时任务
![](https://img12.360buyimg.com/ddimg/jfs/t1/126176/4/18889/44763/60b1f74eEde7305b3/2688beb3c87172f5.png)
6、微信推送
进入青龙面板-配置文件 加入末尾下面的参数
```shell
## Push Plus
export PUSH_PLUS_TOKEN=""
export PUSH_PLUS_USER=""
```
其中 PUSH_PLUS_TOKEN 是 http://pushplus.plus/ 注册登录后提供的Token，必填
PUSH_PLUS_USER 选填，一对一则不填，一对多必填，填入pushplus群组编号
![](https://img14.360buyimg.com/ddimg/jfs/t1/192365/30/5426/94373/60b1fb79E78ab18bc/4f3d32ddad9232db.png)
pushplus
安装青龙扫码
示例：[京豆自助扫码](https://jd.mrjiang.top/#/)
傻瓜式安装

> 有的小伙伴可能不太懂下面步骤，这里特意加了一个傻瓜式安装，下载压缩包解压到 /ql/目录里面，启动JDC即可
文件下载：[点击下载](https://pan.mrjiang.top/s/K5esn)（压缩包里的是AMD64架构的，适用于服务器的CPU，但是有的小伙伴是用斐讯N1、路由器之内的ARM架构，需要下载arm版的JDC替换压缩包里面的文件哦，需要自己重命名一下：[点击下载ARM版本JDC](https://pan.mrjiang.top/s/o1Gtm)）  

一、解压到/ql/目录下

二、将JDC分配可执行权限
```shell
chmod 777 JDC
```
三、运行JDC
```shell
nohup ./JDC
```
四、若要修改页面直接修改index.html即可，不需要重启

[源码备份下载](https://pan.mrjiang.top/s/48Jf2)
已编译下载：[AMD64架构](https://pan.mrjiang.top/s/8joCQ) (适用服务器，PC等) ， [ARM架构](https://pan.mrjiang.top/s/o1Gtm) (适用N1，路由器，树莓派等)

1、安装Wget、Unzip
```shell
//ubuntu
apt install wget unzip
//centos
yum install wget unzip -y
```
2、下载并解压扫码程序
```shell
//切换成青龙的路径
cd /ql
```
```shell
//作者已删库，下面的链接可能访问不了，建议采用傻瓜式安装或者下载源码编译安装
//如果你是amd64架构（服务器，PC等）
wget https://github.com/huayu8/JDC/releases/download/1.0.2/linux_amd64.zip && unzip linux_amd64.zip
//如果你是arm架构（N1，路由器，树莓派等）
wget https://github.com/huayu8/JDC/releases/download/1.0.2/linux_arm.zip && unzip linux_arm.zip 
```
3、分配文件权限并初始化
```shell
chmod 777 JDC
./JDC
```
4、修改配置文件,主要是注意青龙的文件路径
```shell
vi config.toml
```
```shell
#公告设置
[app]
    explain         = "扫码后请返回页面完成登录" #页面使用说明显示
    path            = "/ql/config/auth.json" #QL文件路径设置，一般无需更改
    QLip            = "http://127.0.0.1" #青龙面板的ip，部署于一台服务器时不用更改
    QLport          = "5700" #青龙面板的端口，默认为5700
    logName         = "chinnkarahoi_jd_scripts_jd_bean_change" #日志脚本名称
    allowAdd        = "0" #是否允许添加账号（0允许1不允许）不允许添加时则只允许已有账号登录

#web服务设置
[server]
    address        = ":5701" #端口号设置
    serverRoot     = "public" #静态目录设置，请勿更改
    serverAgent    = "JDCookie" #服务端UA

#模板设置
[viewer]
    Delimiters  =  ["${", "}"] #模板标签，请勿更改
```
5、开放5701扫码端口

方法参考安装青龙面板

6、运行青龙扫码
```shell
nohup ./JDC
```
7、关于修改HTML无效的原因

> 因为作者把静态资源打包后，以通过模块的形式进行导入，需要删除掉该导入，将静态资源放在同JDC同目录或者public文件夹内进行读取
- 编辑main.go并删除_ "getJDCookie/packed"
![](http://mrjiang-images.oss-cn-chengdu.aliyuncs.com/2021/05/30/153c46ff501e5.PNG)

- 将修改好的静态文件放入JDC所在文件的public目录里或根目录
![](http://mrjiang-images.oss-cn-chengdu.aliyuncs.com/2021/05/30/4d37f6787b653.PNG)

- GO编译
打开CMD，使用cd命令切换到源文件根目录
```shell
//编译前修改运行环境
//GOARCH指的是目标处理器的架构，支持一下处理器架构 arm arm64 386 amd64 ppc64 ppc64le mips64 mips64le s390x
set GOARCH=amd64
//GOOS指的是目标操作系统，支持以下操作系统 darwin freebsd linux windows android dragonfly netbsd openbsd plan9 solaris
set GOOS=linux
//Go语言 编译命令
go build -o JDC
```
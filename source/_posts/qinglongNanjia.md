---
title: '青龙+nanjia扫码教程'
date: 2021-08-23 10:30:46
tags:
---


注意

新手建议保存好ck，重置系统从0安装。教程从0开始。

第一步 安装docker，docker-compose。

## 安装docker
``` shell
sudo yum check-update
curl -sSL https://get.daocloud.io/docker | sh
sudo systemctl start docker
sudo systemctl status docker
sudo systemctl enable docker
```
安装完docker以后 安装docker-compose  
```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
# 不想重置系统就删除容器

docker stop $(docker ps -a -q) // stop停止所有容器 
docker rm $(docker ps -a -q) // remove删除所有容器
```
第二步

在root文件夹下创建一个目录，目录内创建docker-compose.yml
填入一下内容
```shell
version: "3"
services:
qinglong:
image: whyour/qinglong:latest
container_name: qinglong
restart: unless-stopped
tty: true
ports:
- 5700:5700
- 5701:5701
environment:
- ENABLE_HANGUP=true
- ENABLE_WEB_PANEL=true
volumes:
- ./config:/ql/config
- ./log:/ql/log
- ./db:/ql/db
- ./repo:/ql/repo
- ./raw:/ql/raw
- ./scripts:/ql/scripts
- ./jbot:/ql/jbot
- ./ninja:/ql/ninja
labels:
- com.centurylinklabs.watchtower.enable=false
```
run模式
```shell
docker run -dit \
-v $PWD/ql/config:/ql/config \
-v $PWD/ql/log:/ql/log \
-v $PWD/ql/db:/ql/db \
-v $PWD/ql/repo:/ql/repo \
-v $PWD/ql/raw:/ql/raw \
-v $PWD/ql/scripts:/ql/scripts \
-v $PWD/ql/jbot:/ql/jbot \
-v $PWD/ql/ninja:/ql/ninja \
-p 5700:5700 \
-p 5701:5701 \
--name qinglong \
--hostname qinglong \
--restart unless-stopped \
whyour/qinglong:latest
```
命令行里cd进docker-compose.yml所在的文件夹，执行命令
```shell
docker-compose up -d
```
然后登录ip:5700 重置一下密码。

进入容器
## ql为容器名 本文机器容器名为qinglong
```shell
docker exec -it qinglong bash
```
为了打击代挂，大佬限制扫码数量为20。感觉有点少。。所以我修改成了一个很舒服的数字
执行以下命令
```shell
git clone https://github.com/MoonBegonia/ninja.git /ql/ninja
cd /ql/ninja/backend
pnpm install
pm2 start
cp sendNotify.js /ql/scripts/sendNotify.js
```
第三步

打开青龙configs文件夹的extra.sh文件
将以下内容粘贴进去。
```shell
cd /ql/ninja/backend
git pull -f
pnpm install
pm2 start
cp sendNotify.js /ql/scripts/sendNotify.js
```
Ninja 环境变量

目前支持的环境变量有：
```shell
ALLOW_ADD: 是否允许添加账号 不允许添加时则只允许已有账号登录（默认 true）
ALLOW_NUM: 允许添加账号的最大数量（默认 40）
```
##配置方式：
```shell
cd /ql/ninja/backend
cp .env.example .env
vi .env
pm2 start
```
修改完成后需要 
```shell
pm2 start
``` 
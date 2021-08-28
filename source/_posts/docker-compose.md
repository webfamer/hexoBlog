---
title: CenterOS docker源的方式的安装以及docker-compose加速安装
date: 2021-08-27 15:10:37
tags:
---
问题解决
在以下安装命令执行过程中，出现Another app is currently holding the yum lock; waiting for it to exit… 的问题。均可执行以下命令去解决。

```shell
rm -f /var/run/yum.pid
```
安装
1、安装依赖
```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
2、添加源
注意：用下面的源，可能速度超级慢，所以建议用阿里云的源

不推荐：
```shell
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
推荐：
用阿里云的源
```shell
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
3、安装docker
```shell
sudo yum install docker-ce docker-ce-cli containerd.io
```
4、启动docker
```shell
sudo systemctl start docker
```
1
5、设置开机启动
```shell
sudo systemctl enable docker
```
6、配置Docker中国区官方镜像
编辑 /etc/docker/daemon.json 文件，添加如下信息
```shell
vi /etc/docker/daemon.json
```
```shell
{
	"registry-mirrors": ["https://registry.docker-cn.com"]
}
```
重启docker
```shell
systemctl daemon-reload
systemctl restart docker
```
docker-compose安装
执行如下俩条命令即可
```shell
curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
```
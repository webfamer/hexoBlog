---
title: '青龙2.8互助教程 8.8更新'
date: 2021-08-23 10:30:46
categories:
- Docker
tags:
- Docker
---
## 方式一：通过官方给的方式进行互助
（适合自给自足的朋友）
准备好2个文件：task_before.sh、code.sh
（请下载对应青龙版本的文件，因青龙v2.8.0.071版本后升级了助力逻辑）

> 青龙v2.8.0-071版本关于互助的逻辑变更：读取code.sh的日志
task_before中执行互助码出错的建议将合并互助码操作移到code.sh中，task_before只做引入互助码操作，
具体可参考最新示例：(code) https://t.me/jiaolongwang/126 (task_before) https://t.me/jiaolongwang/120
示例即文末随附的下载文件。

> 文末有下载，或直戳标题下方“前往下载”（都是官方给的）

1. 将task_before.sh放入青龙映射的config文件夹📁，无需任何修改。
2. 将code.sh放入青龙映射的scripts文件夹📁
3. 根据拉取的库，修改code.sh里面的name_js前缀，默认**chinnkarahoi**。
> 如果你拉取的是JDHelloWorld，那就把前缀chinnkarahoi全部替换成JDHelloWorld，然后保存。举一反三
```
name_js=(
  chinnkarahoi_jd_scripts_jd_fruit
  chinnkarahoi_jd_scripts_jd_pet
  chinnkarahoi_jd_scripts_jd_plantBean
  chinnkarahoi_jd_scripts_jd_dreamFactory
  chinnkarahoi_jd_scripts_jd_jdfactory
  chinnkarahoi_jd_scripts_jd_jdzz
  chinnkarahoi_jd_scripts_jd_crazy_joy
  chinnkarahoi_jd_scripts_jd_jxnc
  chinnkarahoi_jd_scripts_jd_bookshop
  chinnkarahoi_jd_scripts_jd_cash
  chinnkarahoi_jd_scripts_jd_sgmh
  chinnkarahoi_jd_scripts_jd_cfd
  chinnkarahoi_jd_scripts_jd_health
)
```
4. 添加定时任务”task code.sh”，定时时间按自己的需求来。
> 青龙v2.8.0-071及以后版本会读取code.sh日志自动互助。<br>如果没有自动互助，读取了脚本内置的互助码，建议换清除了内置助力干净的脚本。

> 成功的前提条件：你必须运行过相应的脚本，形成了日志，它才可以读取和合并你的互助码。今日新建了ql容器，把QL里面的日志copy了过去进行测试，没有问题。

## 方式二：通过大佬🧍‍♂️的助力池上车🚗
（适合势单力薄的伙计）
目前我知道的有两个：[1](https://t.me/jdShareCode)、[2](https://t.me/passerbyb2021)。

我没有用过，也不知道能不能分享，就用数字1、2代表了，需要的自己进群，根据大佬的要求上车。

后话：我发现还有大佬🧍‍♂️自己改了code.sh文件，可以自由切换repo的库进行互助。但是我还是喜欢原汁原味的，所以先介绍这2种吧，后面有空了再写写高阶的。小白白先吃点清淡的。
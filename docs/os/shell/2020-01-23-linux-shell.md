---
title: Linux Shell相关的很多东西
description: Linux Shell命令相关配置
categories:
 - LINUX
tags:
 - Shell
---

## 一些方便的实例

> 查找某个符号在哪个so中

```bash
for i in *.so; do nm $i | grep symbol &&  echo $i ; done
```

> 输出为表格
```
mount | column -t
```

> 按内存/cpu使用量对进程排序
```
ps aux | sort -rnk 4
ps aux | sort -nk 3
```

> 将命令与终端分离
```
nohup
```

> 上一条命令
```
!!
```

## 命令详解

> 查看Linux信息

```
uname [-amnrsv][--help][--version]
[内核名]Linux
[主机名]yoko-pc
[内核版本]5.4.6-2-MANJARO 
[内核编译时间]#1 SMP PREEMPT Tue Dec 24 15:55:20 UTC 2019  
[机器类型]x86_64
[操作系统]GNU/Linux
```

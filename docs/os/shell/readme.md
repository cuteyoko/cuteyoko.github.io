# Linux Shell概述

## shell


### shell prompt

```
[xxx@xxx missing-sem]$

自定义
```

### Shell为何可以执行程序

- 环境变量（搜寻PATH寻找应用程序）
- 是一种脚本语言，支持循环条件等
- `which echo` 找到所在路径

### 相对路径/绝对路径

```
pwd  当前目录路径
cd .. 上一层
```

### 例子
```
echo hello
echo "hello world"
echo 'hello world'
echo hello\ world
```

## shell脚本

### shebang/Hashbang

详见[wiki](https://zh.wikipedia.org/wiki/Shebang)

脚本标准起手，指定解释器
```bash
#!/bin/sh 
```

> Shebang这一语法特性由#!开头，即井号和叹号。 在开头字符之后，可以有一个或数个空白字符，后接解释器的绝对路径，用于调用解释器。 在直接调用脚本时，调用者会利用Shebang提供的信息调用相应的解释器，从而使得脚本文件的调用方式与普通的可执行文件类似。

包括如下用法：

```bash
#!/bin/cat
Hello world!
```

```
#!/usr/bin/env 脚本解释器名称
```




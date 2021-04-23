---
title: CentOS使用记录
description: 这波啊，这波是centos
categories:
 - 记录
tags:
 - CentOS
---

## 安装

安装过程比较简单，采用hyperV + 镜像的方式。分区按推荐，毕竟虚拟方式，不值得多说。

## 配置流程

- 启动网络

首先还是激活一把网络,配置，然后`service network start`

```config
BOOTPROTO="static"
DEFROUTE=yes
IPADDR=192.168.xxx.23
GATEWAY=192.168.xxx.1
NETMASK=255.255.255.0
```

然而没得dns

`nmcli con mod eth0 ipv4.dns 10.xxx.xxx.xxx`

`nmcli con up eth0`

感觉直接在`/etc/sysconfig/network-scrpts/ifconfig-eth0`直接增加`DNSx=xx.xxx.xx.xxx`一样呀

- 修改源

```bash
yum clean all
yum make cache
```

## 软件安装

- git
没啥可说的

- vim
考虑安装更新的版本
考虑编译安装，碰到问题，没得gcc
新错误

```basherr
no terminal library found
checking for tgetent()... configure: error: NOT FOUND!
      You need to install a terminal library; for example ncurses.
      Or specify the name of the library with --with-tlib.
```

解决方案
缺库找对应dev

```bash
yum install ncurses-devel.x86_64 lua lua-devel python python-devel python3 python3-devel perl
```

configure

```bash
./configure --prefix=/usr/local/vim81 \
--enable-fail-if-missing --with-features=huge \
--enable-multibyte --enable-pythoninterp --enable-python3interp \
--enable-perlinterp --enable-luainterp --enable-gui=gtk2 --enable-cscope
make
make install
```

fatal error: EXTERN.h: No such file or directory
yum install perl-ExtUtils-Embed
bashrc增加路径

- gcc

```basherr
Error: Package: glibc-2.17-292.el7.i686 (base)
           Requires: glibc-common = 2.17-292.el7
           Installed: glibc-common-2.17-307.el7.1.x86_64 (@anaconda)
               glibc-common = 2.17-307.el7.1
           Available: glibc-common-2.17-292.el7.x86_64 (base)
               glibc-common = 2.17-292.el7
```

解决方案： 降级`yum -y downgrade glibc glibc-common`

- rpm安装docker
因为公司源没有docker-ce
下载对应rpm包，
安装

```bash
   55  rpm -ivh containerd.io-1.2.6-3.3.el7.x86_64.rpm
   56  yum install container-selinux
   57  yum downgrade policycoreutils
   58  yum install container-selinux
   59  rpm -ivh containerd.io-1.2.6-3.3.el7.x86_64.rpm
   61  rpm -ivh docker-ce-cli-19.03.9-3.el7.x86_64.rpm
   62  rpm -ivh docker-ce-19.03.9-3.el7.x86_64.rpm
```

- docker修改存储位置

```bash
daemon.json
"data-root" = "/home/.docker"
systemctl restart docker
```

- vim插件

```bash
位置: ~/.vim/pack/plugin/start [opt]
附送我的vimrc.


" allow syntax
syntax on

" allow plugins
filetype plugin indent on
set backspace=2
set laststatus=2
set ruler
let g:better_whitespace_enabled=1


colorscheme molokai
set t_Co=256
set background=dark

" file association set
autocmd FileType c,cpp,cc,h set shiftwidth=4 | set expandtab | set tabstop=4


map <C-p> :Leaderf file<CR>
map <C-f> :Leaderf rg
map <C-n> :NERDTree<CR>

```

**LeaderF** : 

- samba安装

```bash
yum install samba samba-client
```

- git

```bash
## 问题解决&常用
- peers certificatie...
那么就`git config [--global] http.sslverify false`

- 保存秘钥
不想sshkey，使用https链接,总要输入密码怎么办 
`git config --global credential.helper store`

- 推送默认
啊哈我才不要一不小心把所有本地匹配都推送了，推了没防护的master就很矬
还是simple比较好`git config --global push.default simple`
```

- cmake（本意是为了安装YCM）

```bash
./bootstrape
gmake 
gmake install
```

- YCM

```bash
xxx
```

- RipGrep

```bash
安装
```

- jenkins

首先`java -jar jenkins.war`,然后配置即可，碰到问题.在页面上更改源不支持。

```bash
用root账号登陆jenkins master所在的机器，执行如下命令

find / -name "default.json"   #找到官方安装插件对应的json文件位置
/root/jenkins_home/updates/default.json

sed -i 's/http:\/\/updates.jenkins-ci.org\/download/http:\/\/mirrors.tools.huawei.com\/jenkins/g' /root/jenkins_home/updates/default.json 
sed -i 's/http:\/\/www.google.com/http:\/\/w3.huawei.com/g' /root/jenkins_home/updates/default.json 

重启jenkins生效 xxx:8080/restart
```

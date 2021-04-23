## Second OS

    WIN10和Ubuntu会出现时间不一致问题：差了8个小时
    Grub2.0默认启动选项问题：默认Ubuntu，可以修改为默认WIN10
    网易云音乐Linux版启动问题：单点图标无法启动
    Wine QQ 字体乱码问题：部分汉字出现方框乱码
    
## Grub Theme

- PickATheme: [link](https://www.gnome-look.org/browse/cat/109/ord/rating/)
- Install
```
sudo ./install.sh
```

## TimeZone

```
为了保持一致，我们需要将Ubuntu的时间改成本地时间

以前版本的的方法是：

编辑/etc/default/rcS将UTC=yes改成UTC=no

Ubuntu 16.04使用systemd启动之后，时间也改成了由timedatectl来管理，而时间同步也由timedatectl进行管理，不再使用ntpdate。这种方式同样支持桌面和服务器版。

更改方法是执行

timedatectl set-local-rtc 1 --adjust-system-clock（#解释：RTC为硬件时间，即BIOS的时间，而adjust为写入到RTC中的选项）

最后重启。
```

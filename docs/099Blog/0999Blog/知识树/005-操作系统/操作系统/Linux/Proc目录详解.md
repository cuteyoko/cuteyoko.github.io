# Proc目录解析

伪文件系统(in-memory pseudo-file system)。该目录下保存的不是真正的文件和目录，而是一些“运行时”信息，如系统内存、磁盘io、设备挂载信息和硬件配置信息等。

- 绝大部分文件大小为0

`cat proc/cmdline`执行结果
```
BOOT_IMAGE=/vmlinuz-5.4.0-70-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro
```
`ls /proc`:

```
.       111   130  21      272222  305356  34   441  57812  723  85229  930        execdomains  kpagecount    sched_debug    uptime
..      112   133  22      272223  306135  35   443  6      736  85244  953        fb           kpageflags    schedstat      version
1       115   14   23      28      306271  36   45   642    740  85246  acpi       filesystems  loadavg       scsi           version_signature
10      1153  149  236     29      306272  38   46   643    742  85366  buddyinfo  fs           locks         self           vmallocinfo
103     116   15   237     292     306283  39   47   644    750  85367  bus        interrupts   mdstat        slabinfo       vmstat
104     1162  16   238     296469  306683  392  474  645    752  85414  cgroups    iomem        meminfo       softirqs       zoneinfo
104289  1174  17   24      297211  307092  4    48   646    758  85422  cmdline    ioports      misc          stat
105     1178  18   241166  297221  32      40   484  657    761  85492  consoles   irq          modules       swaps
107     118   187  241168  3       33      408  50   658    765  855    cpuinfo    kallsyms     mounts        sys
1079    119   188  25008   30      332     41   51   659    781  85503  crypto     kcore        mtrr          sysrq-trigger
108     12    189  259284  300884  333     42   52   663    792  85516  devices    keys         net           sysvipc
109     120   190  26      300888  334     437  53   664    793  870    diskstats  key-users    pagetypeinfo  thread-self
11      121   2    261     302829  335     438  54   689    830  894    dma        kmsg         partitions    timer_list
110     13    20   27      304474  336     44   55   721    845  9      driver     kpagecgroup  pressure      tty
```

## man proc

查看proc内文件含义
/proc/XXX XXX是指以进程PID（数字编号）命名的目录


```
arch_status  clear_refs       cwd      gid_map    maps        net        oom_score_adj  root       smaps         status         uid_map
attr         cmdline          environ  io         mem         ns         pagemap        sched      smaps_rollup  syscall        wchan
autogroup    comm             exe      limits     mountinfo   numa_maps  patch_state    schedstat  stack         task
auxv         coredump_filter  fd       loginuid   mounts      oom_adj    personality    sessionid  stat          timers
cgroup       cpuset           fdinfo   map_files  mountstats  oom_score  projid_map     setgroups  statm         timerslack_ns

```

### 一、信息显示

​	Linux系统可以通过top命令查看系统的CPU、内存、运行时间、交换分区、执行的线程等信息。

```java
[root@foundation6 docker]# top

top - 21:31:26 up 15:16,  5 users,  load average: 0.61, 0.82, 0.75
Tasks: 240 total,   2 running, 238 sleeping,   0 stopped,   0 zombie
%Cpu(s): 13.7 us,  1.5 sy,  0.0 ni, 84.2 id,  0.6 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  3775264 total,   250100 free,  2495300 used,  1029864 buff/cache
KiB Swap:  4064252 total,  2789544 free,  1274708 used.   527664 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND    
16507 kiosk     20   0 1935284 201988  10816 R  46.8  5.4  68:11.92 plugin-con+
15773 kiosk     20   0 1784208 497692  40776 S   4.7 13.2  37:05.32 firefox    
  408 root      20   0   36940   4116   3920 S   3.0  0.1   4:51.67 systemd-jo+
 3789 kiosk     20   0  747664  14124   4696 S   2.0  0.4   2:49.76 gnome-term+
 2404 root      20   0  439488 106688  84580 S   1.7  2.8  16:08.35 Xorg       
 2662 kiosk      9 -11  700096   5232   3032 S   1.7  0.1   5:17.25 pulseaudio 
21632 kiosk     20   0  812940 167440  30100 S   1.7  4.4  20:15.48 wps        
 2688 kiosk     20   0 2111764 218776  18580 S   1.3  5.8  20:25.33 gnome-shell
  663 root      20   0  399976   3352   2984 S   1.0  0.1   0:46.92 rsyslogd   
 7349 qemu      20   0 1697464 956932    556 S   0.7 25.3   5:03.80 qemu-kvm   
 7803 qemu      20   0 1697460 708164    544 S   0.7 18.8   4:16.74 qemu-kvm   
   18 root      20   0       0      0      0 S   0.3  0.0   0:16.94 rcuos/0    
   19 root      20   0       0      0      0 S   0.3  0.0   0:18.43 rcuos/1    
   21 root      20   0       0      0      0 S   0.3  0.0   0:19.62 rcuos/3    
  671 root      20   0  207984    160    120 S   0.3  0.0   0:01.60 abrt-watch+
 5676 root      20   0       0      0      0 S   0.3  0.0   0:00.28 kworker/u1+
```

#### top命令第一行：

```java
top - 21:31:26 up 15:16,  5 users,  load average: 0.61, 0.82, 0.75
```

依次对应：系统当前时间up系统到目前为止运行的时间，当前登录系统的用户数量。load average后面三个数字分别表示距离现在一分钟，五分钟，十五分钟的负载情况。

注意：load average数据是每隔5秒钟检查一次活跃的进程数，然后按特定算法计算出的数值。如果这个数除以逻辑CPU的数量，结果高于5的时候就表明系统在超负荷运转了。

一般情况下，逻辑cpu=物理CPU个数×每颗核数。

#### top命令第二行：

```java
Tasks: 240 total,   2 running, 238 sleeping,   0 stopped,   0 zombie
```

依次对应：tasks表示任务（进程），240 total表示现在有240个进程，其中处于运行的有2个，238个在休眠（挂起），zombie状态即僵尸的进程数为0个。

#### top命令第三行：

```java
%Cpu(s): 13.7 us,  1.5 sy,  0.0 ni, 84.2 id,  0.6 wa,  0.0 hi,  0.0 si,  0.0 st
```

依次对应：

us：user用户空间占用cpu的百分比

sy: system内核空间占用cpu的百分比

ni:niced改变过优先级的进程占用cpu的百分比

id:空闲cpu百分比

wa:wait IO等待占用cpu的百分比

hi:Hardware IRQ 硬中断 占用cpu的百分比   --硬中断一般是由外设引发

si:software 软中断 占用cpu的百分比   --由程序调用引发

st:被hypervisor偷去的时间

#### top命令第四行，内存状态

```java
KiB Mem :  3775264 total,   250100 free,  2495300 used,  1029864 buff/cache
```

依次对应：物理内存总量（3.7G），空闲内存总量（2.5G），使用中的内存总量（2.4G），缓冲内存量

### top命令第五行，swap交换分区：

```java
KiB Swap:  4064252 total,  2789544 free,  1274708 used.   527664 avail Mem 
```

依次对应：交换区总量（4G），空闲交换区总量（2.7G），使用的交换区总量（1.2G），可用交换区总量（0.5G）

对于内存监控，在top里我们要时刻监控第五行swap交换分区的used，如果这个数值在不断的变化，说明内核在不断进行内存和swap的数据交换，这时真正的内存不够用了。



### top命令第6行

| 字符    |                             含义                             |
| ------- | :----------------------------------------------------------: |
| PID     |                            进程号                            |
| USER    |                          进程创建者                          |
| PR      |                          进程优先级                          |
| NI      |  nice值。越小优先级越高，最小-20，最大20（用户设置最大19）   |
| VIRT    |        进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES         |
| RES     |  进程使用的、未被换出的物理内存大小，单位kb。RES=CODE+DATA   |
| SHR     |                     共享内存大小，单位kb                     |
| S       | 进程状态。D=不可中断的睡眠状态 R=运行 S=睡眠 T=跟踪/停止 Z=僵尸进程 |
| %CPU    |                      进程占用cpu百分比                       |
| %MEM    |                      进程占用内存百分比                      |
| TIME+   |                         进程运行时间                         |
| COMMAND |                           进程名称                           |

### TOP命令 (在进入top后使用)

P：以占据CPU百分比排序

M：以占据内存百分比排序

T：以积累占用CPU时间排序

Q：退出命令



### 注：

总核数 = 物理CPU个数 * 每颗物理CPU的核数

总逻辑CPU数= 物理CPU个数 * 每颗物理CPU的核数 * 超线程数

```
# 查看物理CPU个数
cat /proc/cpuinfo | grep "physical id" | sort | uniq |wc -l

# 查看每个物理CPU中core的个数（即核数）
cat /proc/cpuinfo | grep "cpu cores" | uniq

# 查看逻辑CPU的个数
cat /proc/cpuinfo | grep "processor" | wc -l

# 查看内存信息
cat /proc/meminfo
```












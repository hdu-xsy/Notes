# Linux

## Linux操作系统基础知识

### Linux操作系统发展历史介绍
* Linux/Unix历史与概述
* Linux是什么
* 自由软件
* GNU项目
* GNU GPL
* 开源软件

### Linux操作系统的现状

### Linux的内核与发行版
* Linux的发行版
* Ubuntu
* Mark Shuttleworth
* Ubuntu是什么

### Linux帮助系统
* man
* --help
* info
* whatis
* /usr/share/man

### Linux接口介绍
* 整个系统调用的过程
* 四个重要的宏
* 系统调用表

## Linux安装与更新

### Linux发行版获取与选择
### Linux发行版U盘启动盘制作
### 虚拟机软件介绍
### Linux系统的安装
### Linux网络基本配置
* 网络基本配置命令
  * ifconfig
     * 查看所有网络接口信息  
     ifconfig -a
     * 查看指定接口信息  
     ifconfig eth0
     * 给接口指定ip地址  
     ifconfig eth0 add ip
     * IPv6  
     ifconfig eth0 add/del ipv6
  * route 
     * 查看路由表  
     route
     * 添加路由信息  
     route add  -net ip netmask 子关掩码 dev eth0
     * 添加默认网关
     route add default gw 网关
* 网络相关配置文件
  * /etc/sysconfig/network  
  主机名(HOSTNAME)、网关(GATEWAY)
  * /etc/sysconfig/network-scripts/ipcfg-eth0  
  以太网接口卡的基本信息(BOOTPROTO、NETMASK、IPADDR、BROADCAST)
  * /etc/resolv.conf  
  DNS服务器地址(nameserver)
  * /etc/hosts  
  IP地址与主机名相对应
  * /etc/host.conf  
  优先级：order hosts,bind
  * /etc/nsswitch.conf  
  优先级：hosts：files dns
  * /etc/networks  
  网络名称与ip相对应
  * /etc/services  
  服务名称与通信端口号之间映射
  * /etc/protocols  
  协议名称与协议编号对应关系
* 网络启动脚本与service命令
  * /etc/rc.d/init.d/network start/stop/restart
  * service network start/stop/restart
* 网络相关实用工具
  * system-config-network
  * ping ping6
  * netstat
  * tcpdump
  * nslookup
  * telnet
  * traceroute
  * arp
  
    
### Linux中文环境配置
### Linux系统更新

## Linux软件管理

### Linux软件包概述
### RPM包和DEB包的安装
### 软件包安装工具
### 原码编译与安装

## Linux Shell及脚本

### Linux操作系统Shell介绍
### Vim编辑器介绍
* 编辑
* 程序
* 设置
* 移动
* 折行
* 文本替换
* 保存与退出
* 其他

### Bash Shell基础和常用命令
* 查看文件内容
  * cat
  * more
  * less
  * zless
* 文件系统
  * ls (-al)
  * cp (-p -r)
  * rm (-rf)
  * mv
  * pwd
  * cd
  * mkdir(-p)
  * rmdir
  * rm
  * grep
  * file
  * find (find /home -atime 14 -ok rm {} \;     find /home -exec 14 -ok rm {} \;)
  * tail(-f)
* 排序比较
  * sort
  * smp
  * comm
  * diff
* 用户管理
  * chown
  * chmod
  * chgrp
  * useradd
  * userdel
  * usermod
  * passwd
  * groupadd
  * su
  * who
  * w
  * whoami
  * users
  * finger
* 压缩与解压缩
  * tar (-xzvf xcvf)
  * gzip (-d S l r)
* 统计
  * wc -l
* 进程
  * ps
  * kill
  * &
* cpu、内存、磁盘
  * top
  * free
  * df
* 文件系统装卸
  * mount/unmount
* 别名
  * alias
* 日期
  * date +“%Y-%m-%d %H:%M:%S"
* 定时执行任务
  * trontab -e (minute hour day month week  command)

### Shell脚本编程基础
* shell变量
* shell传递参数
* shell数组
* shell运算符
* shell流程控制
  * if  
  
```
if [ condition ]
then
elif
else
fi
```
  * for
  
```
for var in item1 itemN
do
done
```

  * while
  
```
while [ condition ]
do
done
```

  * until
  * case
* shell函数

```
func(){
}
func
```  

## Linux系统管理
### 基于终端的系统管理

### 文件系统  
目录|描述
----|------
/|主层次 的根，也是整个文件系统层次结构的根目录
/bin|存放在单用户模式可用的必要命令二进制文件，所有用户都可用，如 cat、ls、cp等等
/boot|存放引导加载程序文件，例如kernels、initrd等
/dev|存放必要的设备文件，例如/dev/null
/etc|存放主机特定的系统级配置文件。
/etc/opt|存储着新增包的配置文件 /opt/.
/etc/sgml|存放配置文件，比如 catalogs，用于那些处理SGML(标准通用标记语言)的软件的配置文件
/home|用户的主目录，包括保存的文件，个人配置，等等/lib	/bin/ 和 /sbin/中的二进制文件的必需的库文件
/lib<架构位数>|备用格式的必要的库文件。
/media|可移动的多媒体(如CD-ROMs)的挂载点
/mnt|临时挂载的文件系统
/opt|可选的应用程序软件包
/proc|以文件形式提供进程以及内核信息的虚拟文件系统，在Linux中，对应进程文件系统（procfs ）的挂载点
/root|根用户的主目录
/sbin|必要的系统级二进制文件，比如， init, ip, mount
/srv|系统提供的站点特定数据
/tmp|临时文件 (另见 /var/tmp). 通常在系统重启后删除
/usr|二级层级存储用户的只读数据； 包含(多)用户主要的公共文件以及应用程序
/usr/bin|非必要的命令二进制文件 (在单用户模式中不需要用到的)；用于所有用户
/usr/include|标准的包含文件
/usr/lib|库文件，用于/usr/bin/ 和 /usr/sbin/中的二进制文件
/usr/lib<架构位数>|备用格式库(可选的)
/usr/local|三级层次 用于本地数据，具体到该主机上的。通常会有下一个子目录, 比如, bin/, lib/, share/.
/usr/local/sbin|非必要系统的二进制文件，比如用于不同网络服务的守护进程
/usr/share|架构无关的 (共享) 数据.
/usr/src|源代码，比如内核源文件以及与它相关的头文件
/var|各式各样的（Variable）文件，一些随着系统常规操作而持续改变的文件就放在这里，比如日志文件，脱机文件，还有临时的电子邮件文件
/var/cache|应用程序缓存数据. 这些数据是由耗时的I/O(输入/输出)的或者是运算本地生成的结果。
/var/lock|锁文件。这些文件用于跟踪正在使用的资源
/var/log|日志文件。包含各种日志。
/var/mail|内含用户邮箱的相关文件
/var/opt|来自附加包的各种数据都会存储在 /var/opt/.
/var/run|存放当前系统上次启动以来的相关信息，例如当前登入的用户以及当前运行的daemons(守护进程).
/var/spool|该spool主要用于存放将要被处理的任务，比如打印队列以及邮件外发队列
/var/mail|过时的位置，用于放置用户邮箱文件
/var/tmp|存放重启后保留的临时文件

### 系统日志
### /proc虚拟文件系统
### 基于Denyhost的安全远程访问管理
### 用户管理
### 网络管理

## Linux服务器应用

### Linux常用服务器应用概述
### Web服务器
* 更改监听端口  
Listen
* 设置目录访问  
* 虚拟站点  


<VirtualHost ip>
DocumentRoot Path
ServerName name
</VirtualHost>

### FTP服务器
* 主动模式
* 被动模式

### DNS服务器
### DHCP服务器
### SSH服务器
### Mail服务器
### SAMBA服务器
# Linux内核编译及添加系统调用

> 先安装ccache可以大幅节省多次内核编译时间

1. 进入内核目录
2. 分配系统调用号,修改系统调用表
```
vim ./arch/x86/entry/syscalls/syscall_64.tbl

尾部添加 [549]为最后一个系统调用号递增
549     64      mysetnice               sys_mysetnice
```
3. 申明系统调用服务例程原型
```
vim ./include/linux/syscalls.h

asmlinkage long sys_mysetnice(pid_t pid,int flag,int nicevalue); //,void __user* prio,void __user* nice);
```
4. 实现系统调用服务例程
```
vim ./kernel/sys.c

SYSCALL_DEFINE3(mysetnice, pid_t, pid, int, flag, int, nicevalue)
{
    struct pid * kpid;
    struct task_struct * task;
    int nicebef;
    kpid = find_get_pid(pid);/* 返回pid */
    task = pid_task(kpid, PIDTYPE_PID);/* 返回task_struct */
    nicebef = task_nice(task);/* 返回进程当前nice值 */
    if(flag == 1)
    {
        set_user_nice(task, nicevalue);/* 修改进程nice值 */
        printk("修改前nice值：%d\t修改后nice值：%d\n", nicebef, nicevalue);
        return 0;
    }
    else if(flag == 0)
    {
        printk("该进程的nice值为%d\n", nicebef);
        return 0;
    }
    return EFAULT;
}

```
5. 重新编译内核
```
make oldconfig
make
make modules
make modules_install
make install
grub2-mkconfig -o /boot/grub2/grub.cfg  //centos
update-grub2  //ubuntu
reboot
```
6. 编写用户态程序测试新系统调用
```
#include <linux/unistd.h>
#include <sys/syscall.h>
#include <stdio.h>
#include <stdlib.h>
#define __NR_mysetnice 549

int main(int argc, char *argv[])
{
     pid_t tid;
     int nicevalue = atoi(argv[1]);
     tid = getpid();
     syscall(__NR_mysetnice,tid,0,0);
     syscall(__NR_mysetnice,tid,1,nicevalue);
     syscall(__NR_mysetnice,tid,0,0);

     //syscall(__NR_mysetnice,tid,flag,nicevalue);
     return 0;
}
```
7. 查看输出内容
```
dmesg
```
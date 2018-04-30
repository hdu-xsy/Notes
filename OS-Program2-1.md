> Makefile 和 OS-2-1.c在同一文件夹内即可

- Makefile
```
obj-m:=OS-2-1.o
OS21-objs:=OS-2-1.o
KDIR:=/home/kannaduki/linux/linux-4.15.15
PWD:= $(shell pwd)
default:
	make -C $(KDIR) M=$(PWD) modules
clean:
	make -C $(KDIR) M=$(PWD) clean

```

- OS-2-1.c
```
#include<linux/init.h>
#include<linux/module.h>
#include<linux/kernel.h>
// 内核版本为3.X 则为<linux/sched.h>
#include<linux/sched/signal.h>
static int hello_init(void)
{
        struct task_struct *p= &init_task;
	printk(KERN_ALERT"\n\n\n----------------------");
	printk(KERN_ALERT"------所有进程程序名 pid 进程状态 父进程------");
        for_each_process(p) {
	if(p->mm == NULL)
		printk(KERN_ALERT"程序名: %s Pid: %d 进程状态: %ld 父进程pid: %d\n",p->comm,p->pid, p->state,p->real_parent->pid);
        }
	printk(KERN_ALERT"----------------------\n\n\n");
        return 0;
}
static void hello_exit(void)
{
        printk(KERN_ALERT "______Exit______ !\n");
}
module_init(hello_init);
module_exit(hello_exit);
MODULE_LICENSE("GPL");

```
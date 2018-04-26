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
#include<linux/sched/signal.h>
static int hello_init(void)
{
        struct task_struct *p= &init_task;
        for_each_process(p) {
	if(p->mm == NULL)
		printk(KERN_ALERT"程序名: %s Pid: %d 进程状态: %ld 父进程pid: %d\n",p->comm,p->pid, p->state,p->real_parent->pid);
        }
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
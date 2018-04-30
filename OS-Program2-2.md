- Makefile
```
obj-m:=OS-2-2.o
OS22-objs:=OS-2-2.o
KDIR:=/home/kannaduki/linux/linux-4.15.15
PWD:= $(shell pwd)
default:
	make -C $(KDIR) M=$(PWD) modules
clean:
	make -C $(KDIR) M=$(PWD) clean

```

- family.c
```
#include<linux/init.h>
#include<linux/module.h>
#include<linux/kernel.h>
#include<linux/pid.h>
// 内核版本为3.X 则为<linux/sched.h>
#include <linux/sched/signal.h>
#include <linux/moduleparam.h>

static pid_t pid=0;
module_param(pid,int,0644);

static int family_init(void)
{
	struct task_struct *p;
	struct list_head *pp;
	struct task_struct *pl;
        if(pid == 0) {
                printk("输入错误\n");
		return 0;
        }
	p = pid_task(find_vpid(pid), PIDTYPE_PID);
	if(!p) {
		printk("\n\n\n------进程不存在----\n\n\n");
		return 0;
	}
	printk("\n\n\n\n\n\n\n\n\n");
	printk("-----进程号------\n");
	printk("%d\n",pid);
	printk("-----父进程------\n");
	if(p->parent==NULL)
	{
		printk("父进程不存在\n");
	}
        else
	{
		printk("pid: %d 程序名:%s 进程状态: %ld\n",p->parent->pid,p->parent->comm,p->parent->state);
	}
	printk("------兄弟进程-------\n");
	list_for_each(pp,&p->parent->children)
	{
		pl=list_entry(pp,struct task_struct,sibling);
		printk("pid: %d 程序名: %s 进程状态: %ld\n",pl->pid,pl->comm,pl->state);
	}
	printk("---------子进程--------\n");
	if(&p->children==NULL) printk("子进程不存在");
	list_for_each(pp,&p->children)
	{
		pl=list_entry(pp,struct task_struct,sibling);
		printk("pid: %d 程序名: %s 进程状态: %ld\n",pl->pid,pl->comm,pl->state);
	}
	printk("-----------------------\n\n\n\n\n\n");
        return 0;
}
static void family_exit(void)
{
        printk(KERN_ALERT "\n--------Exit---------\n");
}

module_init(family_init);
module_exit(family_exit);
MODULE_LICENSE("GPL");

```
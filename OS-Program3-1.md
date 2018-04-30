- cmd1、cm2、cmd3.c
```
#include<stdio.h>
void  main(){
	printf("cmd1运行完毕\n");
}

```
`gcc cmd1.c -o cmd1`

- shell.c
```
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<unistd.h>
void  main(){
	int pid;
	int i;
	char *cmd = (char*)malloc(sizeof(20));
	while(1) {
		printf("输入指令\n");
		scanf("%s",cmd);
                if(strcmp(cmd,"exit")==0)       return;
		pid=vfork();
		if (pid) {
			waitpid(pid,NULL,0);
		}else{
			if(strcmp(cmd,"cmd1")==0 || strcmp(cmd,"cmd2")==0 || strcmp(cmd,"cmd3")==0){
				execl(cmd,cmd,NULL);
			}else{
				printf("Command not found\n");
				_exit(0);
			}
		}
	}
}

```
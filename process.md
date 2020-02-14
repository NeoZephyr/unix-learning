```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

extern int create_process (char* program, char** arg_list);

int create_process (char* program, char** arg_list)
{
    pid_t child_pid;
    child_pid = fork ();
    if (child_pid != 0)
        return child_pid;
    else {
        execvp(program, arg_list);
        abort();
    }
}
```
```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
 
extern int create_process (char* program, char** arg_list);
 
int main ()
{
    char* arg_list[] = {
        "ls",
        "-l",
        "/etc/yum.repos.d/",
        NULL
    };
    create_process ("ls", arg_list);
    return 0;
}
```
```sh
gcc -c -fPIC process.c
gcc -c -fPIC createprocess.c
```
```sh
# 静态链接库
ar cr libstaticprocess.a process.o
```
```sh
# -L 表示在当前目录下找.a 文件
gcc -o staticcreateprocess createprocess.o -L. -lstaticprocess
```
静态链接库一旦链接，程序运行的时候，就不依赖于这个库是否存在。这样会有以下缺点：相同的代码段被多个程序使用，在内存里面就有多份；静态链接库更新，二进制执行文件也需要重新编译

```sh
# 动态链接库
gcc -shared -fPIC -o libdynamicprocess.so process.o
```
```sh
gcc -o dynamiccreateprocess createprocess.o -L. -ldynamicprocess
```
当一个动态链接库被链接到一个程序文件中的时候，最后的程序文件并不包括动态链接库中的代码，而仅仅包括对动态链接库的引用，并且不保存动态链接库的全路径，仅仅保存动态链接库的名称

当运行这个程序的时候，首先寻找动态链接库并加载。默认情况下，系统在 /lib 和 /usr/lib 文件夹下寻找动态链接库，如果找不到就会报错。可以设定 LD_LIBRARY_PATH 环境变量，程序运行时会在此环境变量指定的文件夹下寻找动态链接库
```sh
export LD_LIBRARY_PATH=.
./dynamiccreateprocess
```

```c

#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
 
#define NUM_OF_TASKS 5
 
void *downloadfile(void *filename)
{
   printf("I am downloading the file %s!\n", (char *)filename);
   sleep(10);
   long downloadtime = rand()%100;
   printf("I finish downloading the file within %d minutes!\n", downloadtime);
   pthread_exit((void *)downloadtime);
}
 
int main(int argc, char *argv[])
{
   char files[NUM_OF_TASKS][20]={"file1.avi","file2.rmvb","file3.mp4","file4.wmv","file5.flv"};
   pthread_t threads[NUM_OF_TASKS];
   int rc;
   int t;
   int downloadtime;
 
   pthread_attr_t thread_attr;
   pthread_attr_init(&thread_attr);
   pthread_attr_setdetachstate(&thread_attr, PTHREAD_CREATE_JOINABLE);
 
   for(t=0;t<NUM_OF_TASKS;t++){
     printf("creating thread %d, please help me to download %s\n", t, files[t]);
     rc = pthread_create(&threads[t], &thread_attr, downloadfile, (void *)files[t]);
     if (rc){
       printf("ERROR; return code from pthread_create() is %d\n", rc);
       exit(-1);
     }
   }
 
   pthread_attr_destroy(&thread_attr);
 
   for(t=0;t<NUM_OF_TASKS;t++){
     pthread_join(threads[t],(void**)&downloadtime);
     printf("Thread %d downloads the file %s in %d minutes.\n",t,files[t],downloadtime);
   }

   pthread_exit(NULL);
}
```
```sh
# 多线程程序要依赖于 libpthread.so
gcc download.c -lpthread
```

查看线程栈大小
```sh
ulimit -a
```
修改线程栈大小
```sh
ulimit -s
```
```c
int pthread_attr_setstacksize(pthread_attr_t *attr, size_t stacksize);
```
```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
 
#define NUM_OF_TASKS 5
 
int money_of_tom = 100;
int money_of_jerry = 100;
pthread_mutex_t g_money_lock;
 
void *transfer(void *notused)
{
  pthread_t tid = pthread_self();
  printf("Thread %u is transfering money!\n", (unsigned int)tid);
  pthread_mutex_lock(&g_money_lock);
  sleep(rand()%10);
  money_of_tom+=10;
  sleep(rand()%10);
  money_of_jerry-=10;
  pthread_mutex_unlock(&g_money_lock);
  printf("Thread %u finish transfering money!\n", (unsigned int)tid);
  pthread_exit((void *)0);
}
 
int main(int argc, char *argv[])
{
  pthread_t threads[NUM_OF_TASKS];
  int rc;
  int t;
  pthread_mutex_init(&g_money_lock, NULL);
 
  for(t=0;t<NUM_OF_TASKS;t++){
    rc = pthread_create(&threads[t], NULL, transfer, NULL);
    if (rc){
      printf("ERROR; return code from pthread_create() is %d\n", rc);
      exit(-1);
    }
  }
  
  for(t=0;t<100;t++) {
    pthread_mutex_lock(&g_money_lock);
    printf("money_of_tom + money_of_jerry = %d\n", money_of_tom + money_of_jerry);
    pthread_mutex_unlock(&g_money_lock);
  }

  pthread_mutex_destroy(&g_money_lock);
  pthread_exit(NULL);
}
```
```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
 
#define NUM_OF_TASKS 3
#define MAX_TASK_QUEUE 11
 
char tasklist[MAX_TASK_QUEUE]="ABCDEFGHIJ";
int head = 0;
int tail = 0;
 
int quit = 0;
 
pthread_mutex_t g_task_lock;
pthread_cond_t g_task_cv;
 
void *coder(void *notused)
{
  pthread_t tid = pthread_self();
 
  while(!quit){
 
    pthread_mutex_lock(&g_task_lock);
    while(tail == head){
      if(quit){
        pthread_mutex_unlock(&g_task_lock);
        pthread_exit((void *)0);
      }
      printf("No task now! Thread %u is waiting!\n", (unsigned int)tid);

      // 只有抢到锁之后才能从这个函数中返回
      pthread_cond_wait(&g_task_cv, &g_task_lock);
      printf("Have task now! Thread %u is grabing the task !\n", (unsigned int)tid);
    }
    char task = tasklist[head++];
    pthread_mutex_unlock(&g_task_lock);
    printf("Thread %u has a task %c now!\n", (unsigned int)tid, task);
    sleep(5);
    printf("Thread %u finish the task %c!\n", (unsigned int)tid, task);
  }
 
  pthread_exit((void *)0);
}
 
int main(int argc, char *argv[])
{
  pthread_t threads[NUM_OF_TASKS];
  int rc;
  int t;
 
  pthread_mutex_init(&g_task_lock, NULL);
  pthread_cond_init(&g_task_cv, NULL);
 
  for(t=0;t<NUM_OF_TASKS;t++){
    rc = pthread_create(&threads[t], NULL, coder, NULL);
    if (rc){
      printf("ERROR; return code from pthread_create() is %d\n", rc);
      exit(-1);
    }
  }
 
  sleep(5);
 
  for(t=1;t<=4;t++){
    pthread_mutex_lock(&g_task_lock);
    tail+=t;
    printf("I am Boss, I assigned %d tasks, I notify all coders!\n", t);
    pthread_cond_broadcast(&g_task_cv);
    pthread_mutex_unlock(&g_task_lock);
    sleep(20);
  }
 
  pthread_mutex_lock(&g_task_lock);
  quit = 1;
  pthread_cond_broadcast(&g_task_cv);
  pthread_mutex_unlock(&g_task_lock);
 
  pthread_mutex_destroy(&g_task_lock);
  pthread_cond_destroy(&g_task_cv);
  pthread_exit(NULL);
}
```
# timerlib

## 描述
一个简单的C语言定时器
## API:
``` c
int  TimerInit();

/*
*
*Timer Destory, free any data allocated for the library.
*/
void TimerDestroy();

/*
*add a Timer
*
*/
int  TimerAdd(long sec, 
            long usec, 
            void (*hndlr)(void*), 
            void* hndlr_arg, 
            int *id);

/*
*Remove from the queue the timer of identifier "id"
*/
void TimerRemove(int id, void (*free_arg)(void*));

/*
*Prints the content of the timer queue (to debug)
*/
void TimePrint();
```

## 简单例子
```c
/* 
 *test code 
 */
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdint.h>
#include <sys/types.h>
#include <unistd.h>
#include <pthread.h>
#include <signal.h>
#include "timerlib.h"
static int count = 0;

void timer_handler(void* arg)
{
    printf("%s\n", (char*)arg);
    count++;

    return;
}

int main()
{
    int ret      = 0;
    int TimerId1 = 0;
    int TimerId2 = 0;
    int TimerId3 = 0;

    sigset_t sigset;
    sigemptyset(&sigset);
    sigaddset(&sigset, SIGALRM);
    pthread_sigmask(SIG_BLOCK, &sigset, NULL);

    ret = TimerInit();
    if (ret < 0) {
        printf("Timer init error\n");
        return -1;
    }

    TimerAdd(3, 0, &timer_handler, "This is TimerTest 1", &TimerId1);
    TimerAdd(6, 0, &timer_handler, "This is TimerTest 2", &TimerId2);
    TimerAdd(9, 0, &timer_handler, "This is TimerTest 3", &TimerId3);
    while (1) {
        if (count == 3) {
            break;
        }
        usleep(1000);
    }

    TimerDestroy();

    return 0;
}

//运行结果
[zhangwj@host timerlib]$ ./TimerTest 
This is TimerTest 1
This is TimerTest 2
This is TimerTest 3
[zhangwj@host timerlib]$    
```

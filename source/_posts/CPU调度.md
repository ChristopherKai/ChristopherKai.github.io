---
title: CPU调度
tags:
  - Note
  - Operating System
  - 操作系统
---


目的：CPU作为一种资源，应当提高利用率，合理利用。

发现：进程执行存在着CPU和IO周期，不断在使用CPU和IO操作间切换。IO瓶颈型进程多为短暂的CPU burst，CPU瓶颈型进程CPU burst较长。

![CPU burst持续时间分布][1]


  
## CPU调度器

职责：从就绪队列(ready queue)选择即将占用CPU的进程。就绪队列没必要以FIFO队列形式存在，可以是链表，优先队列，树等。

## 抢占式调度 preemptive scheduling

非抢占式调度发生在：
    1.进程主动从运行态切换至等待态(IO操作或者wait()子进程)
    2.进程终结

抢占调度发生在：
    1.进程从运行态转为就绪态（比如发生中断）
    2.进程从等待态转为就绪态（比如IO完成）
    
抢占式调度必须依赖特定硬件环境才可实现，比如一个timer。


抢占调度带来的问题：
1.race condition，两个进程访问同一内存，发生抢占
2.内核设计。系统调用等。


## Dispatcher

职责：
1.上下文切换
2.特权模式切换
3.重启进程

从一个进程切换到另外一个进程的时间被称为dispatch latency。

## 调度算法评价指标

1.CPU利用率。
2.吞吐量。单位时间内完成的进程数。
3.turnaround 时间：进程从加载到内存，就绪队列等待时间，CPU执行时间，IO等待时间。
4.等待时间：进程在就绪队列的等待时间。
5.响应时间。

一般来讲，倾向于最大化1.2.，最小化3.4.5.。研究发现，在交互式系统上，最小化响应时间的方差比最小化平均响应时间效果更好。因为一个有着合理并可预测响应时间的交互式系统更好用。



## 调度算法

### FCFS

First Come First Serve算法：先请求CPU的先使用CPU。

实现：FIFO队列。
抢占式：否
缺点：convoy effect

### SJF

Shortest Job First算法：按照进程下次使用CPU的时长，最小的优先使用CPU。

别名：shortest-nextCPU-burst算法。
实现：优先队列
抢占式：是
备注：短期调度器实现困难，因为进程的下一次CPU burst难以预估。在长期调度器中使用频繁。

Un+1 = a * tn +　(1 - a)Un 指数平均数，根据历史估测和历史CPU burst时长来来估算下一次CPU burst。

### Priority Scheduling

实现：优先队列
抢占式：不定，可以抢占CPU或者仅仅放入优先队列

优先队列调度会产生饥饿，即一些进程永远得不到CPU时间。解决办法，引入aging，随着时间增加，提升进程优先级，最终低优先级进程会得到执行。

### Round-Robin

Round-Robin：队列内所有进程分配特定大小时间片，进程执行时间小于该时间片，切换到下一个进程，进程执行时间长于该时间将被下一个进程抢占CPU。

抢占式：是
实现：循环队列

在时间片很大情况下，退化为FCFS算法。

### 多级队列 MQ

根据进程特性分配到不同队列，各个队列有各自的调度算法，队列间存在优先级，进程无法在队列间切换优先级。

### 多级反馈队列

进程优先级会在各个队列间移动。结果：IO密集型会在高优先级队列，CPU密集型会在低优先级队列。


## 线程调度

### PCS (Process Contention Scope)

在多对一，多对多线程模型下，多个用户线程互相竞争分配到内核线程的机会，所以，竞争范围为一个进程内，为PCS。

### SCS (System Contention Scope)

内核线程一起竞争CPU，竞争范围为SCS。

### POSIX 设置竞争范围api
```cpp
// scope 可选值PTHREAD_SCOPE_PROCESS,PTHREAD_SCOPE_SYSTEM
pthread_attr_setscope(pthread_attr_t *attr, int scope)
pthread_attr_getscope(pthread_attr_t *attr, int *scope)
```
在Linux和Mac OS上只支持PTHREAD_SCOPE_SYSTEM。


## 多处理器调度

主要目标负载共享，load sharing。

SMP:symmetric multiprocessing


## Processor Affinity 进程亲和性
定义：进程偏向当前正在执行的处理器。
原因：每个处理器都有自己的缓存cache。当一个进程从一个CPU调度到另一个CPU的时候，原CPU和该进程有关的缓存必须invalidate，调度到的CPU的缓存必须重新产生，这样，调度的时候最好不要将进程跨处理器调度，降低cache更新的成本。

soft affinity：尽量使进程一直运行在一个处理器上，但不保证一直运行在一个处理器上。
hard affinity：操作系统提供syscall，让进程只运行在一部分处理器上。


eg. Linux实现了soft affinity，但是可用`sched_setaffinity()`设置hard affinity。

## Load Balancing 负载均衡

Load balancing试图使工作量在处理器间均匀分布。
在每个处理器有私有调度队列时，load balancing是必要的。而所有处理器共享调度队列时，load balacing不必要，调度器会自动将工作分摊给空闲处理器。当代支持SMP的操作系统大多有各自的调度队列。 

load balacing 的两种办法: push migration,pull migration。这两种并不排斥，linux上同时实现了两者。

### push migration

push migration: 特定任务周期性检测各个处理器负载，主动将进程均匀分布。

### pull miragtion

pull migration: 空闲处理器主动将忙处理器的等待进程拉取。

Note.  维持负载均衡的过程中，有违处理器亲和性Processor Affinity。


## 多核处理器

memeory stall: cpu访问内存，等待数据可用的时间。

// TODO 


## 实时操作系统

Soft real-time system： 不保证critical进程何时被调度，但保证critical进程会比non critical进程优先。

Hard real-time system： 保证进程在期限前完成。超出期限则等同于没有服务。


实时操作系统是事件驱动的，事件可能是软件的（时间中断）或者硬件的(汽车探测到路障)。

event latency：事件发生到收到服务的时间。必须保证这段时间内事件得到服务，否则超过期限，服务将无意义。

// TODO

### POSIX 实时调度api


## 操作系统实例

### Linux

版本v2.5以前使用unix调度算法的变体。v2.5开始使用O(1)的调度算法，开始支持SMP，处理器亲和性，负载均衡。由于对交互式进程支持不够好，重新设计了Completely Fair Scheduler（CFS）调度算法。



调度基于调度类scheduling class。scheduling class有不同的优先级。标准linux实现了两种调度类：1.CFS 2.realtime 。为了决定哪个进程占用cpu，调度器从高优先级的调度类内选择高优先级的进程。

#### CFS

不具体根据优先级分配时间片大小，CFS分配一定比例cpu时间。该比例的计算依据nice值，nice取值范围-20 到19，越小优先级越高，分配cpu比例更多，缺省值为0。nice的意思是一个进程越nice，对其他进程越nice，让给其他进程更多cpu。
CFS使用目标延迟(target latency)分配时间，target latency即进程在至少运行一次的时间间隔。目标延迟除了有缺省值，最小值，其会随着进程数量增加而增加。

CFS会记录进程运行时间，用虚拟运行时间vruntime。进程优先级决定了衰减因子(decay factor)，优先级越低衰减因子越大。对缺省nice值0，虚拟运行时间vruntime等于实际运行时间。低优先级进程若vruntime 200ms，实际运行时间会小于200ms。高优先级进程若vruntime为200ms，实际运行时间大于200ms。
调度时，选择vruntime较小的进程，而且高优先级进程会抢占低优先级进程。


具体分析：
比如，两个nice值一样的进程，一个IO密集型，一个CPU密集型。一般第一个进程只会运行短暂时间，第二个则会耗尽时间片。所有，第一个进程的vruntime会比第二个小，优先调度。在第二个进程运行时，若第一个进程IO请求返回时，则会抢占CPU。


实现：
按照进程vruntime作为key组织进程红黑树，就绪队列。

![vruntime红黑树][2]


#### realtime

任何SCHED_FIFO 或SCHED_RR任务优先级高于非实时任务。实时任务优先级静态映射0-99，非实时任务的nice值-20映射100,19映射139。


![Linux进程优先级][3]


### windows

windows使用基于优先级的抢占式调度算法。其确保最高优先级进程一直运行。内核调度模块被称为Dispatcher。最高优先级进程会一直运行直到，被更高优先级进程抢占，终止，IO请求，时间片耗尽。


优先级分为12档。两类优先级：1.可变 variable class 2.realtime class。
第一类 ， 1-15。 第二类，16 - 30。（优先级0用来内存管理线程）。Dispatcher从队列选择高优先级进程，若无执行idle进程。
windos api定义了6类进程优先级，默认NORMAL_PRIORITY_CLASS。`SetPriorityClass()`设置优先级。除了REALTIME_PRIORITY_CLASS外，优先级类都可修改，即 variable class。

一个线程优先级由其所属优先级类和相对优先级共同决定。

![windos进程优先级][4]


## 算法评估

// TODO

## 优先级反置

// TODO!




  [1]: https://www.github.com/ChristopherKai/picsRepo/raw/master/%E5%B0%8F%E4%B9%A6%E5%8C%A0/1521633568672.jpg
  [2]: https://www.github.com/ChristopherKai/picsRepo/raw/master/%E5%B0%8F%E4%B9%A6%E5%8C%A0/1522417224584.jpg
  [3]: https://www.github.com/ChristopherKai/picsRepo/raw/master/%E5%B0%8F%E4%B9%A6%E5%8C%A0/1522418549196.jpg
  [4]: https://www.github.com/ChristopherKai/picsRepo/raw/master/%E5%B0%8F%E4%B9%A6%E5%8C%A0/1522420303976.jpg
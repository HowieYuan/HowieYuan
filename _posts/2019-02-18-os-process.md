---
layout:     post                    # 使用的布局（不需要改）
title:      一文带你了解什么是进程              # 标题 
subtitle:   #副标题
date:       2019-02-18              # 时间
author:     Howie                      # 作者
header-img: img/os-process.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 操作系统

---

## 一. 进程和线程
### 进程
我们的操作系统里面会有很多应用，比如手机里面的各种应用，每一个应用都有一个或多个进程，而且每个应用运行时又会用到很多不同的资源。进程就帮助我们隔离了不同的资源，利用各种资源帮助应用管理着各种状态，因此，我们经常说**进程是资源分配的最小单位**。

多进程的目的是为了满足用户的需要，同时对多个资源进行处理，简单来说就是同时做多个事情。

### 线程
进程拥有了资源之后，就需要去利用它们，所以就需要线程的帮忙了，每个进程都至少需要一个线程作为执行单位，同时也可以开启新的线程执行。另外，一个 CPU 核心一个时刻只能运行一个线程，多个线程之间需要不断进行调度，因此我们经常说**线程是CPU调度的最小单位**

有了线程之后，进程间可以并发，而且一个进程内部的多个线程之间也是可以并发的。

而多线程的目的就是为了充分利用并发带来的效率上的提高。


## 二. 进程的状态和转换是怎样的？
对于一个 CPU 核心，一个时刻只能运行一个线程（因此也只能运行一个进程），为了实现并发运行，引入了分时的概念，即将时间分为多个时间片，不同的时间片执行不同的线程，以实现一个时间段内运行多个线程

![图片来源：https://www.jianshu.com/p/d254b138de03](https://user-gold-cdn.xitu.io/2019/2/18/168ff2ea4c2fab82?w=561&h=371&f=png&s=139728)


###  运行状态
进程正在 CPU 上运行，对于 CPU 的单个核心来说，一个时刻最多只能有一个进程处于运行状态

→ 就绪态：当分派给当前进程的时间片用完以后，该进程就必须让出 CPU，变回就绪态等待下一次进程调度分派时间片；或者，如果是可剥夺的操作系统，有更高优先级的进程进入就绪态时，也需要让出 CPU

→ 阻塞态：当进程请求某一资源（如外设）的使用和分配或等待某一事件的发生（如I/O操作的完成）时，它就从运行状态转换为阻塞状态。

###  就绪状态
准备运行，已经获得了出 CPU 以外的所有需要的资源，等待 CPU 分派时间片

→ 运行态：当前进程被分派了 CPU 时间片后，也就获得 CPU 资源（分派处理机时间片），可以使用 CPU 了，因而进入运行态

###  阻塞状态（等待状态）
因为要等待某个资源或者某个事件的发生而被阻塞因此暂停运行。

就绪状态是指进程缺少 CPU 资源；而等待状态是指需要除了 CPU 以外的其他资源或者某个事件

→ 就绪态：当进程等待的事件到来时，如I/O操作结束或中断结束，或者获得了等待的资源，中断处理程序就会把该进程的状态转换为就绪状态



## 三. 进程通信的几种方式
### 1. PV 操作
**一种低级的通信方式**。在操作系统这种多线程，多进程环境中，我们要密切关注资源的同步互斥问题

- 同步：指对共享资源的一系列操作要有顺序性，一致性，要对资源进行有序访问，以保证数据的正确性，先完成这一个操作，才能继续执行下一个操作
- 互斥：指一次只能有一个线程对共享资源进行操作

为了解决共享资源的同步互斥问题，我们将具体的资源的数量抽象为信号量 Semaphore，通过 P 操作和 V 操作分别对信号量进行减和增。

- P 操作通过减少信号量来占用多余的资源，当资源资源不足时，则会阻塞线程。
- V 操作增加信号量释放已经使用完的资源，并恢复正在等待资源的阻塞状态的线程。

如果我们将信号量看作为资源设置的资源锁，那么 P 操作相当于加锁操作，而 V 操作相当于解锁操作 。

---
**随着操作系统的发展，用于进程之间实现通信的机制也在发展，并已由早期的低级进程通信机制发展为能传送大量数据的高级通信工具机制。**

### 2. 共享存储
在通信的进程之间存在一块**可直接访问的共享空间**，通过对这片共享空间进行写/读操作实现进程之间的信息交换。

当然，在对共享空间进行写读操作时，也需要使用同步互斥工具（如 P操作、V操作），对共享空间的写读进行控制。

### 3. 消息传递
通信的进程之间不存在可直接访问的共享空间，则必须利用操作系统提供的消息传递方法实现进程通信。

- 直接通信方式：指发送进程利用操作系统所提供的发送命令，直接把消息发送给目标进程，接收进程直接利用接收命令取得信息。同时，还有一种消息缓冲队列通信机制也是直接通信方式。
- 间接通信方式：发送进程把消息发送到某个中间实体（信箱）中暂存，接收进程从中间实体中取得消息。这种通信方式又称为信箱通信方式。

### 4. 管道通信
利用一个“管道”实现进程间的通信。这个管道是一个连接读进程和写进程的共享文件。

写进程以字符流的方式将数据输入到这个共享文件中，供读进程读取。当然这一系列操作也需要管道提供同步和互斥的能力。


## 四. 进程的同步机制
### 如何实现同步
- 互斥机制：对资源的访问，一个时间段只能有一个进程对其进行访问
- 信号量机制：将资源的数量定义为信号量，通过 pv 操作控制进程对资源的访问
- 管程机制：将共享的资源和对于这些共享资源的操作封装起来，形成一个具有一定接口的功能模块，进程可以调用管程来实现进程级别的并发控制，同时对管程的操作是互斥的

### 同步机制应遵循的规则
- 空闲让进：当没有进程处于临界区的时候，应该许可其他进程进入临界区的申请
- 忙则等待：当前如果有进程处于临界区，如果有其他进程申请进入，则必须等待，保证对临界区的互斥访问
- 有限等待：对要求访问临界资源的进程，需要在有限时间内进入临界区，防止出现死等
- 让权等待：当进程无法进入临界区的时候，需要释放处理机，也就是转换为阻塞状态

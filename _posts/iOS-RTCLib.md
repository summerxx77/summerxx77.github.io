layout: Grand Central Dispatch
title: iOS Grand Central Dispatch (GCD) 探究
date: 2020-01-15 01:18:02
tags: GCD 
---
前言: Dispatch Queue 意为调度队列, GCD就是解决多任务在多线程下的调度问题.

### 1. 种类
队列类型分为两种: Serial Dispatch Queue(串行), Concurrent Dispatch Queue(并行)
 - 1.1.串行队列: 使用一个线程, 没有开辟新线程, 但是可使用多个Serial Dispatch Queue, 来并行执行任务.
 - 1.2 并行队列使用多个线程, 开辟新线程, 并行处理的数量取决于当前系统的状态

<!--more-->
### 2. 创建
- 2.1 并行队列
```
dispatch_queue_t serialQueue = dispatch_queue_create("example.gcd.create.concurrent.summerxx", DISPATCH_QUEUE_CONCURRENT);
```
- 2.2 串行队列
```
 dispatch_queue_t concurrnetQueue = dispatch_queue_create("example.gcd.create.serial.summerxx", DISPATCH_QUEUE_SERIAL);
```

### 3. 系统提供可直接使用的队列(Dispatch Queue)
- 3.1 Main Dispatch Queue 在主线程中执行的 是一个串行队列(Serial Dispatch Queue), 在主线程的Runloop中执行
```
    dispatch_queue_t mainDispatchQueue = dispatch_get_main_queue();
```
   - 3.2Global Dispatch Queue, 并行队列
```
/*
     #define DISPATCH_QUEUE_PRIORITY_HIGH 2
     #define DISPATCH_QUEUE_PRIORITY_DEFAULT 0
     #define DISPATCH_QUEUE_PRIORITY_LOW (-2)
     #define DISPATCH_QUEUE_PRIORITY_BACKGROUND INT16_MIN
     
     */
    dispatch_queue_t globalDispatchQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0);
```
### 4 搭配使用

- 4.1并发队列, 同步执行按顺序执行
```
dispatch_queue_t queue = dispatch_queue_create("example.gcd.create.concurrent.summerxx", DISPATCH_QUEUE_CONCURRENT);

    dispatch_sync(queue, ^{
        NSLog(@"1");
    });

    dispatch_sync(queue, ^{
        NSLog(@"2");
    });

    dispatch_sync(queue, ^
        NSLog(@"3");
    });
    dispatch_sync(queue, ^{
        NSLog(@"4");
    });
2020-05-17 19:41:30.784199+0800 GCD-Example[24148:4813765] 1
2020-05-17 19:41:30.784378+0800 GCD-Example[24148:4813765] 2
2020-05-17 19:41:30.784505+0800 GCD-Example[24148:4813765] 3
2020-05-17 19:41:30.784616+0800 GCD-Example[24148:4813765] 4
```
- 4.2 并发队列, 异步执行顺序不定

```
    dispatch_queue_t queue = dispatch_queue_create("example.gcd.create.concurrent.summerxx", DISPATCH_QUEUE_CONCURRENT);
    
    dispatch_async(queue, ^{
        NSLog(@"1");
    });
    
    dispatch_async(queue, ^{
        NSLog(@"2");
    });
    dispatch_async(queue, ^{
        NSLog(@"3");
    });
    dispatch_async(queue, ^{
        NSLog(@"4");
    });
```
- 4.3 串行队列, 异步执行, 按顺序执行
```
dispatch_queue_t queueSerial = dispatch_queue_create("queue.serial.smx", DISPATCH_QUEUE_SERIAL);

    dispatch_async(queueSerial, ^{
        NSLog(@"1");
    });

    dispatch_async(queueSerial, ^{
        NSLog(@"2");
    });

    dispatch_async(queueSerial, ^{
        NSLog(@"3");
    });

    dispatch_async(queueSerial, ^{
        NSLog(@"4");
    });
2020-05-17 19:50:50.115997+0800 GCD-Example[26545:4828324] 1
2020-05-17 19:50:50.116180+0800 GCD-Example[26545:4828324] 2
2020-05-17 19:50:50.116304+0800 GCD-Example[26545:4828324] 3
2020-05-17 19:50:50.116416+0800 GCD-Example[26545:4828324] 4
```
- 4.4 串行队列, 同步执行, 按顺序执行
```
dispatch_queue_t queueSerial = dispatch_queue_create("queue.serial.smx", DISPATCH_QUEUE_SERIAL);

    dispatch_sync(queueSerial, ^{
        NSLog(@"1");
    });

    dispatch_sync(queueSerial, ^{
        NSLog(@"2");
    });

    dispatch_sync(queueSerial, ^{
        NSLog(@"3");
    });

    dispatch_sync(queueSerial, ^{
        NSLog(@"4");
    });
2020-05-17 19:52:29.842083+0800 GCD-Example[26966:4831721] 1
2020-05-17 19:52:29.842271+0800 GCD-Example[26966:4831721] 2
2020-05-17 19:52:29.842378+0800 GCD-Example[26966:4831721] 3
2020-05-17 19:52:29.842472+0800 GCD-Example[26966:4831721] 4
```
- 4.5 使用系统提供的串行队列进行同步操作
```
dispatch_queue_t mainDispatchQueue = dispatch_get_main_queue();
    dispatch_sync(mainDispatchQueue, ^{
        NSLog(@"1");
    });
```
> 该代码在dispatch_get_main_queue 也就是主线程中执行指定的Block, 而其实在主线程中就在执行这些代码, 造成了队列循环等待的情况, 也就造成了死锁.


未完待续
文/夏天然后

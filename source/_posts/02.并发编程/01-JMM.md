---
title: 01.JMM
date: 2023-07-16 10:21:32
categories: [并发编程]
tags: [JMM,volatile]
---

可见性：
volatile // 内存屏障禁止重排序，保证32位处理器对64位数据处理的原子性，对++这种复杂操作不具备原子性
UnsafeFactory.getUnsafe().storeFence(); // 内存屏障
LockSupport.unpark(Thread.currentThread()); // 内存屏障
sleep(); // 内存屏障



Thread.yield(); // 释放时间片，切换线程上下文，导致本地缓存刷新


while(true) 优先级很高-抢占式 （可能饥饿死锁，Thread.yield()后依然会抢占到）


1. 内存屏障：jvm-storeLoad -> x86-lock替代了mfence
2. 上下文切换






















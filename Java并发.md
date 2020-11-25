# Java并发常见面试题总结
Java并发这一块在Java面试的过程中问的也还是挺多的，有的面试官甚至会问的比较深，所以有时间还是需要好好掌握的

## 1. 创建线程的方式
    这个问题从不同的角度来看有不同的答案,但是从Oracle的官方文档来看只有两种,...
    
## 2. 线程的启动

### 2.1 一个线程两次调用start()方法会发生什么

### 2.2 为什么要用start()方法启动线程而不用run()启动

## 3. 如何停止线程

## 4. 线程的状态转换

## 5. 为什么线程通信的方法wait(),notify()被定义在Object类,而sleep()定义在Thread类里

## 6. wait/notify 与 sleep的异同

## 7. 什么是线程安全

## 8. Synchronized的原理以及和ReentrantLock的区别

## 9. volatile的底层原理
   简单来说，volatile具有两个重要的功能，即保证可见性和禁止重排序。保证可见性，是其在经过JVM编译之后，对应的语句前面会有一个lock指令，
   这个lock指令会使得CPU将缓存写回到主内存中，同时导致其他处理器的缓存失效（在多核处理器下）
   禁止重排序，其实是在volatile语句处加了内存屏障。
   
## 10. 线程池

### 10.1 为什么要用线程池

### 10.2 线程池的七个参数

## 11. 说一说你知道的JUC下面的一些类
   ConcurrentHashMap，CopyOnWriteArrayList
   CountDownLatch，CyclicBarrier，Semaphore
   BlockingQueue
   ReentrantLock
   AtomicInteger,AutomicBoolean
   Executor


## 12. Java并发相关代码

### 12.1 轮流打印奇偶数

### 12.2 手写生产者消费者模型

### 12.3单例模式
     双重检查的好处，不要第一次if会咋样，为什么要volatile



  

# Java并发常见面试题总结
Java并发这一块在Java面试的过程中问的也还是挺多的，有的面试官甚至会问的比较深，所以有时间还是需要好好掌握的



## 说一说你知道的JUC下面的一些类

   ConcurrentHashMap，CopyOnWriteArrayList
   CountDownLatch，CyclicBarrier，Semaphore
   BlockingQueue
   ReentrantLock
   AtomicInteger,AutomicBoolean
   Executor

## Synchronized的原理以及和ReentrantLock的区别

## volatile的底层原理
简单来说，volatile具有两个重要的功能，即保证可见性和禁止重排序。

保证可见性，是其在经过JVM编译之后，对应的语句前面会有一个lock指令，这个lock指令会使得CPU将缓存写回到主内存中，同时导致其他处理器的缓存失效（在多核处理器下）

禁止重排序，其实是在volatile语句处加了内存屏障。

## 轮流打印奇偶数

## 单例模式
 
### 双重检查的好处，不要第一次if会咋样，为什么要volatile

## 手写生产者消费者模型

## wait()和sleep()区别

## 
  

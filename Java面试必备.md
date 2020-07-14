# Java容器

## ArrayList和LinkedList的区别（都是线程不安全的）
1. ArrayList是基于Object数组实现的，而LinkedList是基于双向链表实现的
2. ArrayList支持随机访问，查询速度比LinkedList更快，但进行增加和删除操作的时候，LinkedList不需要向ArrayList一样不断地进行移位操作
3. 内存空间的开销不一样：ArrayList的空间浪费在需要预留一定的容量空间，而LinkedList的空间主要花费在其要存放前驱节点和后继节点的指针信息上

## ArrayList相关知识（基于Java 11）
1. ArrayList继承自AbstractList，初始默认容量为10（private static final int DEFAULT_CAPACITY = 10）
2. ArrayList有两个构造函数，一个不传参默认生成一个初始容量为10的elementData数组，另一个传入一个初始容量参数，小于0会抛出异常
3. ArrayList使用add添加元素的时候会调用grow(size+1)方法来增加容量
```
private Object[] grow(int minCapacity) {
        return elementData = Arrays.copyOf(elementData,newCapacity(minCapacity));
 }
 private int newCapacity(int minCapacity) {
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity <= 0) {
            if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
                return Math.max(DEFAULT_CAPACITY, minCapacity);
            if (minCapacity < 0)
                throw new OutOfMemoryError();
            return minCapacity;
        }
        return (newCapacity - MAX_ARRAY_SIZE <= 0)? newCapacity: hugeCapacity(minCapacity);
 }
```
上述代码中ArrayList的MAX_ARRAY_SIZE为Integer.MAX_VALUE-8;

## HashMap,HashTable,ConcurrentHashMap
### HashMap和HashTable的区别

### HashMap的底层实现
    1.构造方法（4个）
    第一个传入初始容量和负载因子
    ```
    public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " + initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " + loadFactor);
        this.loadFactor = loadFactor;
        this.threshold = tableSizeFor(initialCapacity);
    }
    ```
    为什么要用红黑树，有什么好处，为什么不用其他平衡二叉树，负载因子为什么默认取0.75，
    HashMap为什么是线程不安全的，HashMap的长度为什么要取2的幂次方，
    HashMap是怎么扩容的，HashMap的哈希过程为什么高16位要异或低16位
    HashMap的put源码和get源码
    
### HashMap和ConcurrentHashMap区别

### ConcurrentHashMap1.7和1.8的区别

# Java容器
## ArrayList和LinkedList的区别（都是线程不安全的）
1. ArrayList是基于Object数组实现的，而LinkedList是基于双向链表实现的
2. ArrayList支持随机访问，查询速度比LinkedList更快，但进行增加和删除操作的时候，LinkedList不需要向ArrayList一样不断地进行移位操作
3. 内存空间的开销不一样：ArrayList的空间浪费在需要预留一定的容量空间，而LinkedList的空间主要花费在其要存放前驱节点和后继节点的指针信息上。
## ArrayList相关知识（基于Java 11）
1. ArrayList继承自AbstractList，初始默认容量为10（private static final int DEFAULT_CAPACITY = 10）
2. ArrayList有两个构造函数，一个不传参默认生成一个初始容量为10的elementData数组，另一个传入一个初始容量参数，小于0会抛出异常
3. ArrayList使用add添加元素的时候会调用grow(size+1)方法来增加容量

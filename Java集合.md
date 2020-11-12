# Java集合常见面试题总结

这一块问来问去主要还是ArrayList和HashMap。HashMap这一块源码是必须要懂的。在有的面试官眼里是基础，有的则会是加分项

## 1. ArrayList和LinkedList的区别（都是线程不安全的）
1. ArrayList是基于Object数组实现的，而LinkedList是基于双向链表实现的
2. ArrayList支持随机访问，查询速度比LinkedList更快，但进行增加和删除操作的时候，LinkedList不需要向ArrayList一样不断地进行移位操作
3. 内存空间的开销不一样：ArrayList的空间浪费在需要预留一定的容量空间，而LinkedList的空间主要花费在其要存放前驱节点和后继节点的指针信息上

## 2. ArrayList相关知识（基于Java 11）
1. ArrayList继承自AbstractList，初始默认容量为10（private static final int DEFAULT_CAPACITY = 10）
2. ArrayList有三个构造函数，一个不传参默认生成一个初始容量为10的elementData数组，另一个传入一个初始容量参数，小于0会抛出异常，最后一个是传入一个指定的集合
```
public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
```
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

## 3. HashMap,HashTable,ConcurrentHashMap

### 3.1 HashMap和HashTable的区别
1. HashMap是线程不安全的，而HashTable是线程安全的，原因是其加了synchronized锁。
2. HashMap的初始容量默认为16，每次扩容都是2的幂次方倍，而HashTable初始容量是11，扩容是2n+1。
3. HashMap可以插入null作为键，而HashTable不行，会抛出NullPointerException异常。
4. 在Java1.8之后HashMap在解决哈希冲突时，当链表长度大于8时会将链表转为红黑树，而HashTable不会且因为效率问题基本被弃用了。

### 3.2 [HashMap的底层实现](https://zhuanlan.zhihu.com/p/21673805)
    1.静态常量（常用）
    
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; 
    static final int MAXIMUM_CAPACITY = 1 << 30;
    static final float DEFAULT_LOAD_FACTOR = 0.75f;  //负载因子
    
    2.哈希桶数组中的存储数据结构
    
    这个是HashMap的一个内部类，实现了Map.Entry接口，其构造方法包含四个参数：hash,key,value,next。
    然后这里面还定义了一些其他方法，如getKey(),getValue(),toString(),setValue(),还重写了equals方法。
    static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;
        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
        public final K getKey()        { return key; }
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }
        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }
        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }
        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }
    
    3.构造方法（4个）
      
    第一个传入初始容量和负载因子
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
    第二个传入初始容量
    public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }
    第三个不传入任何值
    public HashMap() {
        this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
    }
    第四个传入一个指定的Map
    public HashMap(Map<? extends K, ? extends V> m) {
        this.loadFactor = DEFAULT_LOAD_FACTOR;
        putMapEntries(m, false);
    }
    
    4.hash源码
    
    static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
    
    5.put源码
    
    public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
    //参数onlyIfAbsent表示是否替换原值,参数evict我们可以忽略它，它主要用来区别通过put添加还是创建时初始化数据的
    final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        //首先判断table是否为空或者长度是否为0，如果是则需要调用resize()扩容
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        //根据hash值计算在哈希桶中的索引，如果当前索引的位置没有节点，直接新建节点添加
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        //如果有节点
        else {
            Node<K,V> e; K k;
            //判断要插入的key是否等于当前索引中的首个元素，如果等于直接覆盖
            if (p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            //判断是否是红黑树，如果是，则在树中直接插入节点
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            //如果不是则开始遍历链表准备插入，如果链表长度大于8则转换成红黑树插入，否则链表插入，若key存在直接覆盖
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash && ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        //插入成功后，判断键值对数量size是否超过了最大容量threshold，超过则扩容
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
    
    6.get源码
    
    public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }
    final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        //首先判断table是否为空，其长度是否大于0，以及当前位置是否为空，如果有一个是则返回null，不是继续判断
        if ((tab = table) != null && (n = tab.length) > 0 && (first = tab[(n - 1) & hash]) != null) {
        //判断当前位置的首节点是否等于key，是则返回，不是则遍历下一节点，然后判断是否为树节点，如果是转为树查找，不是则遍历链表查找。
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            if ((e = first.next) != null) {
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }
 
 ### 3.3 HashMap的扩容机制
 下述代码为Java1.7的源代码
 ```
 void resize(int newCapacity) {   //传入新的容量
      Entry[] oldTable = table;    //引用扩容前的Entry数组
      int oldCapacity = oldTable.length;         
      if (oldCapacity == MAXIMUM_CAPACITY) {  //扩容前的数组大小如果已经达到最大(2^30)了
          threshold = Integer.MAX_VALUE; //修改阈值为int的最大值(2^31-1)，这样以后就不会扩容了
          return;
      }   
     Entry[] newTable = new Entry[newCapacity];  //初始化一个新的Entry数组
     transfer(newTable);                         //将数据转移到新的Entry数组里
     table = newTable;                           //HashMap的table属性引用新的Entry数组
     threshold = (int)(newCapacity * loadFactor);//修改阈值
 }
 ```
 Java1.8对扩容做了哪些优化？
 
    在java1.7版本的时候，扩容的时候每次都需要重新计算hash值，但是因为HashMap使用的是2的幂次方扩展，所以可以发现每次扩容，
    元素的位置要么在原位置，要么在原位置上移动新扩容的大小所处的位置。而元素在哈希桶上的位置是由（length-1）& hash得到的，
    所以并不需要每次都重新计算hash值，只要看之前hash新增的那个比特是1还是0即可。
 
 HashMap扩容为什么要取2的幂次方？
 
    因为只有在length为2的幂次方的时候hash%length==（length-1）& hash，使用位运算符能够大大提高计算效率
 
### 3.4 HashMap为什么是线程不安全的？
 

### 3.5 为什么要用红黑树，有什么好处，为什么不用其他平衡二叉树
 
### 3.6 HashMap的负载因子为什么要取0.75

### 3.7 HashMap的哈希过程为什么高16位要异或低16位
    
### 3.8 HashMap和[ConcurrentHashMap](https://github.com/miraclewk/JavaRookies/edit/master/Java%E9%9B%86%E5%90%88.md)区别

### 3.9 [ConcurrentHashMap1.7和1.8的区别,看过源码吗，怎么实现线程安全的](https://www.jianshu.com/p/e694f1e868ec)

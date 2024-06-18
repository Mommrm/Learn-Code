# Map

基本概念：一种存放键值对的数据结构，其中键是无序唯一，值是可重复无序的


## 实现Map接口的实现类

1. HashMap: 无序，基于哈希表实现，性能较高
2. TreeMap: 有序，基于红黑树实现，键要实现Comparble或提供Comparator
3. LinkedHashMap: 有序，基于哈希表和双向链表来实现
4. Hashtable: 线程安全，类似于HashMap但是为了保证线程安全，性能有所下降
5. ConcurrentHashMap：线程安全，高效并发的HashMap，使用颗粒度更小的锁

## HashMap和Hashtable区别

1. HashMap线程不安全，Hashtable线程安全(使用synchronized锁来实现，效率较低)
2. HashMap支持NULL键或值，但只能有一个Null的键，Hashtable不允许Null值，会抛出NullPointerException
3. HashMap初始容量是16，Hashtable初始容量是11；HashMap扩容是2倍，Hashtable扩容是2n+1

## HashMap和HashSet的区别
HashSet底层是基于HashMap来实现的, 利用HashMap键的唯一性保证HashSet元素的唯一性，值是添加一个PRESENT对象
因此HashSet的特性就是HashMap中键的特性
因此HashSet中允许Null元素，但是只允许有一个Null值元素

## HashMap和TreeMap的区别
1. 底层实现不一样，HashMap是基于哈希表实现，TreeMap是基于红黑树实现
2. TreeMap还实现了NavigableMap接口和SortedMap接口，可以对集合里面的元素进行搜索

## HashMap的底层实现
1. 通过数组+链表的方式实现的
2. JDK1.8之前是每一格数组就是一个链表节点，JDK1.8之后链表默认长度是8，如果数组长度小于64就会先扩容数组，大于64，链表转换成红黑树，减少搜索时间

## ConcurrentHashMap和Hashtable的区别
底层：都是数组加链表的方式

主要是实现线程安全的方式的区别：
ConcurrentHashMap：

JDK1.7的时候是使用分段锁的方式(Segment),每把锁只锁容器中的一部分
JDK1.8后使用Node数组 + 链表 + 红黑树的数据结构来实现，并发控制使用synchronized和CAS来进行操作

Hashtable：
使用的使用同一把synchronized锁，只有一个线程能put元素


## 为什么ConcurrentHashMap不允许Null值作为键或值

因为存在二义性，如果返回的值是null，有两种意思：
一种是根本就没有这个值
一种是值就是null
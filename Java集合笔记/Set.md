# Set

## Set特性
1. 无序
2. 唯一
3. 基础操作api
```java
add()
remove()
contains()
size()
```

## Set实现类

1. HashSet
> 底层实现：哈希表实现，提供快速插入、删除、查找性能
> 元素唯一：重写了hashCode()和equals()方法来确保唯一
> 无序: 不保证元素顺序

2. LinkedHashSet
> 底层实现：哈希表+链表实现
> 适用场景：可以维护元素插入顺序
> 原理：独立维护两个数据结构


3. TreeSet
> 底层实现：基于红黑树，自然排序或自定义比较器排序
> 排序：自动对元素进行排序
> 适用场景：频繁插入、查找、删除，且需要保持元素排序


所有的Set都提供迭代器，用于遍历Set集合的元素
提供交并补的集合运算


## 实现排序

### 排序接口
- Comparable：出自java.lang 有一个compareTo(Object o)
- Comparator：出自java.util 有一个compare(Object object1, Object object2)

重写

# List

## 1. ArrayList和Array的区别？
- ArrayList是动态数组，支持动态扩容和缩容，而Array被创建之后就不能改变长度
- ArrayList支持泛型来确保类型安全，Array不可以
- ArrayList只支持存储对象，Array既支持对象也支持基本数据类型
- ArrayList支持插入、删除、遍历等操作，如add()、remove(),而Array只能按照下标访问
- ArrayList创建时不需要指明大小，而Array需要

## 2. ArrayList和Vector的区别？
- ArrayList是List主要的实现类，底层是使用Object[]来存储的，适用于频繁查找工作，线程不安全
- Vector是List古老的实现类，底层是使用Object[]来存储的，线程安全

## 3. Vector和Stack的区别？
- Vector和Stack都是线程安全的，都使用了Synchronized关键字进行同步处理
- Stack继承于Vector，Stack是一个先进后厨的栈，而Vector是一个列表

## 4. ArrayList可以添加null值吗？
可以存放null值，但是不建议，因为会出现难以维护或空指针异常

## 5. ArrayList插入和删除元素的时间复杂度？
对于插入：
- 头部插入：O(n)
- 尾部插入：未满时O(1)，满时O(n)
- 指定位置：O(n)
对于删除：
- 头部删除：O(n)
- 尾部删除：O(1)
- 指定位置: O(n)

## 6. LinkedList插入和删除元素的时间复杂度？
- 头部插入/删除：O(1)
- 尾部插入/删除：O(1)
- 指定位置插入/删除：O(n),需要移动到指定位置才能修改指针


## 7. LinkedList为什么不能实现RandomAccess接口？
因为RandomAccess接口是用来标记该接口类支持随机访问(通过索引来快速访问)，因为LinkedList底层是数据结构是链表，内存地址是不连续的，只能通过指针遍历来定位。

## 8. ArrayList和LinkedList的区别？
- 线程安全：都是不同步，线程不安全
- 底层数据结构：ArrayList底层是Object[]、LinkedList底层是双向链表
- 删除、插入是否受元素影响：ArrayList受影响，因为插入或删除(除了头尾之外)都会影响元素的位置，移动元素；LinkedList不受影响，因为插入或删除只影响指针，不影响元素位置
- 是否支持随机访问：ArrayList支持，LinkedList不支持
- 内存空间占用：ArrayList在列表List结尾会预留一些容量空间，而LinkedList每个元素都会存放直接前驱和后继的指针和数据

## 9. ArrayList的扩容机制？

三种初始化方式：
```java
// 第一种
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}

// 第二种
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {//初始容量大于0
        //创建initialCapacity大小的数组
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {//初始容量等于0
        //创建空数组
        this.elementData = EMPTY_ELEMENTDATA;
    } else {//初始容量小于0，抛出异常
        throw new IllegalArgumentException("Illegal Capacity: " + initialCapacity);
    }
}

// 第三种
/**
 *构造包含指定collection元素的列表，这些元素利用该集合的迭代器按顺序返回
 *如果指定的集合为null，throws NullPointerException。
 */
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    if ((size = elementData.length) != 0) {
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        // replace with empty array.
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

默认大小是10，当添加元素时，如果容量不足，则扩容为原来的1.5倍，如果扩容后仍然不足，则扩容为原来的2倍。
newCapacity = oldCapacity + (oldCapacity >> 1);
Arrays.copyOf(elementData, newCapacity);
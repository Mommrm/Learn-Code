# Queue

## Queue特性

1. 队列结构，只能操作对头(front)和队尾(rear)

## Queue接口实现类

1. ArrayDeque: 基于数组的双端队列
2. LinkedList： 也可作为队列适用，支持两端操作
3. PriorityQueue：优先级队列，自动排序
4. ConcurrentLinkedQueue：线程安全的队列

## Queue和Deque的区别

Queue是单端队列

Deque是双端队列

## ArrayDeque和LinkedList的区别
共同点：都实现了Deque接口，两者都具有队列的功能

不同点：ArrayDeque是数组加双指针来实现的，而LinkedList是双向链表实现的
ArrayDeque是存在动态扩容，但是均摊插入操作后还是O(1)的复杂度

## PriorityQueue
出队顺序是按照优先级来出队的

## BlockingQueue
阻塞队列，一般是ArrayBlockingQueue和LinkedBlockingQueue


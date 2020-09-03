# SynchronousQueue类分析

## 实现的接口
首先这个类实现了BlockingQueue接口，那么问题来了什么是阻塞队列？

阻塞队列是一个支持“阻塞的插入”和“阻塞的移除”这两个在常规队列之外附加的操作的特殊队列。

1. 阻塞的插入：当队列满时，队列会阻塞插入元素的线程，直到队列有空位

2. 阻塞的移除：当队列空时，获取队列元素的线程会被阻塞，直到队列变为非空。

应用场景：producer & consumer model
阻塞队列不可用（特殊状态：满 or 空）时，4种处理方式

|方法/处理方式|抛异常|返回特殊值|一直阻塞|阻塞超时退出|
|-|-|-|-|-|
| 插入|add(e)|offer(e)|put(e)|offer(e, time, unit)|
| 移除|remove()|poll()|take()|poll(time, unit)|
| 检查元素是否存在 |element()|peek()|不可用|不可用|

* 抛异常的情况：队列满时，如果继续往队列里插入元素，
```java
throw new IllegalStateException("Queue full");
```
* 返回特殊值：插入元素后返回true/false标志。移除时，从队列中取出一个元素并返回，若队列为空返回null
* 一直阻塞：队列满时，put操作被阻塞，生产者线程挂起，直到队列非满或者put线程响应中断退出。队列空时，take操作被阻塞，消费者线程挂起，直到队列非空
* 超时退出：队列满时，如果继续往队列里插入元素，生产者线程会被阻塞一段时间，超过时间后，生产者线程退出

记忆技巧：put 和 take 分别含有首字母 t ，offer 和 poll 都含有字母 o，add 和 remove 是最常见的一对反义词

## 使用的算法
具有条件同步的非阻塞并发数据结构
https://www.cs.rochester.edu/research/synchronization/pseudocode/duals.html
当成短篇论文看吧。

## 类功能概要
A blocking queue in which each insert operation must wait for a corresponding remove operation by another thread, and vice versa. A synchronous queue does not have any internal capacity, not even a capacity of one. You cannot peek at a synchronous queue because an element is only present when you try to remove it; you cannot insert an element (using any method) unless another thread is trying to remove it; you cannot iterate as there is nothing to iterate. The head of the queue is the element that the first queued inserting thread is trying to add to the queue; if there is no such queued thread then no element is available for removal and poll() will return null. For purposes of other Collection methods (for example contains), a SynchronousQueue acts as an empty collection. This queue does not permit null elements.
Synchronous queues are similar to rendezvous channels used in CSP and Ada. They are well suited for handoff designs, in which an object running in one thread must sync up with an object running in another thread in order to hand it some information, event, or task.
一个阻塞队列，每一个 insert 操作必须等待对应的另一个线程的 remove 操作，反之亦然。一个同步的队列**没有任何内部容量**，甚至连为1的容量都没有。
你不能窥视一个同步队列内部，因为只有当你尝试 remove 一个元素时它才呈现；
你不可以 insert 一个元素（使用任何方法）除非另一个线程尝试 remove 它；
你不可以迭代它，因为没有东西可迭代。
队列的头部是第一个排队的插入线程试图添加到队列中的元素；如果没有这样的排队线程，那么没有元素可用于 remove ，并且 poll() 将会返回 null 。
对于其他集合方法的目的（比如 contains），一个 SynchronousQueue 表现得就像是一个空的集合。
这个队列不允许 null 元素。
同步队列类似于 CSP 和 Ada 中的汇合信道。他们非常适合于传递设计，在传递设计中，运行在一个线程中的一个对象必须与运行在另一个线程中一个对象同步，为了传递给它一些信息、事件或任务。

This class supports an optional fairness policy for ordering waiting producer and consumer threads. By default, this ordering is not guaranteed. However, a queue constructed with fairness set to true grants threads access in FIFO order.
这个类支持一个可选的公平策略，用于排序正在等待的生产者和消费者线程。默认地，顺序不能被确切保证。然而，一个公平性设置为true的队列能够保证线程以先进先出的顺序访问。

This class and its iterator implement all of the optional methods of the Collection and Iterator interfaces.
这个队列和它的迭代器实现了 Collection 和 Iterator 接口的所有可选方法。

This class is a member of the Java Collections Framework.
此类是Java集合框架的一员。

## 应用场景
类注释已经说得很明了了，适合于传递设计，可以用来**在线程间安全的交换单个元素**（类似于生活中一手交钱一手交货这种情形，消费者没拿走当前的产品，生产者是不能再给产品的。），比如 生产者-消费者模型 ，当需要保证消费者和生产者的节奏一致时，这个特性就非常有用。





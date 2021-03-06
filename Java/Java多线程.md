# Java多线程

-------

## 一、ThreadLocal 线程变量

## 1.1、作用

> 提供线程内的局部变量，不同的线程之间不会互相干扰，这种变量在线程的生命周期内起作用，减少同一个线程内多个函数或组件之间一些公共变量传递的复杂度。
>
> 总结：
>
> 1. 线程并发：在多线程并发的场景下
> 2. 传递数据：可以通过ThreadLocal在同一线程下，不同组件中传递变量
> 3. 线程隔离：每个线程的变量都是互相独立的，不会互相影响

## 1.2、ThreadLocal与synchronized的区别

​	虽然ThreadLocal模式与synchronized关键字都用于处理多线程并发访问变量的问题，不过两者处理问题的角度和思路不同。

|        | synchronized                                                 | ThreadLocal                                                  |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 原理   | 同步机制采用‘以时间换空间’的方式，只提供了一份变量，让不同的线程排队访问 | ThreadLocal采用以‘以空间换时间’的方式，为每一个线程都提供一份变量的副本，从而时间同步访问互不干扰 |
| 侧重点 | 多个线程之间访问资源的同步                                   | 多线程中让每个线程之间的数据 互相隔离                        |

## 1.3、弱应用和内存泄漏

##### 1.3.1、内存泄漏的相关概念

- Memory overflow：内存溢出，没有足够的内存提供申请者使用。
- Memory leak：内存泄漏是指程序中已动态分配的堆内存由于某种原因程序未释放或无法释放，造成系统内存的浪费，导致程序运行速度减慢甚至系统崩溃等严重后果。内存泄漏的堆积终将导致内存溢出。

##### 1.3.2、引用的相关概念

- 强引用（Strong Reference）：最常见的普通对象引用，只要还有强引用指向一个对象，就能表明对象还活着，垃圾回收器就不会回收这种对象。
- 弱引用（WeakReference）：垃圾回收器一旦发现只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。

##### 1.3.3、使用强、弱引用的假设

- 如果使用强引用：因为threadLocal的Entry强引用了threadLocal，造成threadLocal无法被回收。
- 如果使用弱引用：threadLocal ref引用可以被回收。但是当前线程仍然运行的前提下，也存在强引用链，thread -> threadLocalMap -> entry -> value，value不会被回收，则导致了value内存泄漏。

##### 1.3.4、出现内存泄漏的真实原因

​	以上，发现内存泄漏的发生和threadLocalMap中的key是否使用弱引用是没有关系的。根源是：由于threadLocalMap的生命周期和thread一样长，如果没有手动删除对应的key就会导致内存泄漏。

##### 1.3.5、为什么使用弱引用

​	也就是说使用完threadLocal及时调用remove，无论key是强引用还是弱引用都不会有问题。

​	事实上，在threadLocalMap中的set/getEntry方法中，会对key为null（也即是threadLocal为null）进行判断，如果为null的话，那么是会对value置为null的。

​	这也就意味着使用完threadLocal，当前线程依然运行的前提下，就算忘记调用remove方法，**弱引用比强引用多一层保障**：弱引用的threadLocal会被回收，对应的value在下一次threadLocalMap调用set、get、remove中的任意方法时候会被清除，从而避免内存泄漏。


- 什么是 JAVA 内存模型：Java 内存模型规范了 JVM 如何提供按需禁用缓存和编译优化的方法。具体来说，这些方法包括 volatile、synchronized 和 final 三个关键字，以及六项 Happens-Before 规则
- 导致可见性的原因是缓存，导致有序性的原因是编译优化
- 那解决可见性、有序性最直接的办法就是**按需禁用缓存以及编译优化**

# volatile
- volatile 关键字并不是 Java 语言的特产，古老的 C 语言里也有，它最原始的意义就是禁用 CPU 缓存。

# Happens-Before 规则

- Happens-Before 要表达的是：前面一个操作的结果对后续操作是可见的
- 比较正式的说法是：Happens-Before 约束了编译器的优化行为，虽允许编译器优化，但是要求编译器优化后一定遵守 Happens-Before 规则
- 在 Java 语言里面，Happens-Before 的语义本质上是一种可见性，A Happens-Before B 意味着 A 事件对 B 事件来说是可见的，无论 A 事件和 B 事件是否发生在同一个线程里。例如 A 事件发生在线程 1 上，B 事件发生在线程 2 上，Happens-Before 规则保证线程 2 上也能看到 A 事件的发生。

## 1. 程序的顺序性规则
- 在一个线程中，按照程序顺序，前面的操作 Happens-Before 于后续的任意操作。

## 2. volatile 变量规则
- 对一个 volatile 变量的写操作， Happens-Before 于后续对这个 volatile 变量的读操作。
- 这个是通过禁用处理器缓存实现的

## 3. 传递性
- 这条规则是指如果 A Happens-Before B，且 B Happens-Before C，那么 A Happens-Before C。
![[b1fa541e98c74bc2a033d9ac5ae7fbe1.webp]]
从图中，我们可以看到：“x=42” Happens-Before 写变量 “v=true” ，这是规则 1 的内容；写变量“v=true” Happens-Before 读变量 “v=true”，这是规则 2 的内容 。再根据这个传递性规则，我们得到结果：“x=42” Happens-Before 读变量“v=true”。这意味着什么呢？如果线程 B 读到了“v=true”，那么线程 A 设置的“x=42”对线程 B 是可见的。也就是说，线程 B 能看到 “x == 42”
- 这就是 1.5 版本对 volatile 语义的增强，这个增强意义重大，1.5 版本的并发工具包（java.util.concurrent）就是靠 volatile 语义来搞定可见性的

## 4. 管程中锁的规则
- 对一个 synchronized 的解锁 Happens-Before 于后续对这个锁的加锁。
- 管程是一种通用的同步原语，在 Java 中指的就是 synchronized，synchronized 是 Java 里对管程的实现。

## 5. 线程 start() 规则
- 主线程 A 启动子线程 B 后，子线程 B 能够看到主线程在启动子线程 B 前的操作。
```java

Thread B = new Thread(()->{
  // 主线程调用B.start()之前
  // 所有对共享变量的修改，此处皆可见
  // 此例中，var==77
});
// 此处对共享变量var修改
var = 77;
// 主线程启动子线程
B.start();
```

## 6. 线程 join() 规则
- 这条是关于线程等待的。它是指主线程 A 等待子线程 B 完成（主线程 A 通过调用子线程 B 的 join() 方法实现），当子线程 B 完成后（主线程 A 中 join() 方法返回），主线程能够看到子线程的操作。当然所谓的“看到”，指的是对共享变量的操作。

```java

Thread B = new Thread(()->{
  // 此处对共享变量var修改
  var = 66;
});
// 例如此处对共享变量修改，
// 则这个修改结果对线程B可见
// 主线程启动子线程
B.start();
B.join()
// 子线程所有对共享变量的修改
// 在主线程调用B.join()之后皆可见
// 此例中，var==66
```

## 线程中断规则：
- 对线程 interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生，可以通过 Thread.interrupted()方法检测到是否有中断发生。

## 对象终结规则
- 一个对象的初始化完成(构造函数执行结束)先行发生于它的 finalize()方法的开始。

# final 关键字

- final 修饰变量时，初衷是告诉编译器：这个变量生而不变，可以可劲儿优化。

# Java 内存模型底层怎么实现的？
- 我们知道为了提高程序执行的性能，编译器和执行器(处理器)通常会对指令做一些优化(重排序)。volatile 通过内存屏障实现了防止指令重排的目的。同时 lock 前缀指令相当于一个内存屏障，它会告诉 CPU 和编译器先于这个命令的必须先执行，后于这个命令的必须后执行。内存屏障另一个作用是强制更新一次不同 CPU 的缓存。例如，一个写屏障会把这个屏障前写入的数据刷新到缓存，这样任何试图读取该数据的线程将得到最新值，而不用考虑到底是被哪个 cpu 核心或者哪颗 CPU 执行的。

- 不同硬件实现内存屏障的方式不同，Java 内存模型屏蔽了这种底层硬件平台的差异，由 JVM 来为不同的平台生成相应的机器码。

- Java 内存屏障主要有两类
（1）一类是**强制读取主内存，强制刷新主内存的内存屏障**，叫做 **Load 屏障** 和 **Store 屏障**
（2）另外一类是**禁止指令重排序的内存屏障**，有四个分别叫做**LoadLoad 屏障、StoreStore 屏障、LoadStore 屏障、StoreLoad 屏障**

**Load屏障**：执行读取数据的时候，强制每次都从主内存读取最新的值
**Store屏障**：每次执行修改数据的时候，强制刷新回主内存。

 

**LoadLoad屏障**

序列：load1指令 **LoadLoad屏障** load2指令

作用：在load1指令和[load2指令](https://www.zhihu.com/search?q=load2%E6%8C%87%E4%BB%A4&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A2299667450%7D)之间加上 LoadLoad屏障，**强制先执行load1指令再执行load2指令**；load1指令和load2指令不能进行重排序（LoadLoad屏障的前面load指令**禁止**和屏障后面的load指令进行重排序）。

  

**StoreStore屏障**

序列：store1指令 **StoreStore屏障** store2指令

作用：在store1指令和store2指令之间加上StoreStore屏障，**强制先执行store1指令再执行store2指令**；store1指令不能和store2指令进行重排序（StoreStore屏障的前面的store指令**禁止**和屏障后面的store指令进行重排序）

  

**LoadStore屏障**

序列：load1指令 **LoadStore屏障** store2指令

作用：在load1指令和store2指令之前加上LoadStore屏障，**强制先执行load1指令再执行store2指令**；load1指令和store2执行不能重排序（LoadStore屏障前面的load执行**禁止**和屏障后面的store指令进行重排序）

  

**StoreLoad屏障**

序列：store1指令 **StoreLoad屏障** load2指令

作用：在store1指令和load2指令之间加上StoreLoad屏障，**强制先执行store1指令再执行load2指令**；

store1指令和load2指令执行不能重排序（StoreLoad屏障前面的Store指令**禁止**和屏障后面的**Store/Load**指令进行重排）



- 在每个 volatile 写操作的前面插入一个 StoreStore 屏障。
- 在每个 volatile 写操作的后面插入一个 StoreLoad 屏障。
- 在每个 volatile 读操作的后面插入一个 LoadLoad 屏障。
- 在每个 volatile 读操作的后面插入一个 LoadStore 屏障。

参考文章：
- 双重检查单例模式问题：https://blog.csdn.net/Dream_Weave/article/details/119929645
- volatile 底层原理：https://blog.csdn.net/wuhuayangs/article/details/122126303
- 内存屏障：为何在volatile写 之前加storestore内存屏障即可，不需要 loadstore么？ - 码农小陈的回答 - 知乎 https://www.zhihu.com/question/289712546/answer/2299667450

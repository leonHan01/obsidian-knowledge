url: https://time.geekbang.org/column/article/83087?cid=100023901

并发编程可以总结为三个核心问题：
- 分工：如何高效的拆解任务并分配给线程
    - 例子
        - Executor
        - Future
        - Fork/Join 框架
        - 生产者 - 消费者、Thread-Per-Message、Worker Thread
- 同步：线程之间如何协助
    - 在项目执行过程中，任务之间是有依赖的，一个任务结束后，依赖它的后续后续任务怎么知道可以开工了呢？这个就是靠同步了。
    - 例子
        - CountDownLatch
        - CyclicBarrier
        - Phaser
        - Exchanger
    - 在 Java 并发编程领域，解决协作问题的核心技术是管程
- 互斥：保证同一时刻只允许一个线程访问共享资源
    - 并发程序里还有一部分内容是关于正确性的，用专业术语叫“线程安全”。
    - 并发程序里，当多个线程同时访问同一个共享变量的时候，结果是不确定的
    - 例子
        - 锁：ReadWriteLock、StampedLock
        - synchronized
        - Thread Local
        - final 关键字 
        - Copy-on-write
        - 原子类


![[Pasted image 20220705231717.png]]


[[可见性、原子性和有序性]] 并发编程BUG源头

[[Java内存模型]] Java 解决可见性和有序性的方法

[[锁]] 通过锁解决原子性问题
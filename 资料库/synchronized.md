* 锁是一种通用的技术方案，Java 语言提供的 synchronized 关键字，就是锁的一种实现。synchronized 关键字可以用来修饰方法，也可以用来修饰代码块，它的使用示例基本上都是下面这个样子:

```java

class X {
  // 修饰非静态方法
  synchronized void foo() {
    // 临界区
  }
  // 修饰静态方法
  synchronized static void bar() {
    // 临界区
  }
  // 修饰代码块
  Object obj = new Object()；
  void baz() {
    synchronized(obj) {
      // 临界区
    }
  }
}  
```

* 问题一：synchronized 加锁 lock() 和解锁 unlock() 在哪里呢
	* Java 编译器会在 synchronized 修饰的方法或代码块前后自动加上加锁 lock() 和解锁 unlock()，这样做的好处就是加锁 lock() 和解锁 unlock() 一定是成对出现的

* 问题二：synchronized 里的加锁 lock() 和解锁 unlock() 锁定的对象在哪里呢
	* 当修饰静态方法的时候，锁定的是当前类的 Class 对象，在上面的例子中就是 Class X；
	* 当修饰非静态方法的时候，锁定的是当前实例对象 this。
* 对于上面的例子，synchronized 修饰静态方法相当于:
``` java

class X {
  // 修饰静态方法
  synchronized(X.class) static void bar() {
    // 临界区
  }
}
```

* 修饰非静态方法，相当于：
```java
class X { // 修饰非静态方法 synchronized(this) void foo() { // 临界区 }}
```

>　　如果你向一个变量写值，而这个变量接下来可能会被另一个线程所读取，或者你从一个变量读值，而它的值可能是前面由另一个线程写入的，此时你就必须使用同步。


- **sychronized**

　 Java语言的**关键字**，当它用来修饰一个方法或者一个代码块的时候，能够保证在同一时刻最多只有一个线程执行该段代码，它是在 **软件层面依赖JVM实现同步**。
　　 synchronized 方法或语句的使用提供了对与每个对象相关的隐式监视器锁的访问，但却强制所有锁获取和释放均要出现在一个块结构中：当获取了多个锁时，它们必须以相反的顺序释放，且必须在与所有锁被获取时相同的词法范围内释放所有锁。

> 　　通过在方法声明中加入 synchronized关键字来声明 synchronized 方法。
> 
　　synchronized 方法控制对类成员变量的访问：每个类实例对应一把锁，每个 synchronized 方法都必须获得调用该方法的类实例的锁方能
执行，否则所属线程阻塞，方法一旦执行，就独占该锁，直到从该方法返回时才将锁释放，此后被阻塞的线程方能获得该锁，重新进入可执行
状态。这种机制确保了同一时刻对于每一个类实例，其所有声明为 synchronized 的成员函数中至多只有一个处于可执行状态（因为至多只有一个能够获得该类实例对应的锁），从而有效避免了类成员变量的访问冲突（只要所有可能访问类成员变量的方法均被声明为 synchronized）
> 
> 
　　synchronized 方法的缺陷：若将一个大的方法声明为synchronized 将会大大影响效率，典型地，若将线程类的方法 run() 声明为　synchronized ，由于在线程的整个生命期内它一直在运行，因此将导致它对本类任何 synchronized 方法的调用都永远不会成功。

  　　

 　　解决synchronized 方法的缺陷

　　

> 通过 synchronized关键字来声明synchronized 块。
```
synchronized(lock) {
// 访问或修改被锁保护的共享状态
}
```
> 其中的代码必须获得对象 syncObject （类实例或类）的锁方能执行。由于可以针对任意代码块，且可任意指定上锁的对象，故灵活性较高。 


　　当两个并发线程访问同一个对象中的这个synchronized(this)同步代码块时，一个时间内只能有一个线程得到执行。另一个线程必须等待当前线程执行完这个代码块以后才能执行该代码块。

　　当一个线程访问对象的一个synchronized(this)同步代码块时，另一个线程仍然可以访问该对象中的非synchronized(this)同步代码块。其他线程对对象中所有其它synchronized(this)同步代码块的访问将被阻塞。

　　如果线程进入由线程已经拥有的监控器保护的 synchronized 块，就允许线程继续进行，当线程退出第二个（或者后续） synchronized 块的时候，不释放锁，只有线程退出它进入的监控器保护的第一个 synchronized 块时，才释放锁。

　　在修饰代码块的时候需要一个reference对象作为锁的对象.
　　在修饰方法的时候默认是当前对象作为锁的对象.
　　在修饰类时候默认是当前类的Class对象作为锁的对象.

 - **lock**

　　Lock 接口实现提供了比使用 synchronized 方法和语句可获得的更广泛的锁定操作。此实现允许更灵活的结构，可以具有差别很大的属性，可以支持多个相关的 Condition 对象。在硬件层面依赖特殊的CPU指令实现同步更加灵活。


> 什么是Condition ？
> Condition 接口将 Object 监视器方法（wait、notify 和 notifyAll）分解成截然不同的对象，以便通过将这些对象与任意 Lock 实现组合使用，为每个对象提供多个等待 set（wait-set）。其中，Lock 替代了 synchronized 方法和语句的使用，Condition 替代了 Object 监视器方法的使用。

　　虽然 synchronized 方法和语句的范围机制使得使用监视器锁编程方便了很多，而且还帮助避免了很多涉及到锁的常见编程错误，但有时也需要以更为灵活的方式使用锁。例如，某些遍历并发访问的数据结果的算法要求使用 "hand-over-hand" 或 "chain locking"：获取节点 A 的锁，然后再获取节点 B 的锁，然后释放 A 并获取 C，然后释放 B 并获取 D，依此类推。Lock 接口的实现允许锁在不同的作用范围内获取和释放，并允许以任何顺序获取和释放多个锁，从而支持使用这种技术。

　　随着灵活性的增加，也带来了更多的责任。不使用块结构锁就失去了使用 synchronized 方法和语句时会出现的锁自动释放功能。在大多数情况下，应该使用以下语句：

```
　　　Lock l = ...; //lock接口的实现类对象
     l.lock();
     try {
         // access the resource protected by this lock
     } finally {
         l.unlock();
     }
```
  在java.util.concurrent.locks包中有很多Lock的实现类，常用的有ReentrantLock、ReadWriteLock（实现类ReentrantReadWriteLock）.它们是具体实现类，不是java语言关键字。


 - 

**ReentrantLock**

　　一个可重入的互斥锁 Lock，它具有与使用 synchronized 方法和语句所访问的**隐式监视器锁**相同的一些基本行为和语义，但功能更强大。

　　最典型的代码如下：
　　

```
 class X {
   private final ReentrantLock lock = new ReentrantLock();
   // ...

   public void m() { 
     lock.lock();  // block until condition holds
     try {
       // ... method body
     } finally {
       lock.unlock()
     }
   }
 }
```

> 重入性：指的是同一个线程多次试图获取它所占有的锁，请求会成功。当释放锁的时候，直到重入次数清零，锁才释放完毕。
> 
　　ReentrantLock 的lock机制有2种，**忽略中断锁**和**响应中断锁**，这给我们带来了很大的灵活性。比如：如果A、B 2个线程去竞争锁，A线程得到了锁，B线程等待，但是A线程这个时候实在有太多事情要处理，就是 一直不返回，B线程可能就会等不及了，想中断自己，不再等待这个锁了，转而处理其他事情。这个时候ReentrantLock就提供了2种机制，第一，B线程中断自己（或者别的线程中断它），但是ReentrantLock 不去响应，继续让B线程等待，你再怎么中断，我全当耳边风（synchronized原语就是如此）；第二，B线程中断自己（或者别的线程中断它），ReentrantLock 处理了这个中断，并且不再等待这个锁的到来，完全放弃。
　　

　　ReentrantLock相对于synchronized多了三个高级功能：
　　
　　①等待可中断
　　　　在持有锁的线程长时间不释放锁的时候,等待的线程可以选择放弃等待.
　　　　

```
tryLock(long timeout, TimeUnit unit)
```
　　　②公平锁
　　　　按照申请锁的顺序来一次获得锁称为公平锁.synchronized的是非公平锁,ReentrantLock可以通过构造函数实现公平锁.

```
new RenentrantLock(boolean fair)
```
　　公平锁和非公平锁。这2种机制的意思从字面上也能了解个大概：即对于多线程来说，公平锁会依赖线程进来的顺序，后进来的线程后获得锁。而非公平锁的意思就是后进来的锁也可以和前边等待锁的线程同时竞争锁资源。对于效率来讲，当然是非公平锁效率更高，因为公平锁还要判断是不是线程队列的第一个才会让线程获得锁。
　　
　　③绑定多个Condition
　　　通过多次newCondition可以获得多个Condition对象,可以简单的实现比较复杂的线程同步的功能.通过await(),signal();

 - **synchronized和lock的用法与区别**
　
　synchronized是托管给JVM执行的，而lock是java写的控制锁的代码。
　
　synchronized原始采用的是CPU悲观锁机制，即线程获得的是独占锁。独占锁意味着其他线程只能依靠阻塞来等待线程释放锁。而在CPU转换线程阻塞时会引起线程上下文切换，当有很多线程竞争锁的时候，会引起CPU频繁的上下文切换导致效率很低。　
　
　Lock用的是乐观锁方式。每次不加锁而是假设没有冲突而去完成某项操作，如果因为冲突失败就重试，直到成功为止。

　ReentrantLock必须在finally中释放锁，否则后果很严重，编码角度来说使用synchronized更加简单，不容易遗漏或者出错。

　ReentrantLock提供了可轮询的锁请求，他可以尝试的去取得锁，如果取得成功则继续处理，取得不成功，可以等下次运行的时候处理，所以不容易产生死锁，而synchronized则一旦进入锁请求要么成功，要么一直阻塞，所以更容易产生死锁。

　synchronized的话，锁的范围是整个方法或synchronized块部分；而Lock因为是方法调用，可以跨方法，灵活性更大

　一般情况下都是用synchronized原语实现同步，除非下列情况使用ReentrantLock
　　　
　　　①某个线程在等待一个锁的控制权的这段时间需要中断
　　　②需要分开处理一些wait-notify，ReentrantLock里面的Condition应用，能够控制notify哪个线程
　　　 ③具有公平锁功能，每个到来的线程都将排队等候

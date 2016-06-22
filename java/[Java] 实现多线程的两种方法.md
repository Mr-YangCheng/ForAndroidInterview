
Java提供了两种创建线程方法：

 - 

通过实现Runable接口；

 - 通过继承Thread类本身。


1 .声明实现 Runnable 接口的类，该类然后实现 run 方法。然后可以分配该类的实例，在创建 Thread 时作为一个参数来传递并启动。例如，计算大于某一规定值的质数的线程可以写成：


```
class PrimeRun implements Runnable {
         long minPrime;
         PrimeRun(long minPrime) {
             this.minPrime = minPrime;
         }
 
         public void run() {
             // compute primes larger than minPrime
              . . .
         }
     }
```

然后，下列代码会创建并启动一个线程：

```
 PrimeRun p = new PrimeRun(143);
     new Thread(p).start();
```

２．将类声明为 Thread 的子类。该子类应重写 Thread 类的 run 方法。接下来可以分配并启动该子类的实例。
```
 class PrimeThread extends Thread {
         long minPrime;
         PrimeThread(long minPrime) {
             this.minPrime = minPrime;
         }
 
         public void run() {
             // compute primes larger than minPrime
              . . .
         }
     }
```

然后，下列代码会创建并启动一个线程：

```
PrimeThread p = new PrimeThread(143);
     p.start();
```

　　当 Java 虚拟机启动时，通常都会有单个非守护线程（它通常会调用某个指定类的 main 方法）。Java 虚拟机会继续执行线程，直到下列任一情况出现时为止：
　　

 
 - 

调用了 Runtime 类的 exit 方法，并且安全管理器允许退出操作发生。

 - 非守护线程的所有线程都已停止运行，无论是通过从对 run 方法的调用中返回，还是通过抛出一个传播到 run 方法之外的异常。

３．使用和区别

　　Runable源码：
　　

```
public interface Runnable {
    /**
     * When an object implementing interface <code>Runnable</code> is used
     * to create a thread, starting the thread causes the object's
     * <code>run</code> method to be called in that separately executing
     * thread.
     * <p>
     * The general contract of the method <code>run</code> is that it may
     * take any action whatsoever.
     *
     * @see     java.lang.Thread#run()
     */
    public abstract void run();
}
```
　　Thread 类实现了 Runnable。激活的意思是说某个线程已启动并且尚未停止。此外，Runnable 为非 Thread 子类的类提供了一种激活方式。通过实例化某个 Thread 实例并将自身作为运行目标，就可以运行实现 Runnable 的类而无需创建 Thread 的子类。**大多数情况下，如果只想重写 run() 方法，而不重写其他 Thread 方法**，那么应使用 Runnable 接口。这很重要，因为**除非程序员打算修改或增强类的基本行为，否则不应为该类创建子类**。

　　继承Thread类实现多线程，要求放入多线程中的类不能继承其他类（Java的单继承特性），如果需要请用 Runnable 实现（接口可以多实现并不影响继承其他类）。
　　
　　一个实现Runnable接口的类可以放在多个线程中执行，多个线程可以去执行同一资源；而继承Thread只能实现多个线程分别去处理自己的资源。（通过Runnable创建的多个线程可以由编程人员传入同一个Runnable对象,即执行同一个run方法，而通过Thread创建的多线程它们运行的都是自己的run方法）。

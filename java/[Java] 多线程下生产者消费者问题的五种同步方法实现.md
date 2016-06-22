

 　　生产者消费者模式是通过一个容器来解决生产者和消费者的强耦合问题。
 　　
 　　生产者消费者模式的优点

	- 解耦
	-  支持并发
	-  支持忙闲不均
 　　解决方法可分为两类：　
 　　（1）用信号量和锁机制实现生产者和消费者之间的同步；
 　　
　　　　　- wait() / notify()方法
　　　　　- await() / signal()方法
　　　　　- BlockingQueue阻塞队列方法
　　　　　- Semaphore方法
　　　　　
 　　（2）在生产者和消费者之间建立一个管道。（一般不使用，缓冲区不易控制、数据不易封装和传输）
　　　　
　　　　　- PipedInputStream / PipedOutputStream　



## 1 wait() / notify()方法实现

　　wait() / nofity()方法是Object里面的两个方法（[Object有哪些公用方法？](http://blog.csdn.net/amazing7/article/details/51219376)），所有Object的子类都可以使用这两个方法。
　　
　　wait()方法：　
　　在其他线程调用此对象的 notify() 方法前，导致当前线程等待。当前线程必须拥有此对象监视器。该线程发布对此监视器的所有权并等待，直到其他线程通过调用 notify 方法，或 notifyAll 方法通知在此对象的监视器上等待的线程醒来。然后该线程将等到重新获得对监视器的所有权后才能继续执行。
　　此方法只应由作为此对象监视器的所有者的线程来调用。

　　notify()方法：
　　唤醒在此对象监视器上等待的单个线程。如果所有线程都在此对象上等待，则会选择唤醒其中一个线程。选择是任意性的，并在对实现做出决定时发生。线程通过调用其中一个 wait 方法，在对象的监视器上等待。
直到当前线程放弃此对象上的锁定，才能继续执行被唤醒的线程。被唤醒的线程将以常规方式与在该对象上主动同步的其他所有线程进行竞争；例如，唤醒的线程在作为锁定此对象的下一个线程方面没有可靠的特权或劣势。
　　此方法只应由作为此对象监视器的所有者的线程来调用。通过以下三种方法之一，线程可以成为此对象监视器的所有者：
　　－通过执行此对象的同步实例方法。
　　－通过执行在此对象上进行同步的 synchronized 语句的正文。
　　－对于 Class 类型的对象，可以通过执行该类的同步静态方法。
一次只能有一个线程拥有对象的监视器。

代码示例：

```
public class Test
{
    private static Integer count = 0;
    private final Integer FULL = 5;
    private static String lock = "lock";
 
    public static void main(String[] args) 
    {
        Test t = new Test();
        new Thread(t.new Producer()).start();
        new Thread(t.new Consumer()).start();
        new Thread(t.new Producer()).start();
        new Thread(t.new Consumer()).start();
    }
    class Producer implements Runnable
    {
        @Override
        public void run()
        {
            for (int i = 0; i < 5; i++)
            {
            	try {
					Thread.sleep(1000);
				} catch (InterruptedException e1) {
					// TODO Auto-generated catch block
					e1.printStackTrace();
				}
                synchronized (lock)
                {
                    while (count == FULL)
                    {
                            try {
								lock.wait();
							} catch (InterruptedException e) {
								// TODO Auto-generated catch block
								e.printStackTrace();
							}
                    }
                    count++;
                    System.out.println(Thread.currentThread().getName() + "produce:: " + count);
                    lock.notifyAll();
                }
            }
        }
    }
    class Consumer implements Runnable
    {
        @Override
        public void run()
        {
            for (int i = 0; i < 5; i++)
            {
            	try {
					Thread.sleep(1000);
				} catch (InterruptedException e1) {
					// TODO Auto-generated catch block
					e1.printStackTrace();
				}
                synchronized (lock)
                {
                    while (count == 0)
                    {
                            try {
								lock.wait();
							} catch (InterruptedException e) {
								// TODO Auto-generated catch block
								e.printStackTrace();
							}
                    }
                    count--;
                    System.out.println(Thread.currentThread().getName()+ "consume:: " + count);
                    lock.notifyAll();
                }
            }
        }
    }
}

```
对象的监视器对锁对象的锁定（也就是代码中的lock对象），注意是调用锁对象的wait() / nofity()方法。

运行结果：
![这里写图片描述](http://img.blog.csdn.net/20160423102010734)


##2 await() / signal()方法

　　await()/signal()是对wait()/notify()的改进，功能更加强大，更适用于高级用户，synchronized是托管给JVM执行的，而lock是java写的控制锁的代码。 
　　
　　ReentrantLock（实现lock接口）相对于synchronized多了三个高级功能：　　 
　　①等待可中断 
　　　　在持有锁的线程长时间不释放锁的时候,等待的线程可以选择放弃等待. 　　　　　　　　　　　
> tryLock(long timeout, TimeUnit unit)

　　　②公平锁 
　　　　按照申请锁的顺序来一次获得锁称为公平锁.synchronized的是非公平锁,ReentrantLock可以通过构造函数实现公平锁.

> new RenentrantLock(boolean fair)

　　公平锁和非公平锁。这2种机制的意思从字面上也能了解个大概：即对于多线程来说，公平锁会依赖线程进来的顺序，后进来的线程后获得锁。而非公平锁的意思就是后进来的锁也可以和前边等待锁的线程同时竞争锁资源。对于效率来讲，当然是非公平锁效率更高，因为公平锁还要判断是不是线程队列的第一个才会让线程获得锁。 
　　 
　　③绑定多个Condition 
　　　通过多次newCondition可以获得多个Condition对象,可以简单的实现比较复杂的线程同步的功能.通过await(),signal();

   一般情况下都是用synchronized原语实现同步，除非下列情况使用ReentrantLock 　　　 
　　　①某个线程在等待一个锁的控制权的这段时间需要中断 
　　　②需要分开处理一些wait-notify，ReentrantLock里面的Condition应用，能够控制notify哪个线程 
　　　 ③具有公平锁功能，每个到来的线程都将排队等候
　　　 
更多了解：[线程同步的方法：sychronized、lock、reentrantLock分析](http://blog.csdn.net/amazing7/article/details/51219714)

　　
下面是使用ReentrantLock来实现生产者消费者问题

代码示例：
```
public class Test {
    private static Integer count = 0;//缓冲区
    private final Integer FULL = 5;
    final Lock lock = new ReentrantLock(); //获得可重入锁
    final Condition put = lock.newCondition();
    final Condition get = lock.newCondition();
 
    public static void main(String[] args)  {
    	
        Test t = new Test();
        new Thread(t.new Producer()).start();
        new Thread(t.new Consumer()).start();
        new Thread(t.new Consumer()).start();
        new Thread(t.new Producer()).start();
    }
    class Producer implements Runnable {
        @Override
        public void run() {
            for (int i = 0; i < 5; i++) {
             	try {
					Thread.sleep(1000);
				} catch (InterruptedException e1) {
					// TODO Auto-generated catch block
					e1.printStackTrace();
				}
                lock.lock();
                try {
                    while (count == FULL) {
                        try {
                            put.await();
                        } catch (InterruptedException e) {
                            // TODO Auto-generated catch block
                            e.printStackTrace();
                        }
                    }
                    count++;
                    System.out.println(Thread.currentThread().getName() + "produce:: " + count);
                    get.signal();
                } finally {
                    lock.unlock();
                }
            }
        }
    }
 
    class Consumer implements Runnable {
 
        @Override
        public void run() {
            for (int i = 0; i < 5; i++) {
            	try {
					Thread.sleep(1000);
				} catch (InterruptedException e1) {
					// TODO Auto-generated catch block
					e1.printStackTrace();
				}
                lock.lock();
                try {
                    while (count == 0) {
                        try {
                            get.await();
                        } catch (Exception e) {
                            // TODO: handle exception
                            e.printStackTrace();
                        }
                    }
                    count--;
                    System.out.println(Thread.currentThread().getName()+ "consume:: " + count);
                    put.signal();
                } finally {
                    lock.unlock();
                }
            }
        }
    }
}
```
运行结果：
![这里写图片描述](http://img.blog.csdn.net/20160423101751985)


##3 BlockingQueue阻塞队列方法

　　BlockingQueue 实现主要用于生产者-使用者队列，但它另外还支持 Collection 接口。是线程安全的，所有排队方法都可以使用内部锁或其他形式的并发控制来自动达到它们的目的。
　　BlockingQueue 方法以四种形式出现，对于不能立即满足但可能在将来某一时刻可以满足的操作，这四种形式的处理方式不同：第一种是抛出一个异常，第二种是返回一个特殊值（null 或 false，具体取决于操作），第三种是在操作可以成功前，无限期地阻塞当前线程，第四种是在放弃前只在给定的最大时间限制内阻塞。下表中总结了这些方法：
　　

　　![这里写图片描述](http://img.blog.csdn.net/20160423103531099)
　　主要说说用于阻塞的那个方法
　　
　　put()方法：将指定元素插入此队列中，将等待可用的空间（如果有必要）。

　　take()方法：获取并移除此队列的头部，在指定的等待时间前等待可用的元素（如果有必要）。
　　
代码示例：
```
public class Test {
    private static Integer count = 0;
    final BlockingQueue<Integer> bq = new ArrayBlockingQueue<Integer>(5);//容量为5的阻塞队列
    
  public static void main(String[] args)  {
        Test t = new Test();
        new Thread(t.new Producer()).start();
        new Thread(t.new Consumer()).start();
        new Thread(t.new Consumer()).start();
        new Thread(t.new Producer()).start();
    }
    class Producer implements Runnable {
        @Override
        public void run() {
            for (int i = 0; i < 5; i++) {
                try {
                    Thread.sleep(1000);
                } catch (Exception e) {
                    e.printStackTrace();
                }
                try {
                    bq.put(1);
                    count++;
                    System.out.println(Thread.currentThread().getName() + "produce:: " + count);
                } catch (InterruptedException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
        }
    }
    class Consumer implements Runnable {
 
        @Override
        public void run() {
            for (int i = 0; i < 5; i++) {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e1) {
                    e1.printStackTrace();
                }
                try {
                    bq.take();
                    count--;
                    System.out.println(Thread.currentThread().getName()+ "consume:: " + count);
                } catch (Exception e) {
                    // TODO: handle exception
                    e.printStackTrace();
                }
            }
        }
    }
}
```
运行结果：
![这里写图片描述](http://img.blog.csdn.net/20160423104129979)

　　

## 4 Semaphore方法实现同步

　　信号量（Semaphore）维护了一个许可集。在许可可用前会阻塞每一个 acquire()，然后再获取该许可。每个 release() 添加一个许可，从而可能释放一个正在阻塞的获取者。但是，不使用实际的许可对象，Semaphore 只对可用许可的号码进行计数，并采取相应的行动。
Semaphore 通常用于限制可以访问某些资源（物理或逻辑的）的线程数目。 

　　注意，调用 acquire() 时无法保持同步锁，因为这会阻止将项返回到池中。信号量封装所需的同步，以限制对池的访问，这同维持该池本身一致性所需的同步是分开的。

代码示例：
```
public class Test
{
    int count = 0;
    final Semaphore put = new Semaphore(5);//初始令牌个数
    final Semaphore get = new Semaphore(0);
    final Semaphore mutex = new Semaphore(1);
 
    
    public static void main(String[] args)  {
        Test t = new Test();
        new Thread(t.new Producer()).start();
        new Thread(t.new Consumer()).start();
        new Thread(t.new Consumer()).start();
        new Thread(t.new Producer()).start();
    }
    
    class Producer implements Runnable
    {
        @Override
        public void run()
        {
            for (int i = 0; i < 5; i++)
            {
                try
                {
                    Thread.sleep(1000);
                }
                catch (Exception e)
                {
                    e.printStackTrace();
                }
                try
                {
                    put.acquire();//注意顺序
                    mutex.acquire();
                    count++;
                    System.out.println(Thread.currentThread().getName() + "produce:: " + count);
                }
                catch (Exception e)
                {
                    e.printStackTrace();
                }
                finally
                {
                    mutex.release();
                    get.release();
                }
 
            }
        }
    }
 
    class Consumer implements Runnable
    {
 
        @Override
        public void run()
        {
            for (int i = 0; i < 5; i++)
            {
                try
                {
                    Thread.sleep(1000);
                }
                catch (InterruptedException e1)
                {
                    e1.printStackTrace();
                }
                try
                {
                    get.acquire();//注意顺序
                    mutex.acquire();
                    count--;
                    System.out.println(Thread.currentThread().getName()+ "consume:: " + count);
                }
                catch (Exception e)
                {
                    e.printStackTrace();
                }
                finally
                {
                    mutex.release();
                    put.release();
                }
            }
        }
    }
}
```
运行结果：
 
   ![这里写图片描述](http://img.blog.csdn.net/20160423110117024)

　　注意同步令牌（notFull.acquire()）必须在互斥令牌（mutex.acquire()）前面获得，如果先得到互斥锁再发生等待，会造成死锁。

##5 PipedInputStream / PipedOutputStream

　　这个类位于java.io包中，是解决同步问题的最简单的办法，一个线程将数据写入管道，另一个线程从管道读取数据，这样便构成了一种生产者/消费者的缓冲区编程模式。PipedInputStream/PipedOutputStream只能用于多线程模式，用于单线程下可能会引发死锁。

代码示例：
```
public class Test {
    final PipedInputStream pis = new PipedInputStream();
    final PipedOutputStream pos = new PipedOutputStream();
	public static void main(String[] args) {
	       Test t = new Test();
	       new Thread(t.new Producer()).start();
	       new Thread(t.new Consumer()).start();
	}
  class Producer implements Runnable{

	@Override
	public void run() {
			try {
				pis.connect(pos);
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			try {
				while(true){ //不断的产生数据
					int n = (int)(Math.random()*255);
					System.out.println(Thread.currentThread().getName()+"produce::"+n);
					pos.write(n);
					pos.flush();
				}
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}finally {
				try {
					pis.close();
					pos.close();
				} catch (IOException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
		}
		
	}
  }
  class Consumer implements Runnable{

	@Override
	public void run() {
			int n;
			try {
				while(true){
					n = pis.read();
					System.out.println(Thread.currentThread().getName()+"consume::"+n);
				}
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}finally{
				try {
					pis.close();
					pos.close();
				} catch (IOException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			
		}
	}
  }
}
```
运行结果：
![这里写图片描述](http://img.blog.csdn.net/20160423094138234)

　　从结果上看出也可以实现同步，但一般不使用，因为缓冲区不易控制、数据不易封装和传输。

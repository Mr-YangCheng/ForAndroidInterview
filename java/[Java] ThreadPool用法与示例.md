<table>
<tr>
<td bgcolor=#f4f31a>
<font color=#00aaff size=5 face="微软雅黑">
 1、ThreadPool的优点
 </font>
</td>
</tr>
</table>
##  

 　　在java.util.concurrent包下，提供了一系列与线程池相关的类。合理的使用线程池，可以带来多个好处：
 　　
（1）降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗；
（2）提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行；
（3）提高线程的可管理性。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

　　线程池可以应对突然大爆发量的访问，通过有限个固定线程为大量的操作服务，减少创建和销毁线程所需的时间。

<table>
<tr>
<td bgcolor=#f4f31a>
<font color=#00aaff size=5 face="微软雅黑">
 1、ThreadPool的创建
 </font>
</td>
</tr>
</table>
##  

　　我们一般通过Executors类下的四个成员函数创建相应的线程池：

```
//创建一个定时任务的线程池
ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(4);

//创建单线程的线程池
ExecutorService singleThreadPool = Executors.newSingleThreadExecutor();

//创建缓存线程池（重用先前的线程）
ExecutorService cachedThreadPool = Executors.newCachedThreadPool();

//创建一个带有固定线程的线程池
ExecutorService threadPool = Executors.newFixedThreadPool(4);

```

我们可以从源码看出，上面四个方法会调用ThreadPoolExecutor的构造方法创建线程池。

```
public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
0L, TimeUnit.MILLISECONDS,
new LinkedBlockingQueue<Runnable>());
    }
```

创建一个ThreadPoolExecutor线程池一般需要以下几个参数：

 - corePoolSize（线程池的基本大小）：
 
  　　当提交一个任务到线程池时，线程池会创建一个线程来执行任务，即使其他空闲的基本线程能够执行新任务也会创建线程，等到需要执行的任务数大于线程池基本大小时就不再创建。如果调用了线程池的prestartAllCoreThreads方法，线程池会提前创建并启动所有基本线程。 

 - maximumPoolSize（线程池最大大小）：

　　线程池允许创建的最大线程数。如果队列满了，并且已创建的线程数小于最大线程数，则线程池会再创建新的线程执行任务。值得注意的是如果使用了无界的任务队列这个参数就没什么效果。
 
 - keepAliveTime（线程活动保持时间）：　
 　　
 　　线程池的工作线程空闲后，保持存活的时间。所以如果任务很多，并且每个任务执行的时间比较短，可以调大这个时间，提高线程的利用率。
 - TimeUnit（线程活动保持时间的单位）：
 
 　　可选的单位有天（DAYS），小时（HOURS），分钟（MINUTES），毫秒(MILLISECONDS)等。
 　　
 - workQueue（任务队列）：　

　　用于保存等待执行的任务的阻塞队列。 可以选择以下几个阻塞队列：ArrayBlockingQueue、LinkedBlockingQueue、SynchronousQueue、PriorityBlockingQueue　
　　
 - threadFactory：　
 　　
 　　用于设置创建线程的工厂，可以通过线程工厂给每个创建出来的线程设置更有意义的名字。　
 　　
 - handler（饱和策略）：　
 　　
 　　当队列和线程池都满了，说明线程池处于饱和状态，那么必须采取一种策略处理提交的新任务。这个策略默认情况下是AbortPolicy，表示无法处理新任务时抛出异常。

　　我们尽量优先使用Executors提供的静态方法来创建线程池，如果Executors提供的方法无法满足要求，再自己通过ThreadPoolExecutor类来创建线程池。

<table>
<tr>
<td bgcolor=#f4f31a>
<font color=#00aaff size=5 face="微软雅黑">
 ２、向线程池提交任务
 </font>
</td>
</tr>
</table>
##  
有两种方式：

①　我们可以使用execute提交的任务，但是execute方法没有返回值，所以无法判断任务是否被线程池执行成功。通过以下代码可知execute方法输入的任务是一个Runnable类的实例。

```
threadsPool.execute(new Runnable() {
            @Override
            public void run() {
                // TODO Auto-generated method stub
            }
        });
```

②　使用submit 方法来提交任务，它会返回一个future,那么我们可以通过这个future来判断任务是否执行成功，通过future的get方法来获取返回值，get方法会阻塞住直到任务完成，而使用get(long timeout, TimeUnit unit)方法则会阻塞一段时间后立即返回，这时有可能任务没有执行完。

```
Future<Object> future = executor.submit(harReturnValuetask);
try {
     Object s = future.get();
} catch (InterruptedException e) {
    // 处理中断异常
} catch (ExecutionException e) {
    // 处理无法执行任务异常
} finally {
    // 关闭线程池
    executor.shutdown();
}
```

<table>
<tr>
<td bgcolor=#f4f31a>
<font color=#00aaff size=5 face="微软雅黑">
 ３、线程池的关闭
 </font>
</td>
</tr>
</table>
##  

　　我们可以通过调用线程池（ExecutorService的对象）的shutdown或shutdownNow方法来关闭线程池，它们的原理是遍历线程池中的工作线程，然后逐个调用线程的interrupt方法来中断线程，所以无法响应中断的任务可能永远无法终止。但是它们存在一定的区别，shutdownNow首先将线程池的状态设置成STOP，然后尝试停止所有的正在执行或暂停任务的线程，并返回等待执行任务的列表，而shutdown只是将线程池的状态设置成SHUTDOWN状态，然后中断所有没有正在执行任务的线程。

　　只要调用了这两个关闭方法的其中一个，isShutdown方法就会返回true。当所有的任务都已关闭后,才表示线程池关闭成功，这时调用isTerminaed方法会返回true。至于我们应该调用哪一种方法来关闭线程池，应该由提交到线程池的任务特性决定，通常调用shutdown来关闭线程池，如果任务不一定要执行完，则可以调用shutdownNow。

<table>
<tr>
<td bgcolor=#f4f31a>
<font color=#00aaff size=5 face="微软雅黑">
 ４、线程池的工作流程
 </font>
</td>
</tr>
</table>
##  

　　![这里写图片描述](http://img.blog.csdn.net/20160519124839474)　


从上图我们可以看出，当提交一个新任务到线程池时，线程池的处理流程如下：　

1、首先线程池判断基本线程池是否已满（ corePoolSize ）没满，创建一个工作线程来执行任务。满了，则进入下个流程。　

2、其次线程池判断工作队列是否已满？没满，则将新提交的任务存储在工作队列里。满了，则进入下个流程。　

3、最后线程池判断整个线程池是否已满（< maximumPoolSize ？）？没满，则创建一个新的工作线程来执行任务，满了，则交给饱和策略来处理这个任务。

也就是说，线程池优先要创建出基本线程池大小（corePoolSize）的线程数量，没有达到这个数量时，每次提交新任务都会直接创建一个新线程，当达到了基本线程数量后，又有新任务到达，优先放入等待队列，如果队列满了，才去创建新的线程（不能超过线程池的最大数maxmumPoolSize）。

　　线程池创建线程时，会将线程封装成工作线程Worker，Worker在执行完任务后，还会无限循环获取工作队列里的任务来执行。我们可以从Worker的run方法里看到这点：

```
public void run() {
     try {
           Runnable task = firstTask;
           firstTask = null;
            while (task != null || (task = getTask()) != null) {
                    runTask(task);
                    task = null;
            }
      } finally {
             workerDone(this);
      }
} 
```

<table>
<tr>
<td bgcolor=#f4f31a>
<font color=#00aaff size=5 face="微软雅黑">
 ５、线程池的监控
 </font>
</td>
</tr>
</table>
##  

　　通过继承线程池并重写线程池的beforeExecute，afterExecute和terminated方法，我们可以在任务执行前，执行后和线程池关闭前干一些事情。如监控任务的平均执行时间，最大执行时间和最小执行时间等。这几个方法在线程池里是空方法。

<table>
<tr>
<td bgcolor=#f4f31a>
<font color=#00aaff size=5 face="微软雅黑">
 ６、线程池的例子
 </font>
</td>
</tr>
</table>
##  

示例一：

```
public class DifferentKindsThreadPool {

	public static void main(String[] args) {
//		displayScheduledThreadPool();
//		displaySingleThreadPool();
//		displayCachedThreadPool();
		displayThreadPool();
	}
	
	/**
	 * 创建一个定时任务的线程池
	 */
	public static void displayScheduledThreadPool() {
		ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(4);
		//它可以向固定线程池一样执行任务
		distributeTaskForThreadPool(scheduledThreadPool);
		//这是它的特殊之处，可以定时任务
		scheduledThreadPool.schedule(
				new Runnable() {
					@Override
					public void run() {
						System.out.println("开始执行任务1");
					}
				}, 
				5, 
				TimeUnit.SECONDS);
		//每隔2秒再次重新执行任务
		scheduledThreadPool.scheduleAtFixedRate(
				new Runnable() {
					@Override
					public void run() {
						System.out.println("开始执行任务2");
					}
				}, 
				5, 
				3, 
				TimeUnit.SECONDS);
	}

	/**
	 * 创建只有一个线程的线程池，如果线程终止，
	 * 他将会创建一个新的线程加入到池子中，这
	 * 个线程池会保证池子中始终有一个线程
	 */
	public static void displaySingleThreadPool() {
		ExecutorService singleThreadPool = Executors.newSingleThreadExecutor();
		distributeTaskForThreadPool(singleThreadPool);
	}

	/**
	 * 创建一个可根据需要创建线程的线程池，但是
	 * 当先前创建的线程可得到时就会重用先前的线
	 * 程，如果不存在可得到的线程，一个新的线程
	 * 将被创建并被加入到池子中。60秒没有被用到
	 * 的线程将被终止并从缓存中移除
	 */
	public static void displayCachedThreadPool() {
		ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
		distributeTaskForThreadPool(cachedThreadPool);
	}

	/**
	 * 创建一个带有固定线程的线程池
	 */
	public static void displayThreadPool() {
		// 创建一个带有4个固定线程的线程池
		ExecutorService threadPool = Executors.newFixedThreadPool(4);
		distributeTaskForThreadPool(threadPool);
	}

	/**
	 * 为线程池分配8个任务，使其驱动
	 * @param threadPool
	 */
	public static void distributeTaskForThreadPool(ExecutorService threadPool) {
		// 让线程池驱动8个任务
		for (int i = 1; i <= 8; i++) {
			// 由于内部类里面不能放一个非final的变量，所以我把i的值赋予task
			final int task = i;

			threadPool.execute(new Runnable() {
				@Override
				public void run() {
					System.out.println("我是" + Thread.currentThread().getName()
							+ "，" + "拿到了第" + task + "个任务，我开始执行了");
				}
			});
		}
	}
}

```

示例二：

```
public class BankCount {
    public synchronized void addMoney(int money){//存钱
        System.out.println(Thread.currentThread().getName() + ">存入：" + money);
    }

    public synchronized void getMoney(int money){//取钱
        System.out.println(Thread.currentThread().getName() + ">取钱：" + money);
    }
}
```

```
public class BankTest {
    public static void main(String[] args) {
        final BankCount bankCount = new BankCount();

        ExecutorService executor = Executors.newFixedThreadPool(10);
        executor.execute(new Runnable() {//存钱线程
            @Override
            public void run() {
                int i = 5;
                while(i-- > 0){
                    bankCount.addMoney(200);
                    try {
                        Thread.sleep(500);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        });
        Future<?> future = executor.submit(new Runnable() {//取钱线程
            @Override
            public void run() {
                int i = 5;
                while(i-- > 0){
                    try {
                        Thread.sleep(500);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    bankCount.getMoney(200);
                }
            }
        });
        try {
            Object res = future.get();
            System.out.println(res);
        } catch (InterruptedException e) {
            // 处理中断异常
            e.printStackTrace();
        } catch (ExecutionException e) {
            // 处理无法执行任务异常
            e.printStackTrace();
        }finally{
            // 关闭线程池
            executor.shutdown();
        }
    }
}
```

#ThreadLocal是什么？

　　线程局部变量，访问某个变量的每个线程都有自己的局部变量，它独立于变量的初始化副本。　　
　　
　　它的功用非常简单，就是为每一个使用该变量的线程都提供一个变量值的副本，是每一个线程都可以独立地改变自己的副本，而不会和其它线程的副本冲突。从线程的角度看，就好像每一个线程都完全拥有该变量。　
　　 
　　ThreadLocal 实例通常是类中的 private static 字段，它们希望将状态与某一个线程（例如，用户 ID 或事务 ID）相关联。每个线程都保持对其线程局部变量副本的隐式引用，只要线程是活动的并且 ThreadLocal 实例是可访问的；在线程消失之后，其线程局部实例的所有副本都会被垃圾回收（除非存在对这些副本的其他引用）。　

#ThreadLocal的接口方法

 - protected T initialValue()　：返回此线程局部变量的初始值

　　线程第一次使用 get() 方法访问变量时将调用此方法，但如果线程之前调用了 set(T) 方法，则不会对该线程再调用 initialValue 方法。通常，此方法对每个线程最多调用一次，但如果在调用 get() 后又调用了 remove()，则可能再次调用此方法。
　　该实现返回 null；如果程序员希望线程局部变量具有 null 以外的值，则必须为 ThreadLocal 创建子类，并重写此方法。通常将使用匿名内部类完成此操作。

 - public T get()　：返回此线程局部变量的当前线程的值

　　如果变量没有用于当前线程的值，则先将其初始化为调用 initialValue() 方法返回的值。

 - public void set(T value)　：将此线程局部变量的当前线程副本中的值设置为指定值

　　大部分子类不需要重写此方法，它们只依靠 initialValue() 方法来设置线程局部变量的值。

 - public void remove()　：移除此线程局部变量当前线程的值

　　如果此线程局部变量随后被当前线程 读取，且这期间当前线程没有 设置其值，则将调用其 initialValue() 方法重新初始化其值。这将导致在当前线程多次调用 initialValue 方法。

　如果希望线程局部变量初始化其它值，那么需要自己实现ThreadLocal的子类并重写该方法，通常使用一个内部类对ThreadLocal进行实例化。
　比如下面的例子，SerialNum类为每一个类分配一个序号： 　

```
public class SerialNum {
    // The next serial number to be assigned
    private static int nextSerialNum = 0;

    private static ThreadLocal serialNum = new ThreadLocal() {
        protected synchronized Object initialValue() {
            return new Integer(nextSerialNum++);
        }
    }

    public static int get() {
        return ((Integer) (serialNum.get())).intValue();
    }
}

```

#ThreadLocal的内部实现

　　先看看ThreadLocal类的部分源码：

```
 public class ThreadLocal<T> {
　　　
//省略...

126    protected T initialValue() {
127        return null;
128    }


159    public T get() {
160        Thread t = Thread.currentThread();
           //当前线程为key存入ThreadLocalMap 中
161        ThreadLocalMap map = getMap(t);
162        if (map != null) {
163            ThreadLocalMap.Entry e = map.getEntry(this);
164            if (e != null) {
165                @SuppressWarnings("unchecked")
166                T result = (T)e.value;
167                return result;
168            }
169        }
170        return setInitialValue();
171    }

199    public void set(T value) {
200        Thread t = Thread.currentThread();
201        ThreadLocalMap map = getMap(t);
202        if (map != null)
203            map.set(this, value);
204        else
205            createMap(t, value);
206    }

219     public void remove() {
220         ThreadLocalMap m = getMap(Thread.currentThread());
221         if (m != null)
222             m.remove(this);
223     }


｝
```

　　我们可以很明显的看出ThreadLocal把线程和线程局部变量存在ThreadLocalMap中，而ThreadLocalMap是ThreadLocal的静态类部类，我们来看看ThreadLocalMap 的部分源码：

```
308        static class Entry extends 　　　　　WeakReference<ThreadLocal<?>> {
            
309
310            Object value;
311
312           Entry(ThreadLocal<?> k, Object v) {
313                super(k);
314                value = v;
315            }
316        }
```

　　这个Map的key是ThreadLocal对象的弱引用，当要抛弃掉ThreadLocal对象时，垃圾收集器会忽略这个key的引用而清理掉ThreadLocal对象 。

　　那么到底是ThreadLocal还是Thread持有ThreadLocalMap对象的引用呢？

我们在Thread源码中发现：

```
180     /* ThreadLocal values pertaining to this thread. This map is maintained
181      * by the ThreadLocal class. */
182     ThreadLocal.ThreadLocalMap threadLocals = null;
```

　　ThreadLocalMap变量属于Thread的内部属性,不同的Thread拥有完全不同的ThreadLocalMap变量.　
　　
　　 Thread中的ThreadLocalMap变量的值是在ThreadLocal对象进行set或者get操作时创建的.　
　　 
　　 在创建ThreadLocalMap之前,会首先检查当前Thread中的ThreadLocalMap变量是否已经存在,如果不存在则创建一个；如果已经存在,则使用当前Thread已创建的ThreadLocalMap.　
　　 
#ThreadLocal如何做到线程安全

　　从上面的分析我们可以得出：

　　

 - 因为每个Thread在进行对象访问时,访问的都是各自线程自己的ThreadLocalMap，所以保证了Thread与Thread之间的数据访问隔离。　
 
 
 - 不同的ThreadLocal实例操作同一Thread时，ThreadLocalMap在存储时采用当前ThreadLocal的实例作为key来保证数据访问隔离（上面源码Entry处可以看出）。　

举个例子说明：

```
public class Test
{
	static ThreadLocal<Integer> local=new ThreadLocal<Integer>(){
		protected synchronized Integer initialValue(){  
            return 0;  
        }
	};  
    public static void main( String args[]){
    	 testRun t = new testRun();
    	 new Thread(t).start();
    	 new Thread(t).start();
    }
    public static  class testRun implements Runnable{
	@Override
	public void run() {
		// TODO Auto-generated method stub
		for(int i=5 ;i>0;i--){
			try {  
		        Thread.sleep(1000);  
		    } catch (InterruptedException e) {  
		        e.printStackTrace();  
		    }
			System.out.println(Thread.currentThread().getName()+"::"+local.get());
			
		     int localValue=local.get();  
		     local.set(++localValue);  
		        } 
		}
	}   
   }
```
输出结果为：

```
Thread-0::0
Thread-1::0
Thread-0::1
Thread-1::1
Thread-1::2
Thread-0::2
Thread-1::3
Thread-0::3
Thread-0::4
Thread-1::4
```

#TheadLocal模式与同步机制的区别

 - 同步机制采用了“以时间换空间”的方式,提供一份变量,让不同的线程排队访问.而ThreadLocal采用了“以空间换时间”的方式,为每一个线程都提供一份变量的副本,从而实现同时访问而互不影响。　
 
 - Java中的synchronized是一个保留字,它依靠JVM的锁机制来实现临界区的函数或者变量的访问中的原子性.在同步机制中,通过对象的锁机制保证同一时间只有一个线程访问变量.此时,被用作“锁机制”的变量是多个线程共享的；
 　　而ThreadLocal会为每一个线程维护一个和该线程绑定的变量的副本，从而隔离了多个线程的数据，每一个线程都拥有自己的变量副本，从而也就没有必要对该变量进行同步了。　
 　
 - 同步机制是为了同步多个线程对相同资源的并发访问，是为了多个线程之间进行通信的有效方式。
 　　而ThreadLocal是隔离多个线程的数据共享，从根本上就不在多个线程之间共享资源（变量），这样当然不需要对多个线程进行同步了。
 　　所以，如果你需要进行多个线程之间进行通信，则使用同步机制。如果需要隔离多个线程之间的共享冲突，可以使用ThreadLocal。


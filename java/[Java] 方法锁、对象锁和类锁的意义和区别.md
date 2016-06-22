> 首先的明白java中锁的机制
> 
> synchronized
> 　　在修饰代码块的时候需要一个reference对象作为锁的对象. 
　　在修饰方法的时候默认是当前对象作为锁的对象. 
　　在修饰类时候默认是当前类的Class对象作为锁的对象. 
　　
> [线程同步的方法：sychronized、lock、reentrantLock分析](http://blog.csdn.net/amazing7/article/details/51219714) 

#方法锁（synchronized修饰方法时）

通过在方法声明中加入 synchronized关键字来声明 synchronized 方法。

synchronized 方法控制对类成员变量的访问：
　　每个类实例对应一把锁，每个 synchronized 方法都必须获得调用该方法的类实例的锁方能执行，否则所属线程阻塞，**方法一旦执行，就独占该锁**，直到从该方法返回时才将锁释放，此后被阻塞的线程方能获得该锁，重新进入可执行状态。这种机制确保了同一时刻对于每一个类实例，其所有声明为 synchronized 的成员函数中**至多只有一个**处于可执行状态，从而有效避免了类成员变量的访问冲突。


#　对象锁（synchronized修饰方法或代码块）

　　当一个对象中有synchronized method或synchronized block的时候调用此对象的同步方法或进入其同步区域时，就必须先获得对象锁。如果此对象的对象锁已被其他调用者占用，则需要等待此锁被释放。（方法锁也是对象锁） 　　 　　　
　　
　　java的所有对象都含有1个互斥锁，这个锁由JVM自动获取和释放。线程进入synchronized方法的时候获取该对象的锁，当然如果已经有线程获取了这个对象的锁，那么当前线程会等待；synchronized方法正常返回或者抛异常而终止，JVM会自动释放对象锁。这里也体现了用synchronized来加锁的1个好处，**方法抛异常的时候，锁仍然可以由JVM来自动释放。**　

对象锁的两种形式：

```
public class Test
{
    // 对象锁：形式1(方法锁)
    public synchronized void Method1()
    {
        System.out.println("我是对象锁也是方法锁");
        try
        {
            Thread.sleep(500);
        } catch (InterruptedException e)
        {
            e.printStackTrace();
        }
        
    }
 
    // 对象锁：形式2（代码块形式）
    public void Method2()
    {
        synchronized (this)
        {
            System.out.println("我是对象锁");
            try
            {
                Thread.sleep(500);
            } catch (InterruptedException e)
            {
                e.printStackTrace();
            }
        }
 
    }
 ｝
```

# 类锁(synchronized 修饰静态的方法或代码块)


　　由于一个class不论被实例化多少次，其中的静态方法和静态变量在内存中都**只有一份**。所以，一旦一个静态的方法被申明为synchronized。此类所有的实例化对象在调用此方法，共用同一把锁，我们称之为类锁。 　
　　
　　**对象锁是用来控制实例方法之间的同步，类锁是用来控制静态方法（或静态变量互斥体）之间的同步。**　
　　
　　类锁只是一个概念上的东西，并不是真实存在的，它只是用来帮助我们理解锁定实例方法和静态方法的区别的。　
　　java类可能会有很多个对象，但是只有1个Class对象，也就是说类的不同实例之间共享该类的Class对象。Class对象其实也仅仅是1个java对象，只不过有点特殊而已。由于每个java对象都有1个互斥锁，而类的静态方法是需要Class对象。所以所谓的类锁，不过是Class对象的锁而已。获取类的Class对象有好几种，最简单的就是［类名.class］的方式。

```
public class Test
{
　　 // 类锁：形式1
    public static synchronized void Method1()
    {
        System.out.println(＂我是类锁一号＂);
        try
        {
            Thread.sleep(500);
        } catch (InterruptedException e)
        {
            e.printStackTrace();
        }
       
    }
 
    // 类锁：形式2
    public void Method２()
    {
        synchronized (Test.class)
        {
            System.out.println(＂我是类锁二号＂);
            try
            {
                Thread.sleep(500);
            } catch (InterruptedException e)
            {
                e.printStackTrace();
            }
          
        }
 
    }
｝
```
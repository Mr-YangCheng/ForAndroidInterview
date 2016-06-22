 > 官方文档 http://docs.oracle.com/javase/8/docs/api/

![这里写图片描述](http://img.blog.csdn.net/20160406172807848)
![这里写图片描述](http://img.blog.csdn.net/20160406172424112)

protected  Object	clone() 
          创建并返回此对象的一个副本。

boolean	equals(Object obj) 
          指示其他某个对象是否与此对象“相等”。

protected  void	finalize() 
          当垃圾回收器确定不存在对该对象的更多引用时，由对象的垃圾回收器调用此方法。

 Class 	getClass() 
          返回此 Object 的运行时类。

 int	hashCode() 
          返回该对象的哈希码值。

void	notify() 
          唤醒在此对象监视器上等待的单个线程。

void	notifyAll() 
          唤醒在此对象监视器上等待的所有线程。

 String	toString() 
          返回该对象的字符串表示。

 void	 wait() 
          在其他线程调用此对象的 notify() 方法或 notifyAll() 方法前，导致当前线程等待。

void	wait(long timeout) 
          在其他线程调用此对象的 notify() 方法或 notifyAll() 方法，或者超过指定的时间量前，导致当前线程等待。

 void	wait(long timeout, int nanos) 
          在其他线程调用此对象的 notify() 方法或 notifyAll() 方法，或者其他某个线程中断当前线程，或者已超过某个实际时间量前，导致当前线程等待。
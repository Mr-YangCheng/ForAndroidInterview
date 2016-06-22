> 从JDK1.2版本开始，把对象的引用分为四种级别，从而使程序能更加灵活的控制对象的生命周期。这四种级别由高到低依次为：强引用、软引用、弱引用和虚引用。

 1 . 强引用(StrongReference) 
 
 强引用是使用最普遍的引用。如果一个对象具有强引用，那垃圾回收器绝不会回收它。当内存空间不足，Java虚拟机宁愿抛出OutOfMemoryError错误，使程序异常终止，也不会靠随意回收具有强引用的对象来解决内存不足的问题。如代码String s=”abc”中变量s就是字符串对象”abc”的一个强引用。只要你给强引用对象s赋空值null,该对象就可以被垃圾回收器回收。因为该对象此时不再含有其他强引用。 
 
 2 . 弱引用(WeakReference) 
 
	弱引用与软引用的区别在于：只具有弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象（s），不管当前内存空间足够与否，都会回收它的内存。不过，由于垃圾回收器是一个优先级很低的线程，因此不一定会很快发现那些只具有弱引用的对象，弱引用非常适合存储元数据。另一个使用弱引用的例子是WeakHashMap，它是除HashMap和TreeMap之外，Map接口的另一种实现。WeakHashMap有一个特点：map中的键值(keys)都被封装成弱引用，也就是说一旦强引用被删除，WeakHashMap内部的弱引用就无法阻止该对象被垃圾回收器回收。
	
	如下代码创建弱引用：
	
```
Counter counter = new Counter(); // strong reference - line 1
WeakReference<Counter> weakCounter = new WeakReference<Counter>(counter); //weak reference
counter = null; // now Counter object is eligible for garbage collection
```
 3 . 软引用(SoftReference)
 
	如果一个对象（如 s）只具有软引用，则内存空间足够，垃圾回收器就不会回收它；如果内存空间不足了，就会回收这些对象的内存。只要垃圾回收器没有回收它，该对象就可以被程序使用。软引用可用来实现内存敏感的高速缓存。
	
	可以使用如下代码创建软引用： 
```
Counter prime = new Counter(); // prime holds a strong reference 
SoftReference soft = new SoftReference(prime) ; //soft reference variable has SoftReference to Counter Object created at line 2
 
prime = null; // now Counter object is eligible for garbage collection but only be collected when JVM absolutely needs memory
```


 4 . 虚引用(PhantomReference) 
 
 "虚引用"顾名思义，就是形同虚设，与其他几种引用都不同，虚引用并不会决定对象的生命周期。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收器回收。
虚引用主要用来跟踪对象被垃圾回收器回收的活动。虚引用与软引用和弱引用的一个区别在于：虚引用 **必须** 和引用队列 （ReferenceQueue）联合使用。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之 关联的引用队列中。

如下代码创建虚引用：

```
DigitalCounter digit = new DigitalCounter(); // digit reference variable has strong reference – line 3
PhantomReference phantom = new PhantomReference(digit); // phantom reference to object created at line 3
 
digit = null;
```

 弱引用、软引用**可以和**一个引用队列（ReferenceQueue）联合使用，如果其所引用的对象被垃圾回收，Java虚拟机就会把这个弱引用加入到与之关联的引用队列中。  

在创建任何弱引用、软引用和虚引用的过程中你可以通过如下代码提供引用队列ReferenceQueue。

```
ReferenceQueue refQueue = new ReferenceQueue(); //reference will be stored in this queue for cleanup
DigitalCounter digit = new DigitalCounter();
PhantomReference<DigitalCounter> phantom = new PhantomReference<DigitalCounter>(digit, refQueue);
```
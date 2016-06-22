> 多态
> 
>同一个类的不同表现形态，不同的形态是通过其不同的子类体现 
>java通过将子类对象引用赋值给超类对象变量, 来实现动态方法调用。
>
>[面向对象的三个特征与含义](http://blog.csdn.net/amazing7/article/details/51219687) 

下面看例子：

```
public class A{
            public String name = "父类name";
            public void move(){
                System.out.println("父类move");
            }
}
```

```
public class B extends A{
    public String name = "子类name";
    @Override
    public void move() {
        // TODO Auto-generated method stub
        System.out.println("子类move");
    }  
}
```

```
public class Test {
    public static void main(String[] args) {
        A a = new B();
        a.move();
        }
      }

```

 **类B是类A的子类， A a = new B() 编译时变量和运行时变量不一样，所以多态发生了。**
 

 ①  A a 作为一个引用类型数据，存储在JVM栈的**本地变量表**中。
 ② new B()作为**实例对象数据**存储在堆中
 　　    Ｂ的对象实例数据（接口、方法、field、对象类型等）的地址也存储在堆中
 　　  Ｂ的对象的类型数据（对象实例数据的地址所执行的数据）存储在方法区中，方法区中 对象类型数据 中有一个指向该类方法的**方法表**。

③Java虚拟机规范中并未对引用类型访问具体对象的方式做规定，目前主流的实现方式主要有两种：

　１. 通过句柄访问
    ![这里写图片描述](http://img.blog.csdn.net/20160414163216948) 

　　在这种方式中，JVM堆中会专门有一块区域用来作为句柄池，存储相关句柄所执行的实例数据地址（包括在堆中地址和在方法区中的地址）。这种实现方法由于用句柄表示地址，因此十分**稳定**。

２.通过直接指针访问
![这里写图片描述](http://img.blog.csdn.net/20160414163449343)　

　　通过直接指针访问的方式中，reference中存储的就是对象在堆中的实际地址，在堆中存储的对象信息中包含了在方法区中的相应类型数据。这种方法最大的优势是速度快，在HotSpot虚拟机中用的就是这种方式。

④实现过程

　　首先虚拟机通过reference类型（A的引用）查询java栈中的 本地变量表，得到堆中的 对象类型数据的地址，从而找到方法区中的 对象类型数据（B的对象类型数据） ，然后查询方法表定位到实际类（B类）的方法运行。
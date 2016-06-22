**重写(Override)**

　　重写是子类对父类的允许访问的方法的实现过程进行重新编写！
　　**返回值和形参都不能改变**。即外壳不变，重写内在实现！
　　
　　重写的好处在于**子类可以根据需要，定义特定于自己的行为**。
也就是说子类能够根据需要实现父类的方法。
　　在面向对象原则里，重写意味着可以重写任何现有方法。实例如下： 

```

class Animal{

   public void move(){
      System.out.println("Animal　can move");
  　 }
}

class Pig extends Animal{

   public void move(){
      System.out.println("Pig can Climb tree");
    }
}

public class Test{

   public static void main(String args[]){
      Animal a = new Animal(); 
      Animal b = new Pig (); 

      a.move();// 执行 Animal 类的方法

      b.move();//执行 Pig 类的方法
   }
}
```

以上实例编译运行结果如下：

```
Animal　can move
Pig can Climb tree
```

　　从上面的例子中可以看出，b 申明的是Animal类 的引用，但运行的是Pig类的move方法。
　　这是因为由于**在编译阶段，只是检查参数的引用类型**。
然而在**运行时，Java虚拟机(JVM)指定对象的类型并且运行该对象的方法。**
　　在上面的例子中，之所以能编译成功，是因为Animal类中存在move方法，然而运行时，运行的是特定对象（Pig 对象）的方法。
看下面的例子：


```
class Animal{

   public void move(){
      System.out.println("Animal　can move");
   }
}

class Pig extends Animal{

   public void move(){
      System.out.println("Pig can Climb tree");
   }
   public void bark(){
      System.out.println("Pig can bray");
   }
}

public class Test{

   public static void main(String args[]){
      Animal a = new Animal(); 
      Animal b = new Pig(); 

      a.move();// 执行 Animal 类的方法
      b.move();//执行 Pig 类的方法
      b.bark();
   }
}

```

以上实例编译运行结果会报如下错误：


```
Test.java:30: cannot find symbol
symbol  : method bark()
location: class Animal
                b.bark();
                 ^
```

这是一个编译错误，因为b的引用类型Animal 类中没有bark方法。

**方法的重写规则**　

**参数列表必须完全**与被重写方法的**相同**；

**返回类型**必须完全与被重写方法的返回类型**相同**；

**访问权限不能比父类中被重写的方法的访问权限更高。**例如：如果父类的一个方法被声明为public，那么在子类中重写该方法就不能声明为protected 或 private。

父类的成员方法只能被它的子类重写。

声明为**final的方法不能被重写**。

声明为**static的方法不能被重写，但是能够被再次声明**。

子类和父类在同一个包中，那么子类可以重写父类所有方法，除了声明为private和final的方法。

子类和父类不在同一个包中，那么子类只能够重写父类的声明为public和protected的非final方法。

重写的方法能够抛出任何非强制异常，无论被重写的方法是否抛出异常。

但是，重写的方法不能抛出新的强制性异常，或者比被重写方法声明的更广泛的强制性异常，反之则可以。

**构造方法不能被重写**。

如果不能继承一个方法，则不能重写这个方法( 父类的private方法)。



**重载(Overload)**

　　重载(overloading) 是在同一个类里面，**方法名字相同，而参数不同，返回类型可以相同也可以不同**的多个方法。
每个重载的方法（只能重载构造函数）都必须有一个独一无二的参数类型列表。
　　
　　
重载规则：

被重载的方法**必须改变参数列表**；
被重载的方法**可以**改变返回类型；
被重载的方法**可以**改变访问修饰符；
被重载的方法可以声明新的或更广的检查异常；
方法能够在同一个类中或者**在一个子类**中被重载。


```
public class Test{
 
	public int test(){
		System.out.println("Overload1");
		return 1;
	}
 
	public void test(int a){
		System.out.println("Overload2");
	}	
 
	//以下两个参数类型顺序不同
	public String test(int a,String s){
		return "Overload3" + s;
	}	
 
	public String test(String s,int a){
		return "Overload4"+s;
	}	
 
	public static void main(String[] args){
		Test t = new Test();
		System.out.println(t.test());
		t.test(1);
		System.out.println(t.test(1,"test3"));
		System.out.println(t.test("test4",1));
	}
}

```

**重写与重载之间的区别**

 重载方法：
		
     参数列表      必须修改
     
     返回类型      可以修改 
     
     异常         可以修改 
     
     访问修饰符    可以修改
               
重写方法：

     参数列表      不能改
     
     返回类型      不能改 
     
     异常         可以减少或删除，不能抛出新的或更广的异常
     
     访问修饰符    只能降低限制，不能变成高限制
                      

> 限制由低到高 ： public  、protected 、private


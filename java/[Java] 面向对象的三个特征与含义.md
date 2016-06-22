> 对象
> 是类的一个实例（对象不是找个女朋友），有状态和行为。例如，一条狗是一个对象，它的状态有：颜色、名字、品种；行为有：摇尾巴、叫、吃等。
> 
> 类
> 是一个模板，它描述一类对象的行为和状态。

1 . **封装性**

　　将对象的状态信息尽可能的隐藏在对象内部，只保留有限的接口和方法与外界进行交互，从而避免了外界对对象内部属性的破坏。

　　Java中使用访问控制符来保护对类、变量、方法和构造方法的访问。
　　Java支持4种不同的访问权限。　
　　
　　默认的，也称为default，在同一包内可见，不使用任何修饰符。
　　私有的，以private修饰符指定，在同一类内可见。
　　共有的，以public修饰符指定，对所有类可见。
　　受保护的，以protected修饰符指定，对同一包内的类和所有子类可见。

２. **继承**

　　 java通过继承创建分等级层次的类，可以理解为一个对象从另一个对象获取属性的过程。
　　 
　　 类的继承是单一继承，也就是说，一个子类只能拥有一个父类
　　 下面的做法是不合法的：

```
public class extends Animal, Mammal{} 
```
　　但是我们可以用多继承接口来实现,　如：
　　

```
public class Apple extends Fruit implements Fruit1, Fruit2{}
```


　　继承中最常使用的两个关键字是extends（用于基本类和抽象类）和implements（用于接口）。
　　 
　　 注意：子类拥有父类所有的成员变量，但对于父类private的成员变量却没有访问权限，这保障了父类的封装性。
　　 下面是使用关键字extends实现继承。
　　 

```
public class Animal{
}

public class Mammal extends Animal{
}

public class Reptile extends Animal{
}

public class Dog extends Mammal{
}
```
　　通过使用关键字extends，子类可以继承父类所有的方法和属性，但是无法使用 private(私有) 的方法和属性。
我们通过使用instanceof 操作符能够确定一个对象是另一个对象的一个分类。

```
public class Dog extends Mammal{

   public static void main(String args[]){

      Animal a = new Animal();
      Mammal m = new Mammal();
      Dog d = new Dog();

      System.out.println(m instanceof Animal);
      System.out.println(d instanceof Mammal);
      System.out.println(d instanceof Animal);
   }
}
```
结果如下：

```
true
true
true
```
Implements关键字使用在类继承接口的情况下， 这种情况不能使用关键字extends。

```
public interface Animal {}

public class Mammal implements Animal{
}

public class Dog extends Mammal{
}
```
可以使用 instanceof 运算符来检验Mammal和dog对象是否是Animal类的一个实例。

```
interface Animal{}

class Mammal implements Animal{}

public class Dog extends Mammal{
   public static void main(String args[]){

      Mammal m = new Mammal();
      Dog d = new Dog();

      System.out.println(m instanceof Animal);
      System.out.println(d instanceof Mammal);
      System.out.println(d instanceof Animal);
   }
} 
```
运行结果如下：

```
true
true
true
```

３．**多态**

　　多态是同一个行为具有多个不同表现形式或形态的能力。
　　多态性是**对象多种表现形式的体现**
　　
　　比如：我到宠物店说"请给我一只宠物"，服务员给我小猫、小狗或者蜥蜴都可以，我们就说"宠物"这个对象就具备多态性。　

	例子
```
public interface Vegetarian{}
public class Animal{}
public class Deer extends Animal implements Vegetarian{}
```
因为Deer类具有多重继承，所以它具有多态性。
访问一个对象的唯一方法就是通过引用型变量 (编译时变量)。
引用型变量只能有一种类型，一旦被声明，引用型变量的类型就不能被改变了。
引用型变量不仅能够被重置为其他对象，前提是这些对象没有被声明为final。还可以引用和它类型相同的或者相兼容的对象。它可以声明为类类型或者接口类型。

```
Deer d = new Deer();
Animal a = d;
Vegetarian v = d;
Object o = d;
```
所有的引用型变量d,a,v,o都指向堆中相同的Deer对象。

我们来看下面这个例子：

```
public class Animal {
			public String name = "父类name";
			public void move(){
				System.out.println("父类move");
			}
			public void content(){
				System.out.println("父类content");
			}
}
```

```
public class Bird extends Animal{
	public String name = "子类name";
	@Override
	public void move() {
		// TODO Auto-generated method stub
		System.out.println("子类move");
	}
	 public void content(){
		System.out.println("子类content");
	}
	
}
```

```
public class Test {
	public static void main(String[] args) {
		Animal a = new Animal();
		System.out.println(a.name);
		a.move();
		a.content();
		
		System.out.println("----------------------");
		
		Animal b = new Bird(); //向上转型由系统自动完成
		//编译时变量      运行时变量	
		System.out.println(b.name);
		b.move();
		b.content();
		
		System.out.println("----------------------");
		
		Bird c = new Bird();
		System.out.println(c.name);
		c.move();
		c.content();
	}
}
```
运行结果：

```
父类name
父类move
父类content
----------------------
父类name
子类move
子类content
----------------------
子类name
子类move
子类content

```
说明：Bird类继承Animal并重写了其方法。
　　 因为Animal b = new Bird()，编译时变量和运行时变量不一样，所以多态发生了。可以从最后的运行结果中看出，调用了**父类的成员变量name和子类重写后的两个方法**。
　　 上面继承说了，子类可以调用父类所有非private的方法和属性。因为name是一个String的对象，**与方法不同，对象的域不具有多态性**。通过引用变量来访问其包含的实例变量时，系统总是视图访问它编译时类型所定义的变量，而不是他运行时类型所定义的变量。

 　　那么问题来了，如果我们把Animal的成员变量换成private，那会不会去调用Bird类的成员变量name来打印输出呢？
 　　![这里写图片描述](http://img.blog.csdn.net/20160413153304267)

也就是说　**系统访问的始终是去访问编译时类型所定义的变量**。
　　　
> 　　重写定义：子类对父类的允许访问的方法的实现过程进行重新编写！返回值和形参都不能改变。即外壳不变，核心重写！


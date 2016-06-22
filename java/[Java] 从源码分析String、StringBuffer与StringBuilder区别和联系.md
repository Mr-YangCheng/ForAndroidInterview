

 - String

> public final class **String** extends Object
implements Serializable, Comparable<String>, CharSequence
String 类代表字符串。Java 程序中的所有字符串字面值（如 "abc" ）都作为此类的实例实现。

>字符串是常量；它们的值在创建之后不能更改。字符串缓冲区支持可变的字符串。因为 String **对象是不可变的，所以可以共享**。例如：
>
     String str = "abc"  
等效于：
>
     char data[] = {'a', 'b', 'c'};
     String str = new String(data);
> 
下面给出了一些如何使用字符串的更多示例：
>
     System.out.println("abc");
     String cde = "cde";
     System.out.println("abc" + cde);
     String c = "abc".substring(2,3);
     String d = cde.substring(1, 2);
 
>String 类包括的方法可用于检查序列的单个字符、比较字符串、搜索字符串、提取子字符串、创建字符串副本并将所有字符全部转换为大写或小写。大小写映射基于 Character 类指定的 Unicode 标准版。

>Java 语言提供对字符串串联符号（"+"）以及将其他对象转换为字符串的特殊支持。**字符串串联是通过 StringBuilder**（或 StringBuffer）类及其 **append 方法**实现的。字符串转换是通过 toString 方法实现的，该方法由 Object 类定义，并可被 Java 中的所有类继承。有关字符串串联和转换的更多信息，请参阅 Gosling、Joy 和 Steele 合著的 The Java Language Specification。

>除非另行说明，否则将 null 参数传递给此类中的构造方法或方法将抛出 NullPointerException。

>String 表示一个 UTF-16 格式的字符串，其中的增补字符 由代理项对 表示（有关详细信息，请参阅 Character 类中的 Unicode 字符表示形式）。索引值是指 char 代码单元，因此增补字符在 String 中占用两个位置。

>String 类提供处理 Unicode 代码点（即字符）和 Unicode 代码单元（即 char 值）的方法。

 - StringBuffer

>public final class **StringBuffer** extends Object
implements Serializable, CharSequence

>**线程安全的可变字符序列**。一个类似于 String 的字符串缓冲区，但不能修改。虽然在任意时间点上它都包含某种特定的字符序列，但通过某些方法调用可以改变该序列的长度和内容。

>可将字符串缓冲区安全地用于多个线程。可以在必要时对这些方法进行同步，因此任意特定实例上的所有操作就好像是以串行顺序发生的，该顺序与所涉及的每个线程进行的方法调用顺序一致。

>StringBuffer 上的主要操作是 append 和 insert 方法，可重载这些方法，以接受任意类型的数据。每个方法都能有效地将给定的数据转换成字符串，然后将该字符串的字符追加或插入到字符串缓冲区中。append 方法始终将这些字符添加到缓冲区的末端；而 insert 方法则在指定的点添加字符。

>例如，如果 z 引用一个当前内容为 "start" 的字符串缓冲区对象，则此方法调用 z.append("le") 会使字符串缓冲区包含 "startle"，而 z.insert(4, "le") 将更改字符串缓冲区，使之包含 "starlet"。

>通常，如果 sb 引用 StringBuilder 的一个实例，则 sb.append(x) 和 sb.insert(sb.length(), x) 具有相同的效果。

>当发生与源序列有关的操作（如源序列中的追加或插入操作）时，该类只在执行此操作的字符串缓冲区上而不是在源上实现同步。

>每个字符串缓冲区都有一定的容量。只要字符串缓冲区所包含的字符序列的长度没有超出此容量，就无需分配新的内部缓冲区数组。如果内部缓冲区溢出，则此容量自动增大。从 JDK 5 开始，为该类补充了一个单个线程使用的等价类，即 StringBuilder。与该类相比，通常应该优先使用 StringBuilder 类，因为它支持所有相同的操作，但由于它不执行同步，所以速度更快。

 - StringBuilder

>public final class **StringBuilder** extends Object
implements Serializable, CharSequence

>**一个可变的字符序列**。此类提供一个与 StringBuffer 兼容的 API，但**不保证同步**。该类被设计用作 StringBuffer 的一个简易替换，用在字符串缓冲区被单个线程使用的时候（这种情况很普遍）。如果可能，建议优先采用该类，因为在大多数实现中，它比 StringBuffer 要快。

>在 StringBuilder 上的主要操作是 append 和 insert 方法，可重载这些方法，以接受任意类型的数据。每个方法都能有效地将给定的数据转换成字符串，然后将该字符串的字符追加或插入到字符串生成器中。append 方法始终将这些字符添加到生成器的末端；而 insert 方法则在指定的点添加字符。

>将 StringBuilder 的实例用于多个线程是不安全的。如果需要这样的同步，则建议使用 StringBuffer。

 1 .上String、StringBuffer与StringBuilder的原代码中的部分代码

```
//String   
public final class String  
implements java.io.Serializable, Comparable<String>, CharSequence 
{  
        private final char value[];  
        
         /**
	     * Allocates a new {@code String} so that it represents the sequence of
	     * characters currently contained in the character array argument. The
	     * contents of the character array are copied; subsequent modification of
	     * the character array does not affect the newly created string.
	     *
	     * @param  value
	     *         The initial value of the string
	     */
     
         public String(String original) {  
              this.value = Arrays.copyOf(value, value.length);
              // 把传入构造函数original字符串切分成字符数组并赋给value[];  
         }  
}  
  

  
//StringBuffer   
public final class StringBuffer extends AbstractStringBuilder  
implements java.io.Serializable, CharSequence
{  
      
	 /**
     * Constructs a string buffer initialized to the contents of the
     * specified string. The initial capacity of the string buffer is
     * <code>16</code> plus the length of the string argument.
     *
     * @param   str   the initial contents of the buffer.
     * @exception NullPointerException if <code>str</code> is <code>null</code>
     */
         public StringBuffer(String str) {  
                 super(str.length() + 16); //继承父类的构造器，并创建一个大小为str.length()+16的value[]数组  
                 append(str); //将str切分成字符序列并加入到value[]中  
        }  
	  public synchronized StringBuffer append(String str) {
        super.append(str);
        return this;
    }

}  





//StringBuilder

public final class StringBuilder
    extends AbstractStringBuilder
    implements java.io.Serializable, CharSequence
{
	/**
     * Constructs a string builder initialized to the contents of the
     * specified string. The initial capacity of the string builder is
     * <code>16</code> plus the length of the string argument.
     *
     * @param   str   the initial contents of the buffer.
     * @throws    NullPointerException if <code>str</code> is <code>null</code>
     */
    public StringBuilder(String str) {
        super(str.length() + 16);
        append(str);
    }
	 public StringBuilder append(String str) {
        super.append(str);
        return this;
    }
}




//AbstractStringBuilder

abstract class AbstractStringBuilder implements Appendable, CharSequence {
    /**
     * The value is used for character storage.
     */
    char[] value;
	
	  /**
     * Creates an AbstractStringBuilder of the specified capacity.
     */
    AbstractStringBuilder(int capacity) {
        value = new char[capacity];
    }
	 public AbstractStringBuilder append(String str) {
        if (str == null) str = "null";
        int len = str.length();
        ensureCapacityInternal(count + len);
        str.getChars(0, len, value, count);
        count += len;
        return this;
    }
}
```

2 .我们可以看出在String中构造器传入的字符串对象被切分成字符序列，存放在其私有final的类变量value[]中。
new String("java")使得value[]={'j','a','v','a'}，之后这个String对象中的value[]再也不能改变了，只能读，所以是**线程安全的**。

StringBuffer和StringBuilder都继承了AbstractStringBuilder 这个抽象类，在他们的构造函数中传入字符串，会调用父类AbstractStringBuilder中对应的构造函数和append函数，父类构造函数新建一个长度为（字符串长度+末尾附加16个空元素）的数组然后通过父类append函数把字符串转换成字符数组value[]中（这里的char[] value 没有final，StringBuffer和StringBuilder中的**字符串是可变的**）。以后通过append()方法将新字符串加入value[]末尾。这样也就改变了value[]的内容和大小了。

StringBuffer对方法加了同步锁或者对调用的方法加了同步锁，所以是**线程安全的**。
而**StringBuilder不是线程安全的**。

①

```
String s = "ja" +"va"

```

②

```
String s1="ja";   
                                                                      StringBuffer sb=new StringBuffer("va");
sb.append(s1);
```

③

```
String s1="ja"; 
String s2 = "va"; 
String s = s1 +s2
```


> 在编译阶段就能够确定的字符串常量，完全没有必要创建String或StringBuffer对象。直接使用字符串常量的"+"连接操作效率最高。
>    时间上 ①<③ 
>
   StringBuffer对象的append效率要高于两个String对象的"+"连接操作。
 时间上 ②<③ 
	
>	一般来说 执行时间上从快到慢 StringBuilder、StringBuffer、String
>
> 非多线程操作字符串缓冲区建议使用 StringBuilder。

 - 抽象类和接口

> 抽象类中可以定义一些子类的公共方法，子类只需要增加新的功能，不需要重复写已经存在的方法； 
> 
> 而接口中只是对方法的申明和常量的定义。
> 

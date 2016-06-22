 -   == 是一个运算符。 
	 equals则是string对象的方法。

 - java中 **值类型** 是存储在内存中的**栈**中。
 而**引用类型**在栈中仅仅是存储引用类型变量的地址，而其本身则存储在**堆**中。所以字符串的内容相同，引用地址不一定相同，有可能创建了多个对象。
 
 - ==操作比较的是两个变量的值是否相等，对于引用型变量表示的是两个变量在堆中存储的地址是否相同，即栈中的内容是否相同。
 
 - equals将此字符串与指定的对象比较。当且仅当该参数不为 null，并且是与此对象表示相同字符序列的 String 对象时，结果才为 true。即堆中的内容是否相同。==比较的是2个对象的地址（栈中），而equals比较的是2个对象的内容（堆中）。所以当equals为true时，==不一定为true。

> 下面是String类equals方法源码，它复写了类 Object 中的 equals方法。

```
	public boolean equals(Object anObject) {
965         if (this == anObject) {
966             return true;
967         }
968         if (anObject instanceof String) {
969             String anotherString = (String)anObject;
970             int n = value.length;
971             if (n == anotherString.value.length) {
972                 char v1[] = value;
973                 char v2[] = anotherString.value;
974                 int i = 0;
975                 while (n-- != 0) {
976                     if (v1[i] != v2[i])
977                         return false;
978                     i++;
979                 }
980                 return true;
981             }
982         }
983         return false;
984     }
```
　　上面已经说到equals是比较两个对象的内容，我们可以看到方法中，先是比较两个String对象是否为同一对象，如果是就直接返回true（两个对象为同一对象那他们的内容必然相等）。
　　如果不是同一对象，先确定传入的对象是否是String类型，如果是，则比较两对象的字符序列（String类内部存储是用char[]实现的，可以查看源码了解），遍历过程中只要有一个字符不相同，就返回false，否则返回true。这里**注意**比较次数为第一个String对象的长度n，而不是传入的String对象参数的长度。

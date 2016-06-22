 1 .  在jdk 7 之前，switch 只能支持 byte、short、char、int 这几个基本数据类型和其对应的封装类型。switch后面的括号里面只能放int类型的值，但由于byte，short，char类型，它们会 **自动** 转换为int类型（精精度小的向大的转化），所以它们也支持。
 
      对于精度比int大的类型，long、float、double，不会自动转换成int。要想使用就得加强转如（int）long。

	另外boolean类型不参与转换，任何类型不能转换为boolean型.
	
2 .我们也可以用枚举类型实现switch可传入string参数

```
public enum  En{
	         a,b,c
	     }
	public static void main(String[] args) {
		  En t = En.a; 
		  function(t);
	   }
	public static void function(En type){
		switch (type) {
			case a:
				  System.err.println("a");
				break;
			case b:
				  System.err.println("b");
				break;
			case c:
				  System.err.println("c");
				break;
			default:
				break;
	}
	}
```
运行结果：

![这里写图片描述](http://img.blog.csdn.net/20160406161357320)
 
 3 . jdk7之后java加入了switch对string的支持,就不用枚举来实现啦！

```
 public static void main(String[] args) {
		  String s = "a";
		  switch (s) {
			case "a":
				  System.err.println("a");
				break;
			case "b":
				  System.err.println("b");
				break;
			case "c":
				  System.err.println("c");
				break;
			default:
				break;
		}
	   }
```
运行结果：

![这里写图片描述](http://img.blog.csdn.net/20160406161357320)
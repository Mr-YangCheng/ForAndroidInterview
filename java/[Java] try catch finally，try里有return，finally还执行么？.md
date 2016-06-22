
*Condition 1：*     如果try中**没有异常**且try中**有return**  （执行顺序）

```
try ---- finally --- return
```

*Condition 2：*   如果try中**有异常**并且try中**有return**

```
try----catch---finally--- return
```

总之 finally 永远执行！



*Condition 3：*   try中有异常，try-catch-finally里都没有return ，finally 之后有个return 

```
try----catch---finally
```

try中有异常以后，根据java的异常机制先执行catch后执行finally，此时错误异常已经抛出，程序因异常而终止，所以你的return是不会执行的

*Condition 4：*  当 try和finally中都有return时，finally中的return会覆盖掉其它位置的return（多个return会报unreachable code，编译不会通过）。

*Condition 5：* 当finally中不存在return，而catch中存在return，但finally中要修改catch中return 的变量值时

```
int ret = 0;
try{ 
	throw new Exception();
}
catch(Exception e)
{
	ret = 1;  return ret;
}
finally{
	ret = 2;
} 

```
最后返回值是1，因为return的值在执行finally之前已经确定下来了
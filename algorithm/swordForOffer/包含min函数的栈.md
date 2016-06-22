## 题目描述

> 定义栈的数据结构，请在该类型中实现一个能够得到栈最小元素的min函数。

##输入描述

> 栈结构

##输出描述

> min函数

##题目分析

> 解法一 　　运行时间：33ms　占用内存：528k

```
import java.util.Stack;

public class Solution {

    Stack<Integer> stack = new Stack<Integer>();
    Stack<Integer> min = new Stack<Integer>();
    public void push(int node) {
        stack.push(node);
        if(min.isEmpty()){
            min.push(node);
        }else{
            min.push(min.peek() > node ? node : min.peek());
        }
    }
    
    public void pop() {
        stack.pop();
        min.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int min() {
        return min.peek();
    }
}
```

　　思路：实现两个栈，stack用来存储数据，min用来保存最小值；为了使min栈顶元素一直是当前stack栈中的最小值，要在stack入栈时进行判断，如果入栈值比当前min栈顶值更小，则把该值入为min新的栈顶，否则再**重复入一次min栈顶元素**（为了stack和min同时出栈，虽然出栈值可能不一样，但并不影响min栈顶是当前stack中的最小元素）。
　　在min函数中min.peek()就行了，返回栈顶但不出栈。

> 解法二 　运行时间：33ms 　占用内存：654k　

```
import java.util.Stack;
import java.util.Iterator;
public class Solution {

     Stack<Integer> stack = new Stack<Integer>();
    public void push(int node) {
        stack.push(node);
    }
    
    public void pop() {
        stack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int min() {
        Iterator<Integer> iterator = stack.iterator();
        int min = stack.peek();
        int temp = 0;
        while(iterator.hasNext()){
            temp = iterator.next();
            if(temp<min){
                min = temp;
             }
        }
        return min;
    }
}
```

　　因为stack实现了Collection接口，我们用迭代器遍历所有元素，求出最小值返回。 

> 解法三　运行时间：28ms　占用内存：629k

```
public class Solution {

    Stack<Integer> s = new Stack<Integer>();
    ArrayList<Integer> a = new ArrayList<Integer>();
    public void push(int node) {
        a.add(s.push(node));
    }
    
    public void pop() {
        a.remove(s.pop());
    }
    
    public int top() {
        return s.peek();
    }
    
    public int min() {
        Object[] b = a.toArray();
        Arrays.sort(b);
        return (int)b[0];
    }
}
```

　　把每次入栈元素都存在一个list中，求最小值时把list转换成数组，用Arrays工具类排序（从小到大），然后返回数组第一个元素就是最小值。
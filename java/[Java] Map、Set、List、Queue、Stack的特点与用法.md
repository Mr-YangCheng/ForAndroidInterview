 - Map 

	

> 键映射到值的对象。一个映射不能包含重复的键；每个键最多只能映射到一个值。

>某些映射实现可明确保证其顺序，如 TreeMap 类；另一些映射实现则不保证顺序，如 HashMap 类。

>Map中元素，可以将key序列、value序列单独抽取出来。
使用keySet()抽取key序列，将map中的所有keys生成一个Set。
使用values()抽取value序列，将map中的所有values生成一个Collection。
为什么一个生成Set，一个生成Collection？那是因为，key总是独一无二的，value允许重复。

 - Set

 

>一个不包含重复元素的 collection。更确切地讲，set 不包含满足 e1.equals(e2) 的元素对 e1 和 e2，并且最多包含一个 null 元素。
>
> 不可随机访问包含的元素  
> 
 只能用Iterator实现单向遍历  
> 
> Set 没有同步方法

 - List
 
 

> 可随机访问包含的元素 
 元素是有序的 
 可在任意位置增、删元素 
 不管访问多少次，元素位置不变 
 允许重复元素 
 用Iterator实现单向遍历，也可用ListIterator实现双向遍历 

 - Queue

> 先进先出
> 
> Queue使用时要尽量避免Collection的add()和remove()方法，而是要使用offer()来加入元素，使用poll()来获取并移出元素。它们的优点是通过返回值可以判断成功与否，add()和remove()方法在失败的时候会抛出异常。 如果要使用前端而不移出该元素，使用element()或者peek()方法。
值得注意的是LinkedList类实现了Queue接口，因此我们可以把LinkedList当成Queue来用。

> Queue 实现通常不允许插入 null 元素，尽管某些实现（如 LinkedList）并不禁止插入 null。即使在允许 null 的实现中，也不应该将 null 插入到 Queue 中，因为 null 也用作 poll 方法的一个特殊返回值，表明队列不包含元素。

 - Stack

> 后进先出
> 
> Stack继承自Vector（可增长的对象数组），也是同步的
> 它通过五个操作对类 Vector 进行了扩展 ，允许将向量视为堆栈。它提供了通常的 push 和 pop 操作，以及取堆栈顶点的 peek 方法、测试堆栈是否为空的 empty 方法、在堆栈中查找项并确定到**堆栈顶**距离的 search 方法。

 - 用法
 

> 如果涉及到堆栈、队列等操作，应该考虑用List；

> 对于需要快速插入，删除元素，应该使用LinkedList；

> 如果需要快速随机访问元素，应该使用ArrayList。

> 如果程序在单线程环境中，或者访问仅仅在一个线程中进行，考虑非同步的类，其效率较高
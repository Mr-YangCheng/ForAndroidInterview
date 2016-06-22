## 题目描述

> 输入一个链表，输出该链表中倒数第k个结点。

##输入描述

> 链表

##输出描述

> 倒数第k个结点

##题目分析

节点描述：

```
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}
```

> 解法一  运行时间：35ms  占用内存：503k

```
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
		   if(head==null || k<=0){
               return null;
           }        
           ListNode last = head;
           for(int i=1;i<k;i++){
               last = last.next;
               if(last==null){
                   return null;
               }
           }
        
           while(last.next!=null){
               head = head.next;
               last = last.next;
           }
        
           return head;
    }
}
```

　　①首先判断边界，新建一个节点last，开始指向head节点；　
　　
　　②向后遍历Ｋ－１次使它与head节点相差ｋ个节点（如果链表长度小于ｋ就返回null）；　　
　　
　　③两个节点同时向后遍历，直到last指向最后一个节点为止。

　　④返回现在的head节点就是倒数第ｋ个节点。


> 解法二 　运行时间：34ms　占用内存：629k

```
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
　　　　　if(head==null || k<=0){
               return null;
        }
        
        ListNode p = head;
        ListNode q = head;
        int i = 0;
        for (; p != null; i++) {
            if (i >= k){
                q = q.next;
            }     
            p = p.next;
        }
        return i < k ? null : q;
    }
}
```
　　与解法一是同一种思路，不过只进行一次遍历，事件复杂度O(n)。
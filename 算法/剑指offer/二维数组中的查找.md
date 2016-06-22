# 题目描述

>　　在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。 

##输入描述

> array： 待查找的二维数组
target：查找的数字

## 输出描述

> 查找到返回true，查找不到返回false

## 题目分析

![这里写图片描述](http://img.blog.csdn.net/20160423182044824)

　　根据题意画了个简单的图，首先我们要确定查找开始的位置，因为它是二维的数组，它的下标变化是两个方向的，根据四个边界点来分析。

　　

> Ａ：向下　增　，向右　增

>Ｂ：向左　减　，向下　增

>Ｃ：向上　减　，向右　增

>Ｄ：向左　减　，向上　减


　　可以看出从B 或Ｃ点开始查找刚好可以构建一个二叉查找树。

> 　　二叉查找树（Binary Search Tree），（又：二叉搜索树，二叉排序树）它或者是一棵空树，或者是具有下列性质的二叉树： 若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值； 若它的右子树不空，则右子树上所有结点的值均大于它的根结点的值； 它的左、右子树也分别为二叉排序树。

 　　先确定二维数组的行数和列数，把 查找值 与 二叉查找树的根节点（Ｂ或者　Ｃ）开始比较，如果相等返回true，小于查找左子树，大于就查找右子树。如果遍历超过数组边界，就返回 false。

> 以C为根节点查找

```
public class Solution {
    public boolean Find(int [][] array,int target) {
	
        int i = array.length -1;
        int m = array[0].length -1;
        int j = 0;
        
        while(i>=0 && j<=m){
            if(target == array[i][j]){
                return true;
            }else if(target >array[i][j]){
                j++;
            }else{
				i--;
            }
        }
        
        return false;
    }
}
```

> 以B为根节点查找

```
public class Solution {
    public boolean Find(int [][] array,int target) {
	
        int i = array.length -1;
        int j = array[0].length -1;
        int n = 0;
        
        while(j>=0 && n<=i){
            if(target == array[n][j]){
                return true;
            }else if(target >array[n][j]){
                n++;
            }else{
				j--;
            }
        }
        
        return false;
    }
}
```
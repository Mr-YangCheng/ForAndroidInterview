## 题目描述

> 输入两颗二叉树A，B，判断B是不是A的子结构。

##输入描述

> 两颗二叉树A，B

##输出描述

> boolean值

##题目分析

树节点声明：

```
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;
    public TreeNode(int val) {
        this.val = val;
    }
}
``` 

> 解法  　运行时间：30ms 　占用内存：629k

```
public class Solution {
　　　//判断root1为根节点的树 是否包含 root2为根的树
    public boolean HasSubtree(TreeNode root1,TreeNode root2) {
    
    　　//如果其中有一个节点为空就返回false
        if (root1 == null || root2 == null) return false;
       
    // 如果root2为根节点的树 是　root1为根的树的子树，返回true;否则依次遍历左右子节点再做判断
　　　return　isSubtree(root1,root2)||
　　　　　　　　　HasSubtree(root1.left,root2)||
　　　　　　　　　　HasSubtree(root1.right,root2);
        
    }　
    //判断root2为根节点的树 是否为 root1为根的树的子树
    public boolean  isSubtree(TreeNode root1, TreeNode root2) {
        if (root2 == null) return true;
        if (root1 == null) return false;
        
        if (root2.val == root1.val) {
        //如果根节点相同分别遍历左右子树，看是否相同
　　　　　　　 return isSubtree(root1.left, root2.left)&& 　              　　　　　　　　　　　　isSubtree(root1.right, root2.right);
        }else {
            return false;
        }
    }
}
```

首先需要递归root1树，找到与root2根一样的节点；

找到相同的根节点后，要判断是否子树（及左右节点都相等），都相等返回true，否则返回false;
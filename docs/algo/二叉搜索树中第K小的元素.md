# 230 二叉搜索树中第K小的元素
## 时间
[[2020-12-28]]
## 题目
[NO.230](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/description/)
给定一个二叉搜索树，编写一个函数 kthSmallest 来查找其中第 k 个最小的元素。

说明：
你可以假设 k 总是有效的，1 ≤ k ≤ 二叉搜索树元素个数。

```
示例 1:

输入: root = [3,1,4,null,2], k = 1
   3
  / \
 1   4
  \
   2
输出: 1
示例 2:

输入: root = [5,3,6,2,4,null,null,1], k = 3
       5
      / \
     3   6
    / \
   2   4
  /
 1
输出: 3
```
进阶：
如果二叉搜索树经常被修改（插入/删除操作）并且你需要频繁地查找第 k 小的值，你将如何优化 kthSmallest 函数？
## 相关概念
[[中序遍历]]
## 思路
递归[[中序遍历]]，提前终止递归调用
## code
```java
class Solution {
    public int res = 0;
    public int rank = 0;
    public int kthSmallest(TreeNode root, int k) {
        tranverse(root, k);
        return res;
    }

    public void tranverse(TreeNode root, int k){
        if(root == null) return;
        tranverse(root.left, k);
        rank ++;
        if(k == rank){
            res = root.val;
            return;
        }
        tranverse(root.right, k);
    }
}
```
## 坑点
### 保持k不变，中序遍历逐个找， rank从0递加至k
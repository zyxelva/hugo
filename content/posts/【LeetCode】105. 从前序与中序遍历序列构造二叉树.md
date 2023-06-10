---
title: 【LeetCode】105. 从前序与中序遍历序列构造二叉树
tags:
  - 二叉树
  - 数据结构
  - LeetCode
  - 算法
  - Java
  - DFS
index_img: /img/LeetCode.jpg
categories:
  - - 算法
    - 二叉树
  - - 算法
    - DFS
  - - LeetCode
abbrlink: 1660581363
date: 2023-05-07 10:10:08
---

# 1.问题
给定两个整数数组 preorder 和 inorder ，其中 preorder 是二叉树的先序遍历， inorder 是同一棵树的中序遍历，请构造二叉树并返回其根节点。
## 示例 1
![](https://img-blog.csdnimg.cn/img_convert/27dd53a0ee432768036d5aa0547dc126.jpeg#pic_center)

> 输入: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7] 
> 输出: [3,9,20,null,null,15,7]

示例 2:

> 输入: preorder = [-1], inorder = [-1] 
> 输出: [-1]

 

**提示**:

- 1 <= preorder.length <= 3000
- inorder.length == preorder.length
- -3000 <= preorder[i], inorder[i] <= 3000
- preorder 和 inorder 均 无重复 元素
- inorder 均出现在 preorder
- preorder 保证 为二叉树的前序遍历序列
- inorder 保证 为二叉树的中序遍历序列
# 2.解题思路
以前序序列[1,2,5,4,6,7,3,8,9]，中序序列[5,2,6,4,7,4,3,8,9]为例，先观察各自序列的特征，如下图所示：
![](https://img-blog.csdnimg.cn/img_convert/18c55fed555c47d2f76dbbed87d615bc.png#pic_center)
令index为根节点root在中序序列中的索引位置，则root.left在前序序列的范围为：[preStart+1, preStart+index-inStart]，在中序序列的范围为：[inStart, index-1]；而root.right在前序中的范围为：[preStart+index-inStart+1, preEnd] ，在中序序列的范围为：[index+1, inEnd].
![](https://img-blog.csdnimg.cn/img_convert/4dbe56291c5c4e7cc2a12cb042025497.png#pic_center)
而在算法开始前，需要遍历中序序列，存储各个节点在中序序列的索引，方便后续根节点定位以及偏移量的计算。
因而，利用递归思想，就可以构造出所需的二叉树。

**复杂度分析**

- 时间复杂度：O(n)，其中 n 是树中的节点个数。

- 空间复杂度：O(n)，除去返回的答案需要的 O(n) 空间之外，还需要使用 O(n) 的空间存储哈希映射，以及 O(h)（其中 h 是树的高度）的空间表示递归时栈空间。这里 h<n，所以总空间复杂度为 O(n)。
# 3.代码
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    Map<Integer, Integer> valueIndexMap=new HashMap<>();
    public TreeNode buildTree(int[] preorder, int[] inorder) {

        //记录中序中 各元素值与索引的映射关系
        for(int i=0;i<inorder.length;i++){
            valueIndexMap.put(inorder[i], i);
        }
        return build(preorder, 0, preorder.length-1, inorder, 0, inorder.length-1);
    }

    private TreeNode build(int[] preorder, int preStart, int preEnd, int[] inorder, int inStart, int inEnd){
        if(inStart>inEnd || preStart>preEnd){
            return null;
        }
        //根节点
        TreeNode root=new TreeNode(preorder[preStart]);
        //根节点在中序中的位置
        int rootIndex=valueIndexMap.get(root.val);

        //偏移量
        int offset=rootIndex-inStart;
        //左子树
        root.left=build(preorder, preStart+1, preStart+offset, inorder, inStart, rootIndex-1);
        //右子树
        root.right=build(preorder, preStart+offset+1, preEnd, inorder, rootIndex+1, inEnd);
        return root;
    }
}
```

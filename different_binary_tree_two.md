# [不同的二叉搜索树](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/submissions/)


**备注：**

```
什么是二叉搜索树(BST-Binary Search Tree)
二叉查找树（叉搜索树，二叉排序树）它或者是一棵空树，或者是具有下列性质的二叉树： 若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值； 若它的右子树不空，则右子树上所有结点的值均大于它的根结点的值； 它的左、右子树也分别为二叉排序树。
```


**题目：**
给定一个整数 n，生成所有由 1 ... n 为节点所组成的二叉搜索树。

**示例：**
![Alt text](./pictures/1561190216815.png)



**思路:**
深度优先搜索：如果要生成从1到n这n个数所构成的所有BST，按照其根节点的数值可以分为n种类型：即根节点的值为从1...n。
假设我们要生成根节点为k (1 <= k <= n)的所有BST，那么首先需要生成范围为1到k-1的所有BST作为左子树，以及范围为k+1到n的所有BST作为右子树，（要注意[1,k-1]范围内的构建的搜素子树也可能有多种形态，[k+1,n]同理，所以需要左右子树不同形态进行组合）然后两两组合(使用双重for循环)就可以形成根节点为k的所有BST（注意思考这里面为什么可以保证没有重复的？）。

```cpp
class Solution {
public:
    vector<TreeNode*> generateTrees(int n) {
        vector<TreeNode *> res;
        if(!n) return res;
        helper(res,1,n);
        return res;
    }
    
    void helper(vector<TreeNode *> &res,int start,int end)
    {
        if(start>end)
        {
            res.push_back(NULL);
            return;
        }
        for(int i=start;i<=end;++i)//遍历根节点，递归生成其左右子树
        {
            vector<TreeNode *> left;
            vector<TreeNode *> right;
            helper(left,start,i-1);
            helper(right,i+1,end);
            //组合所有形态的左右子树
            for(auto l:left)
                for(auto r:right)
                {
                    TreeNode * node = new TreeNode(i);
                    node->left = l;
                    node->right = r;
                    res.push_back(node);
                }
            }
    }
};
```
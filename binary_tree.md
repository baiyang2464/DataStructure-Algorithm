#### 二叉树定义

二叉树是每个结点最多有两个子树的树结构。通常子树被称作“左子树”（left subtree）和“右子树”（right subtree）。
+ 完全二叉树

  若设二叉树的高度为h，除第 h 层外，其它各层 (1～h-1) 的结点数都达到最大个数，第h层有叶子结点，并且叶子结点都是从左到右依次排布，这就是完全二叉树。

+ 满二叉树

  除了叶结点外每一个结点都有左右子叶且叶子结点都处在最底层的二叉树。

+ 平衡二叉树

  平衡二叉树又被称为AVL树（区别于AVL算法），它是一棵二叉排序树，且具有以下性质：它是一棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。

+ 二叉搜索树

  或者是一棵空树，或者是具有下列性质的二叉树： 若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值； 若它的右子树不空，则右子树上所有结点的值均大于它的根结点的值

#### 二叉树的三种非递归遍历

前序
```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        pre(res,root);
        return res;
    }
    void pre(vector<int> &res,TreeNode *node)
    {
        stack<TreeNode *> s;
        while(!s.empty() ||node)
        {
            while(node)
            {
                res.push_back(node->val);//这里访问
                s.push(node);
                node = node->left;
            }
            node = s.top();s.pop();
            node = node->right;
        }
    }
};
```
中序
```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        mid(res,root);
        return res;
    }
    void mid(vector<int> &res,TreeNode *node)
    {
        stack<TreeNode *> s;
        while(!s.empty() ||node)
        {
            while(node)
            {
                s.push(node);//这里访问
                node = node->left;
            }
            node = s.top();s.pop();
            res.push_back(node->val);
            node = node->right;
        }
    }
};
```
后续
``` cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> res;
        if(!root) return res;
        stack<TreeNode *> s;
        TreeNode * cur = root;
        TreeNode * last= NULL;                  //last标记上一次访问的结点
        while(!s.empty()||cur)
        {
            while(cur)
            {
                s.push(cur);
                cur = cur->left;
            }
            cur = s.top();
            if(!cur->right || last==cur->right)//若当前结点没有右孩子，
            {                                  //或右孩子已经访问过，则访问当前结点
                res.push_back(cur->val);
                last = cur;
                s.pop();cur = NULL;            //将cur置为空，免得进入11行的循环
            }
            else cur = cur->right;             //否则访问右子树
        }
        return res;
    }
};
```

#### [验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

**题目**

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

节点的左子树只包含小于当前节点的数。

节点的右子树只包含大于当前节点的数。

所有左子树和右子树自身必须也是二叉搜索树。



示例 1:
```
输入:
    2
   / \
  1   3
输出: true
```
示例 2:
```
输入:
    5
   / \
  1   4
     / \
    3   6
输出: false
解释: 输入为: [5,1,4,null,null,3,6]。
根节点的值为 5 ，但是其右子节点值为 4 。
```

你写的代码，利用BST中序遍历输出一个递增序列的特点，让last指向上一次上文的结点，p为当前访问的结点，只需要验证每次last->val 小于 p->val 即可
```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        //二叉搜索树中序遍历时得到一个递增序列
        //TreeNode * last =NULL;
        bool flag = true;
        TreeNode * last = NULL;
        midOrder(root,last,flag);
        return flag;
        
    }
    void midOrder(TreeNode* p,TreeNode* &last,bool &flag)
    {
        if(p && flag)
        {
            midOrder(p->left,last,flag);
            if(!last &&p->left==NULL)
            {
                last = p;//第一个被访问的结点
                cout<<last->val<<endl;
            }
            else if(last)
            {
                if(p->val <=last->val) flag = false;
                last = p;
            }
            midOrder(p->right,last,flag);
        }
    }
};
```

执行时间12ms的代码，大体思路一致，看下别人怎么控制指针的

```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        //二叉搜索树中序遍历时得到一个递增序列
        //TreeNode * last =NULL;
        bool flag = true;
        TreeNode * last = NULL;
        midOrder(root,last,flag);
        return flag;
        
    }
    void midOrder(TreeNode* p,TreeNode* &last,bool &flag)
    {
        if(p && flag)
        {
            midOrder(p->left,last,flag);
            if(last && p->val <=last->val) flag =false;
            last = p;
            midOrder(p->right,last,flag);
        }
    }
};
```


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

#### 二叉树的高级遍历方式

Morris算法遍历二叉树实质是利用二叉树中大量未使用得指针构建线索进行回溯

注：二叉树有n个结点，每个结点有2个指针，共有n-1个结点被指针指向，则有2n-(n-1)=n+1个空闲指针

Morirs算法遍历过程中会有2次接触同一结点的机会，第一次是通过原有结点之间的指针连接，第二次是通过左子树的回溯线索。因此相当于用时间换取空间。

**切记：Morris算法每个结点有两次接触的机会， 在第一次就访问结点，就是前序遍历，在第二次访问结点就是中序遍历**

**1.中序遍历**

**分析**

中序遍历的基本顺序为leftTree， root，rightTree。这里，抓住中序遍历的本质：要想访问root节点，必须先访问其leftTree。但如果不借助stack，在访问了leftTree之后，又如何能再次访问root呢？可以找到leftTree最后访问的结点，对其进行线索化，然后可通过线索回溯至root结点。

<p align="center">
	<img src=https://images2015.cnblogs.com/blog/1107295/201704/1107295-20170418153624196-1120151240.png alt="Sample"  width="500">
	<p align="center">
		<em>二叉树</em>
	</p>
</p>

从图中可以看出，我们可以在leftTree中找到root节点在中序遍历下的前驱节点pre，将该前驱节点pre的右指针指向root节点，那么，下次在访问完leftTree之后，便能通过前驱节点pre回到root节点。

大体意思每次去找当前节点的左子树的最右子节点，最右子节点的右节点指向当前根节点，然后以左子树为根节点，再找其左子树的最右子节点，最右子节点再指向当前的根节点，这是建立线索的过程，回溯的过程要记住“第二次时候做判断”，详见代码

```c++
void inOrderTraverse(TreeNode *root){//Morris
  TreeNode *cur = root;
  TreeNode *pre = nullptr;
  while(cur){
    if(cur->left){
      pre = cur->left;				//找左子树的最右结点
      while(pre->right!=NULL && pre->right != cur){
        pre = pre->right;
      }
      if(pre->right==NULL){			//第一次接触
        pre->right = cur;			//建立线索
        cur = cur->left;			//根结点指针移动到左子树，找左子树的左子树的线索
      }else{						//第二次接触
        pre->right = nullptr;		//销毁线索，恢复原来的二叉树结构
        visit(cur);
        cur = cur->right;			//访问右子树
      }
    }else{							//如果cur左子树为空，则可直接访问
      visit(cur);	
      cur = cur->right;
    }
  }
}
```

**2.前序遍历**

前序遍历的思路和中序遍历的思路完全相似，只是访问时机的不同。在前序遍历中，root节点需要先访问到，然后再访问其leftTree和rightTree。

```c++
void preOrderTraverse(TreeNode *root){//Morris
  TreeNode *cur = root;
  TreeNode *pre = nullptr;
  while(cur){
    if(cur->left){
      pre = cur->left;
      while(pre->right && pre->right != cur){
        pre = pre->right;
      }
      if(!pre->right){					//first touch
        pre->right = cur;
        visit(cur);						//visit root at first touch
        cur = cur->left;
      }else{
        pre->right = nullptr;
        cur = cur->right;
      }
    }else{
      visit(cur);						//visit root
      cur = cur->right;
    }
  }
}
```

**3.后序遍历**



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

#### [恢复二叉搜索树](https://leetcode-cn.com/problems/recover-binary-search-tree/)

**题目**

二叉搜索树中的两个节点被错误地交换。

请在不改变其结构的情况下，恢复这棵树。

示例 1:
```
输入: [1,3,null,null,2]

   1
  /
 3
  \
   2

输出: [3,1,null,null,2]

   3
  /
 1
  \
   2
```
示例 2:
```
输入: [3,1,4,null,null,2]

  3
 / \
1   4
   /
  2

输出: [2,1,4,null,null,3]

  2
 / \
1   4
   /
  3
```

**解题思路**

比如：一个BST的中序遍历应该是递增的，那么假如一个被错误交换两结点的BST树的中序输出序列是1,2,`6`,4,5,`3`,7，只需要在遍历过程重找出这两个错误结点，进行交换即可。

怎么发现位置错误的结点：last是上一次访问的结点，则`last->val > cur->val`就是位置错误，一般来说发现两次就可找出两个位置错误的结点，最后交换它们的值就可。

但是像1,`3`,`2`,4这样两个挨在一起的就能发现一次，这种情况是写代码的难点。

最开始你写的代码

```cpp
class Solution {
public:
    void recoverTree(TreeNode* root) {
        TreeNode * first = NULL,*last =NULL;
        dfs(root,first,last);
    }
    void dfs(TreeNode * node,TreeNode * &first,TreeNode * &last,TreeNode * &)
    {
        if(node)
        {
            dfs(node->left,first,last);
            if(first && last->val > node->val)
            {
                int tmp = first->val;
                first->val = node->val;
                node->val = tmp;
            }
            if(last && last->val > node->val)
            {
                first = last;
            }
            last = node;
            dfs(node->right,first,last);
        }
    }
};
```

这样像1,`3`,`2`,4的情况（只发现一次位置不对），就不能进行结点值得交换（代码写死了，只有发现两次位置不对才能进行交换）

参考别人的代码

```cpp
class Solution {
public:
    void recoverTree(TreeNode* root) {
        TreeNode * first = NULL,*second=NULL,*last =NULL;
        dfs(root,first,second,last);
        int tmp = first->val;
        first->val = second->val;
        second->val = tmp;
    }
    void dfs(TreeNode * node,TreeNode * &first,TreeNode * &second,TreeNode * &last)
    {
        if(node)
        {
            dfs(node->left,first,second,last);
            if(last && last->val > node->val)//错误位置不挨着时会进入两次
            {								 //错误位置挨着时只进入一次
                if(!first)					 //保证first,second值正确
                    first = last;
                second = node;
            }
            last = node;
            dfs(node->right,first,second,last);
        }
    }
};
```



#### 如何找到二叉树每一层得最右端结点

**解析**

用一个队列依次加入本层结点对应得下一层得孩子结点，用一个指针last指向本层最后一个结点，当遍历到本层最后一个结点时，队列最后入队的孩子结点就是下一层的最后一个结点，last指向本层最后入队的孩子结点，就完成了last指针的跟新。

**例题**

给定一个二叉树，返回其按层次遍历的节点值。 （即逐层地，从左到右访问所有节点）。

例如:
给定二叉树: [3,9,20,null,null,15,7],
```
    3
   / \
  9  20
    /  \
   15   7
```

返回其层次遍历结果：
```
[
  [3],
  [9,20],
  [15,7]
]
```

**代码**

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        queue<TreeNode *> Q;
        TreeNode * last = root;
        TreeNode * cur;
        Q.push(root);
        vector<vector<int>> res;
        if(!root) return res;
        vector<int> tmp;
        while(!Q.empty())
        {
            cur = Q.front();Q.pop();
            tmp.push_back(cur->val);
            if(cur->left) Q.push(cur->left);
            if(cur->right) Q.push(cur->right);
            if(cur==last) 
            {
                res.push_back(tmp);
                tmp.clear();
                last = Q.back();
            }
        }
        return res;
    }
};
```



#### 从有序链表构建平衡二叉树

**题目** [有序链表转换二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/)

给定一个单链表，其中的元素按升序排序，将其转换为高度平衡的二叉搜索树。

本题中，一个高度平衡二叉树是指一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1。

示例:

给定的有序链表： [-10, -3, 0, 5, 9],

一个可能的答案是：[0, -3, 9, -10, null, 5], 它可以表示下面这个高度平衡二叉搜索树：
```
      0
     / \
   -3   9
   /   /
 -10  5
```



##### (一)有序链表构建有序数组来生成AVL

将有序链表转成有序数组，每次从有序数组中取中间位置的元素作为一棵AVL树的根节点，然后递归的用数组的中间元素的左边元素构建根结点的左子树，用数组的中间元素的右边元素构建根结点的右子树

每次找中间元素位置可用mid = (left+right)/2

特点是利用了数组可以以O(1)时间复杂度取任意位置的元素，每次取中间位置元素保证了左右子树深度差不超过1，且本是升序数组，保证了构建的树是搜索树

##### (二)从单链表中找中间结点来生成AVL

每次在一个链表中用[快慢指针法](./list.md/#如何在单链表中找中间结点)找中间结点，用该结点的值作为AVL树的根节点值，然后递归的生成左右子树

##### （三）模仿中序遍历生成AVL树

先看下中序遍历的模板

```c++
void midOrder(node * t)
{
    if(!t) return;
    midOrder(t->left);
    visit(t);			//可以下功夫的地方
    midorder(t-right);
}
```

观察`第5行：visit(t)`处

之前遍历时是只是访问`t`结点，现在我们把链表对应结点的值赋值给`t`，然后第4行建立t的左子树，第6行建立t的右子树，然后将t返回给上层，递归结束后可以用有序的链表建立一棵二叉搜索树了

但上述方法建立的树肯定不是平衡的，**怎么建立一个平衡的树？**

在学习二分搜索时，是不是利用mid = (left + right)/2建立了一颗平衡的搜索树结构！将此两种方法结合就可以构建出平衡的二叉搜索树了。

**代码**

```c++
class Solution {
public:
    TreeNode* sortedListToBST(ListNode* head) {
        ListNode * p =head;
        int n =0;
        while(p)								//求链表的总长度
        {
            p=p->next;
            ++n;
        }
        return helper(head,0,n-1);
    }
private:
    TreeNode * helper(ListNode * &p,int l,int r)
    {
        if(l>r) return NULL;
        int mid = (l+r)/2;						//用来约束树的框架
        TreeNode * left=helper(p,l,mid-1);		//左
        TreeNode * cur = new TreeNode(p->val);	//中
        cur->left = left;						
        p=p->next;
        cur->right=helper(p,mid+1,r);			//右
        return cur;
    }
};
```

#### [二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

给定一个非空二叉树，返回其最大路径和。

本题中，路径被定义为一条从树中任意节点出发，达到任意节点的序列。该路径至少包含一个节点，且不一定经过根节点。

示例 1:

输入: [1,2,3]
```
       1
      / \
     2   3
```
输出: 6
示例 2:

输入: [-10,9,20,null,null,15,7]
```
   -10
   / \
  9  20
    /  \
   15   7
```
输出: 42



**思路**

首先应该想到利用后序遍历，因为要先判断左右子树的情况，在判断加入根结点后的情况。

设计后序遍历递归函数

假如二叉树如下所示：



<p align="center">
	<img src=./pictures/012.png alt="Sample"  width="250">
	<p align="center">
		<em>二叉树例子</em>
	</p>
</p>

递归过程：

+ 遇到空结点时候返回0；

+ 遇到非空结点则计算以该结点为**根**的树中最长路径和，具体先计算左右子树的最大路径和，然后计算考虑加入当前根结点时候的最大路径和，如下图所示，当前结点为`-10`，则加入当前节点后，最大路径和有以下几种情况，取最大即可；



<p align="center">
	<img src=./pictures/123.png alt="Sample"  width="550">
	<p align="center">
		<em>遍历到非空结点</em>
	</p>
</p>

+ 返回值：返回以该结点为**头结点**的最大路径和。只有三种情况，取最大。

<p align="center">
	<img src=./pictures/456.png alt="Sample"  width="550">
	<p align="center">
		<em>返回值情况</em>
	</p>
</p>

代码：

```c++
class Solution {
public:
    //重点：采用后续遍历
    //分清情况，一点一点的写出来，其实并不是很难
    int maxPathSum(TreeNode* root) {
        res= INT_MIN;
        postOrder(root);
        return res;
    }
private:
    int res;
    int postOrder(TreeNode * cur)
    {
        if(cur==NULL) return 0;
        int left = postOrder(cur->left);
        int right= postOrder(cur->right);
        //找该棵树中的最大路径和
        int tmp = cur->val;
        if(tmp<0)
        {
            res = max(res,tmp);
            res = max(res,tmp+left+right);
        }
        else
        {
            if(left>0) tmp+=left;
            if(right>0) tmp+=right;
            res = max(res,tmp);
        }
        //处理返回值
        tmp = cur->val;
        if(left>0 || right>0) tmp+=max(left,right);
        return tmp;
    }
};
```




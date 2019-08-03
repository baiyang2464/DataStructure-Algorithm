#### 访问链表的一些注意事项

链表常见的问题有多链表归并，交换链表中的结点，对链表进行排序（一个单链表可用直接插入法，两个单链表可用归并方法）等

会涉及头插法、尾插法等链表的访问、操作方法

操作链表时，若空间允许可以人为增加一个头结点，这样链表的很多操作都统一了，比较方便。

#### 如何在单链表中找中间结点

使用快慢指针slow，fast，快指针每走两步，慢指针走一步，快指针遍历完整个链表时，慢指针刚好指向链表中间结点。

若要找到中间结点之前一个结点，可以让慢指针晚一步走。

![](./pictures/slow_fast_pointer.png)





#### [k个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

**题目**

给出一个链表，每 *k* 个节点一组进行翻转，并返回翻转后的链表。

*k* 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 *k* 的整数倍，那么将最后剩余节点保持原有顺序。

**示例 :**

给定这个链表：`1->2->3->4->5`

当 *k* = 2 时，应当返回: `2->1->4->3->5`

当 *k* = 3 时，应当返回: `3->2->1->4->5`

**说明 :**

+ 你的算法只能使用常数的额外空间。
+ **你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

**代码:**

**感悟:**该题目并不难，但是你首次做却做了很久，一是不太熟悉链表操作了，而是C/C++语言的指针用法不熟悉了，忘了函数传参时什么时候会改变实参什么时候又不会。

```c++
 ListNode* reverseKGroup(ListNode* head, int k) {
        struct ListNode * linkNode= new ListNode(0);
        struct ListNode * left=linkNode;
        struct ListNode * right= head;
        linkNode->next = head;
        int dist = 1;
        while(right)
        {
            dist =1;
            while(right&&dist<k)
            {
                right=right->next;
                dist+=1;
            }
            if(right&&dist==k)
            {
                cout<<left->val<<" "<<right->val<<endl;
                reverseSection(left,right);
                left = right;
                right = left->next;
            }
            else
                break;
            cout<<"right->val"<<right->val<<endl;
        }
        head = linkNode->next;
        delete linkNode;
        return head;
    }
    void reverseSection(ListNode * &headPre,ListNode * &tail)//headPre是局部链表的首结点之前的一个结点（相当于头结点）
        //局部反转链表
    {
        if(headPre==NULL)
            return;
        struct ListNode * p= headPre->next;
        struct ListNode * q=NULL;
        struct ListNode * last=tail->next;
        tail = headPre->next;
        headPre->next = last;
        while(p!=last)
        {
            q = p->next; 
            p->next = headPre->next;
            headPre->next = p;
            p = q;
        }
        cout<<"tail="<<tail->val<<endl;
    }
```
#### [删除排序链表中的重复元素-II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

**题目**
给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现 的数字。

**示例 1:**

输入: 1->2->3->3->4->4->5
输出: 1->2->5
**示例 2:**

输入: 1->1->1->2->3
输出: 2->3

>感悟：这道题目你纠结了好久，做这么长时间原因有两点，①没有画图直接凭空想象（做链表题目画画示意图可以直观的演示结点、指针操作过程，下次一定要记得画图），②可以自己为链表添加一个头结点，这样链表的操作就会统一，减少很多特殊情况的思考。
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        //自己添加一个头结点，可以统一链表的操作，少很多麻烦
        if(!head) return head;
        ListNode * p = new ListNode(0);
        p->next = head;
        head = p;
        ListNode * left ,*right;
        while(p->next)
        {
            left=p->next;right = left;
            while(right->next && right->next->val == left->val)
                right=right->next;
            if(right==left)//left，right指向同一结点，该结点的值没有重复出现，不需要裁剪这个结点
                p=p->next;
            else//裁剪
                p->next=right->next;
        }
        return head->next;
    }
};
```



#### 判断单链表是否有环

涉及题目：[环形链表](https://leetcode-cn.com/problems/linked-list-cycle)

**题目**

给定一个链表，判断链表中是否有环。

为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

示例 1：
```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

<p align="center">
	<img src=https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png alt="Sample"  width="250">
	<p align="center">
		<em>有环链表</em>
	</p>
</p>

示例 2：

```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

<p align="center">
	<img src=https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png alt="Sample"  width="130">
	<p align="center">
		<em>有环链表</em>
	</p>
</p>

示例 3：

```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```

<p align="center">
	<img src=https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test3.png  width="40">
	<p align="center">
		<em>一个结点没有环</em>
	</p>
</p>

**（一）用哈希表存储结点**

遍历链表，将访问过的结点都存储到哈希表中，当再次访问已经访问过的结点时，则有环。

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        unordered_set<ListNode*> hash;
        ListNode * cur = head;
        while(cur && hash.count(cur) ==0 )
        {
            hash.insert(cur);
            cur=cur->next;
        }
        return cur;
    }
};
```



时间复杂度：O(n)，遍历一次

空间复杂度：O(n)，存储所有结点

**（二）快慢指针法**

快指针每次走两步，慢指针每次走一步，若快慢指针相遇而没有变成`NULL`则有环，否则无环

```c++
class Solution {
    public:
    bool hasCycle(ListNode *head) {
        if(!head || !head->next) return false;
        ListNode * slow = head;
        ListNode * fast= head;
        while(slow &&fast && fast->next)
        {
            fast=fast->next->next;
            slow=slow->next;
            if(fast==slow) return true;
        }
        return false;
    }
};
```

时间复杂度：O(n)，有环的话最多将链表遍历二次

空间复杂度：O(1)

#### [环形链表-II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

说明：不允许修改给定的链表。

示例 1：
```
输入：head = [3,2,0,-4], pos = 1
输出：tail connects to node index 1
解释：链表中有一个环，其尾部连接到第二个节点。
```

<p align="center">
	<img src=https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png alt="Sample"  width="250">
	<p align="center">
		<em>有环链表</em>
	</p>
</p>

示例 2：

```
输入：head = [1,2], pos = 0
输出：tail connects to node index 0
解释：链表中有一个环，其尾部连接到第一个节点。
```

<p align="center">
	<img src=https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png alt="Sample"  width="130">
	<p align="center">
		<em>有环链表</em>
	</p>
</p>

示例 3：

```
输入：head = [1], pos = -1
输出：no cycle
解释：链表中没有环。
```

<p align="center">
	<img src=https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test3.png  width="40">
	<p align="center">
		<em>一个结点没有环</em>
	</p>
</p>

**思路**

找环的头结点分三步

1. 用快慢指针找到相遇结点；
2. 用快慢指针从相遇结点出发到再次相遇，计算环的长度len；
3. front指针从head出发先走len步，然后before指针从head出发与front同步，front与before第一次相遇点就是如环的头结点

关键就是把环的长度计算出来

**代码**

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        //找第一次相遇结点
        if(!head) return NULL;
        ListNode * fast = head,* slow = head;
        ListNode * start = NULL;
        while(slow && fast && fast->next && !start)
        {
            fast = fast->next->next;
            slow = slow->next;
            if(fast==slow)
                start= fast;
        }
        if(!start) return NULL;
        //计算环的长度
        int len = 0;
        do{
            slow = slow->next;
            ++len;
        }while(slow!=start);
        //front先走len步
        ListNode * front = head, * before = head; 
        while(len--)
            front = front->next;
        while(front != before)
        {
            front = front->next;
            before = before->next;
        }
        return front;
    }
};
```

时间复杂度：O(n)，有环的话链表仅仅遍历数次而已

空间复杂度：O(1)

#### [排序链表](https://leetcode-cn.com/problems/sort-list/)

在 O(n log n) 时间复杂度和常数级空间复杂度下，对链表进行排序。

示例 1:
```
输入: 4->2->1->3
输出: 1->2->3->4
```
示例 2:
```
输入: -1->5->3->4->0
输出: -1->0->3->4->5
```

**思路**

题目要求O(n log n) 时间复杂度对链表进行排序（空间复杂度暂且不管），可以考虑归并排序

归并排序分为两步：

**第一步： 使用快慢指针法把链表分割成两个链表**

+ 使用快慢指针找到链表中间结点

+ 把链表分成两个链表

+ 不用考虑奇偶数问题

**第二步： 递归的合并有序的链表（使用归并算法）**

+ 第一步得到的两个链表是两个子问题，子问题递归的进行分割和归并

+ 两个子函数返回已经有序的子问题链表的头指针，归并两个有序链表为一个链表

+ 使用递归，每次只判断链表头，代码简洁且易懂

**总结**

+ 把问题分解成子问题，比较容易解决（分成两步（二分法和合并有序链表））

+ 如果循环解决问题困难，考虑使用递归（合并有序链表）

```c++
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        if(!head || !head->next) return head;
        ListNode * slow = head,* fast = head;
        //快慢指针找中间结点
        while(fast && fast->next)
        {
            fast = fast->next;
            if(!fast->next) break;
            fast = fast->next;
            slow=slow->next;
        }
        fast = slow->next;
        slow->next = NULL;//一定要前后分割开
        return merge(sortList(head),sortList(fast));//归并有序的子链表
    }
    ListNode * merge(ListNode * left,ListNode * right)//二路归并排序要求归并的两个子序列分别是有序的
    {
        ListNode * head = NULL;
        ListNode * min=NULL,*tail=NULL;
        while(left && right)
        {
            if(left->val < right->val)
            {
                min = left;
                left = left->next;
            }
            else
            {
                min = right;
                right = right->next;
            }
            if(!tail)
                tail = min;
            else
            {
                min->next = tail->next;
                tail->next = min;
                tail = min;
            }
            if(!head)
                head = tail;
        }
        if(left)
            tail->next = left;
        if(right)
            tail->next = right;
        return head;
    }
};
```

**分析**

时间复杂度：O(nlogn)，递归树深度为logn，递归过程中每层的问题的总规模是n，所以时间复杂度是O(n*logn)



#### [合并K个排序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

合并 k 个排序链表，返回合并后的排序链表。请分析和描述算法的复杂度。

示例:
```
输入:
[
  1->4->5,
  1->3->4,
  2->6
]
输出: 1->1->2->3->4->4->5->6
```

**思路**

此题暴力做法是对k个排序链表进行K路归并，每次遍历k个链表的头结点，选取最小的插入到归并结果链表的尾结点之后，直至归并完所有结点。

较为高效的做法是：

```c++
1）以k个排序链表的头结点建立一个小顶堆，为合并结果链表建立一个头结点和尾结点
2）选区堆顶元素插入到结果链表的尾结点
	a）若堆顶结点所在链表有下一个结点，则将下一个结点放在堆顶
	b）否则将堆的最后一个结点放在堆顶，堆的元素个数减一
	c）对堆顶元素做堆调整，使堆重新符合小顶堆要求
3）堆空，则归并完毕
```

**代码**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if(lists.empty()) return NULL;
        vector<ListNode*> heap;
        heap.push_back(lists[0]);//占位
        for(auto node : lists)
            if(node) heap.push_back(node);
        //建立一个小根堆
        int idx = heap.size()/2;//最后一个父节点
        while(idx>0)
        {
            adjustHeap(heap,idx);
            --idx;
        }
        ListNode * head=NULL ,*tail=NULL;
        //取堆顶元素
        //然后做堆调整
        while(heap.size()>1)
        {
            if(!head)
            {
                head=heap[1];
                tail = head;
            }
            else
            {
                tail->next = heap[1];
                tail = tail->next;
            }
            if(tail->next)
                heap[1] = tail->next;
            else
            {
                heap[1]= heap.back();
                heap.pop_back();
            }
            adjustHeap(heap,1);//插入新结点后要进行堆调整，使其重新满足小顶堆要求
        }
        return head;
    }
    void adjustHeap(vector<ListNode*> &heap,int idx)
    {
        int cur= idx;
        ListNode * tnode = heap[idx];
        while(cur<=heap.size()/2)
        {
            int minChild= cur*2;
            if(minChild>=heap.size()) break;
            if(minChild+1<heap.size() &&heap[minChild]->val> heap[minChild+1]->val) ++minChild;
            if(tnode->val>heap[minChild]->val)
            {
                heap[cur] = heap[minChild];
                cur = minChild;
            }
            else break;
            cur = minChild;
        }
        heap[cur] = tnode;
    }
};
```




### 哈希

#### 哈希的实现方式即冲突解决方法

待补充



#### [最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

**题目**

给定一个未排序的整数数组，找出最长连续序列的长度。

要求算法的时间复杂度为 O(n)。

示例:
```
输入: [100, 4, 200, 1, 3, 2]
输出: 4
解释: 最长连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

**思路**

心急可以直接看[解法三](#（三）采用hash)

题目要求时间复杂度为O(n)，介于无法马上想出较好的解法，可以先想想暴力解法

##### （一）最暴力的解法

依次遍历数组每个元素，针对每个元素我们以它为一个序列的头结点，然后找它之后的一位元素是否在数组中（是否存在也是遍历），每多找到一位则这条序列的长度加一，直至后面一位元素不在数组中。遍历所有元素。时间复杂度为O(n^3)

```java
class Solution {
    private boolean arrayContains(int[] arr, int num) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == num) {
                return true;
            }
        }
        return false;
    }
    public int longestConsecutive(int[] nums) {
        int longestStreak = 0;

        for (int num : nums) {
            int currentNum = num;
            int currentStreak = 1;

            while (arrayContains(nums, currentNum + 1)) {
                currentNum += 1;
                currentStreak += 1;
            }

            longestStreak = Math.max(longestStreak, currentStreak);
        }

        return longestStreak;
    }
}
```

复杂度分析

时间复杂度：O(n^3)

外部循环运行恰好 n次。同时，因为 currentNum 在 while 循环中每次增加 1 ，所以 while 循环会运行 O(n)次。在每次 while 循环中，会有一个对数组 O(n)的查找。所以暴力算法是一个嵌套三层 O(n的循环，总时间复杂度是 O(n^3 )。

空间复杂度：O(1)

暴力算法仅使用了有限的整数，所以它用了常数级别的额外空间。



##### （二）排序

先用快排把数组排一遍，然后找有序数组的最长连续序列（遇到相同元素序列不断开，但序列长度不增加）。时间复杂度O(nlogn)

##### （三）采用hash

+ 怎么降低时间复杂度？

本题目前面两种方法都不能达到题目的要求，怎么可以进一步降低时间复杂度？一是可以设计更加巧妙的算法，二是可以**用空间换时间**，因此可以想到用hash表来存储数组又可以去重。c++可以使用unordered_set这个数据结构。

+ 接下来是怎么判断连续？

由于是整数，例如连续序列arr=[1,2,3,4]中的`arr[2]=3`只要比前一个元素大`arr[1]=2`大1就连续。所以在hash表中只用检查`当前数字-1`是否在哈希表中即可。

+ 怎么计算连续序列的长度？

计算一个连续序列长度，得首先找到该序列的头结点。若是递增顺序的话得找到最小的那个结点，然后依次以连续的标准找后续结点并计算连续序列的长度。

有没有更好的方法？找最小结点岂不是又要遍历数组然后记录min！

其实可以直接判断**`结点值-1`**是否在hash表中即可，若不在则该结点一定是某个连续序列的头结点（极限情况是该序列就一个结点），而其它结点一定已经出现在了某个连续序列里。

**算法步骤**

1. 将数组存入hash表中

2. 遍历数组
   1. 找头结点
   2. 计算连续序列的长度
   3. 一个连续序列长度计算完跳转(1)找下一个头结点

3. 返回最长长度

```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        if(!nums.size()) return 0;
        unordered_set<int> hash(nums.begin(),nums.end());
        int max=1;
        int len = 0;
        vector<int> dp(nums.size(),1);
        for(int i=0;i<nums.size();++i)
        {
            if(hash.count(nums[i]-1)) continue;
            int cur = nums[i];
            len = 0;
            while(hash.count(cur)) 
            {
                ++cur;
                ++len;
            }
            if(max<len) max=len;
            
        }
        return max;
    }
};
```

分析

时间复杂度：O(n)

while循环只有在头结点使执行，整个算法只遍历了一次数组

空间复杂度：O(n)

采用hash表



#### [LRU缓存机制](https://leetcode-cn.com/problems/lru-cache/)

**题目**

运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制。它应该支持以下操作： 获取数据 get 和 写入数据 put 。

获取数据 get(key) - 如果密钥 (key) 存在于缓存中，则获取密钥的值（总是正数），且将该密钥的值标记为最近使用，否则返回 -1。
写入数据 put(key, value) - 如果密钥不存在，则写入其数据值。当缓存容量达到上限时，它应该在写入新数据之前删除最近最少使用的数据值，从而为新的数据值留出空间。

进阶:

你是否可以在 O(1) 时间复杂度内完成这两种操作？

示例:
```
LRUCache cache = new LRUCache( 2 /* 缓存容量 */ );

cache.put(1, 1);
cache.put(2, 2);
cache.get(1);       // 返回  1
cache.put(3, 3);    // 该操作会使得密钥 2 作废
cache.get(2);       // 返回 -1 (未找到)
cache.put(4, 4);    // 该操作会使得密钥 1 作废
cache.get(1);       // 返回 -1 (未找到)
cache.get(3);       // 返回  3
cache.get(4);       // 返回  4
cache.put(3,5);     // 该操作会使得密钥 3 得到更新
cache.put(5,1);     // 该操作会使得密钥 4 作废
```



**思路**

LRU（最近最少使用）内存页面管理机制

+ 在内存满时将内存队列尾部的页面换出以给新访问不在内存中的页面提供位置；

+ 最近访问的页面会被放到内存队列头部，以表示最近访问过；

+ 以O(1)时间复杂度完成操作；

这要求LRU中要有一个队列，能先进先出，且可将最近访问的数据调到头部。

操作的时间复杂度要为O(1)，则自然会想到用hash来存储key可以使得key的查询时间为O(1)；

为了使得结点交换的操作为O(1)，队列不可能用数组，只能用链表，则hash表中键为key，值为链表中对应结点的指针；

链表中删除队尾结点需要将尾指针移动前前一个结点，因此链表应该是双向链表；

删除链表的尾结点时，也要删除hash表中对应的键值对，需要知道链表尾结点对应的key，因此链表结点的成员应存储该结点的key;

所以LRU的数据结构应该是个hash双向链表，结构如下：

<p align="center">
	<img src=https://pic.leetcode-cn.com/39f4af83bbd64b0078be748474000cc99e6f488cc3f56101c2eba7d42f7321ba-WX20190606-145838@2x.png alt="Sample"  width="600">
	<p align="center">
		<em>hash双向链表</em>
	</p>
</p>

链表结点应该包含以下成员：

```c++
class node{
    int key;
    int val;
    node *next;
    node *pre;
};
```

实现代码：

```c++
class node{
    public:
    node(int x,int y)
    :key(x),val(y),next(NULL),pre(NULL){}
    int key;
    int val;
    node *next;
    node *pre;
};
class LRUCache {
    public:
    LRUCache(int capacity) 
    :_capacity(capacity)
    ,_size(0)
    ,_hash()
    ,_head(NULL)
    ,_tail(NULL)
    {}

    int get(int key) {
        if(_size>0)
        {
            if(_hash.count(key))//已经存在
            {
                //最近被使用，调换结点在链表中的位置
                node *ptr = _hash[key];
                move2head(ptr);
                return ptr->val;
            } 
            else return -1;
        }
        else return -1;
    }

    void put(int key, int value) {
        if(_hash.count(key))//已经存在
        {
            _hash[key]->val = value;//更新值
            move2head(_hash[key]);//将结点移动到头结点
            return;
        }
        if(_size==_capacity)//容量达到饱和，需要换出
        {
            node * ptr = _tail;
            if(_head==_tail)
            {
                _head = _tail = NULL;
            }
            else
            {
                _tail = _tail->pre;
                _tail->next = NULL;
            }
            //删除hash表中相应值
            _hash.erase(ptr->key);
            //取出链表最后一个结点
            delete ptr;
            --_size;
        }
        node * p = new node(key,value);
        p->next = _head;
        if(_head)
            _head->pre = p;
        _head = p;
        if(!_tail)//tail为空，即size=0的特殊情况
            _tail = _head;
        _hash.insert({key,_head});
        ++_size;
    }
    void move2head(node * ptr)//将已经访问过的结点移至链表头结点
    {
        if(_head == _tail || _head==ptr) return;
        if(_tail == ptr) _tail = ptr->pre;
        if(ptr->pre)
            ptr->pre->next = ptr->next;
        if(ptr->next)
            ptr->next->pre = ptr->pre;
        ptr->next = _head;
        ptr->pre=NULL;
        _head->pre = ptr;
        _head = ptr;
    }
    private:
    int _capacity;
    int _size;
    unordered_map<int, node*> _hash;
    node * _head;
    node * _tail;
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```


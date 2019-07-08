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
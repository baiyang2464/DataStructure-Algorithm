

## 数组

数组类型的问题经常结合

+ 双指针

+ 栈、堆

+ 动态规划

+ 字符串匹配

+ 排列组合问题

+ 排序

+ 数组还可以被用来做hash，用空间换取时间

## 常见的排序算法

它们之间的关系

![img](https://upload-images.jianshu.io/upload_images/1156494-ab4cecff133d87b3.png)

它们之间的比较

![img](https://upload-images.jianshu.io/upload_images/1156494-62f859c2ac6f95ff.png)

几个排序算法的代码有待补充



## 删除排序数组中的重复项

链接：[26. 删除排序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

**题目**

给定一个排序数组，你需要在**原地**删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在**原地修改输入数组**并在使用 O(1) 额外空间的条件下完成。

**示例 :**


给定数组 nums = [1,1,2], 
函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 
你不需要考虑数组中超出新长度后面的元素。

**代码**

```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int n = nums.size();
        int p=1;
        int delNum=0;
        if(0==n||1==n)
            return n;
        while(p<n)
        {
           if(nums[p]==nums[p-1]) 
           {
               delNum++;
           }
            nums[p-delNum]=nums[p];
            ++p;
        }
        return n-delNum;
    }
};
```

## 下一个排列

**题目**

实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

必须**原地**修改，只允许使用额外常数空间。

**示例**

以下是一些例子，输入位于左侧列，其相应输出位于右侧列。

`1,2,3` → `1,3,2`

`3,2,1` → `1,2,3` 

`1,1,5` → `1,5,1` 

**分析**

+ 排列的大小

1. 若一个排列是降序，则该序列最大
2. 若一个排列是升序，则该序列是最小

+ 找最长前缀

如果一个排列为A，下一个排列为A_NEXT，那么A_NEXT一定与A有尽可能长的公共前缀。 

看具体例子，一个排列为124653，如何找到它的下一个排列，因为下一个排列一定与124653有尽可能长的前缀，所以，脑洞大开一下，从后面往前看这个序列，如果后面的若干个数字有下一个排列，问题就得到了解决。

第一步：找最后面1个数字的下一个全排列。

124653，显然最后1个数字3不具有下一个全排列。

第二步：找最后面2个数字的下一个全排列。

124653，显然最后2个数字53不具有下一个全排列。

第三步：找最后面3个数字的下一个全排列。

124653，显然最后3个数字653不具有下一个全排列。

插曲：可以看出，如果一个序列是递减的，那么它不具有下一个排列。

第四步：找最后面4个数字的下一个全排列。

124653，发现显然最后4个数字4653具有下一个全排列。因为它不是递减的，例如6453，5643这些排列都在4653的后面。

并总结出重复上面操作的两种终止情况：

1. 从后向前比较相邻的两个元素，直到前一个元素小于后一个元素，停止
2. 如果已经没有了前一个元素，则说明这个排列是递减的，所以这个排列是没有下一个排列的。

124653这个排列终止情况是上面介绍的第一种，从后向前比较相邻的2个元素，遇到4<6的情况停止。

并且可以知道：

1. 124653和它的下一个排列的公共前缀为12(因为4653存在下一个排列，所以前面的数字12保持不变)
2. 4后面的元素是递减的(上面介绍的终止条件是前一个元素小于后一个元素，这里是4<6)

+ 找非前缀的下一个排列

现在，考虑如何找到4653的下个排列，首先明确4后面的几个数字中至少有一个大于4.

4肯定要和653这3个数字中大于4的数字中(6，5)的某一个进行交换。这里就是4要和6，5中的某一个交换，很明显要和5交换，如果找到这样的元素呢，因为我们知道4后面的元素是递减的，所以在653中从后面往前查找，找到第一个大于4的数字，这就是需要和4进行交换的数字。这里我们找到了5，交换之后得到的临时序列为5643.，交换后得到的643也是一个递减序列。

所以得到的4653的下一个临时序列为5643，但是既然前面数字变大了(4653--->5643)，后面的自然要变为升序才行，变换5643得到5346.

所以124653的下一个序列为125346.

对于第二种情况，该序列是一个降序序列，直接将整个序列翻转即可。

**代码**

```c++
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        //从后往前找第一个非降序的元素——目标元素
        int n = nums.size();
        if(1 == n)
            return;
        int i = n-2;
        for(;i>=0 && nums[i]>=nums[i+1];--i);
        //该元素之前为公共序列，之后为一个降序序列，目标元素与之后的降序序列为一个子序列
        //若一个序列为降序序列，则该序列最大,若一个序列为升序序列，则该序列最小
        //在降序序列中从后往前找第一个比目标元素大的元素，与目标元素交换
        if(i<0)
            reverse(nums.begin(),nums.end());
        else
        {
            int j = n-1;
            for(;j>i && nums[j]<= nums[i];--j);
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j]=temp;
            //得到的新子序列，比原序列子序列要大,将新子序列除去左端元素后，升序排序
            //此时就得到整个原序列的下一个序列
            sort(nums.begin()+i+1,nums.end());
        }
    }
};
```

## 最长有效括号

**题目**

给定一个只包含 `'('` 和 `')'` 的字符串，找出最长的包含有效括号的子串的长度。 

**示例 1:**

```
输入: "(()"
输出: 2
解释: 最长有效括号子串为 "()"
```

**示例 2:**

```
输入: ")()())"
输出: 4
解释: 最长有效括号子串为 "()()"
```

**分析**

对字符串遍历，进行括弧有效性验证，记录最大的有效长度。同样的方式，倒序再来一次，取最大值。时间复杂度 2*s.length 。

请先看代码再看此处的说明

+ 由左至右判断: 
  1. ')'数量 > '('数量时，一定无效，数量归零，继续计算，最终会有正确结果；
  2. 当')'数量 = '('数量时，当前有效，最总会得到正确结果； 
  3. 当最後 ')'数量 < '('数量，无法确定有效括号的数量，只能确定<=')'数量。故需要倒序再来一次，就转为第一种情况。
+ 由右至左: 1.')'数量 < '('数量时，一定无效，数量归零，继续计算；2.若')'數量 = '('數量時，當前有效；

**代码**

```c++
class Solution {
public:
    int longestValidParentheses(string s) {
        return max(longestParentheses(s,0,s.length(),1,'('),
                   longestParentheses(s,s.length()-1,-1,-1,')')
        );
    }
    
private:
    int longestParentheses(string &s,int start,int end,int step,char c)
    {
        int sum,max,curlen,validp;
        sum=max=curlen=validp=0;
        for(int i=start;i!=end;i+=step)
        {
            sum+= s[i]==c? 1 : -1;
            ++curlen;
            if(sum<0)
            {
                max =max < validp ? validp : max;
                sum = curlen=0; 
            }
            if(sum == 0)
                validp= curlen;
        }
        return max=max < validp ? validp : max;
    }
};
```

## [接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

**题目**

给定 *n* 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。 

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png)

上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 **感谢 Marcos** 贡献此图。 

**示例**

```
输入: [0,1,0,2,1,0,1,3,2,1,2,1]
输出: 6
```

**（1）单调栈法**

设置一个栈，存放高度数组的中元素的下标，
+ 遍历`height高度数组`，数组当前下标用`right`表示
  + 若栈为空或当前高度大于栈顶元素高度，则将当前高度下标入栈
  + 否则，栈顶下标对应的高度形成一个洼地，可以接雨水，将栈顶元素出栈，设为`low`，
    + 若栈未空，洼地的左边高度就是原栈中紧挨`low`的第二个元素——现在的栈顶元素，设为`left`，左边高度就是`nums[left]`，洼地右边高度就是`nums[right]`，累加局部面积面积`min(nums[right],nums[left])-nums[low] * (right-left-1)`，然后将`right`入栈
    + 若栈空，直接将`right`入栈

**代码**

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        stack<int> s;
        int right=0;
        int water = 0;
        while(right<height.size())
        {
            while(!s.empty() && height[right]>height[s.top()]) 
            {
                int top = s.top();s.pop();
                if(!s.empty())
                {
                    int left = s.top();
                    water+=((min(height[left],height[right])-height[top])*(right-left-1));
                }
            }
            s.push(right++);
        }
        return water;
    }
};
```

**分析**

时间复杂度：O(n)，遍历一次数组，栈中元素（由于出栈入栈）最多访问两次

空间复杂度：O(n)，栈最多装下所有元素

**（2）双数组法**

每一处水柱的高度取决于该处左右两边实心柱的高度的最小者，最小者减去该处水底的高度，就得到水柱的深度，即该处水的量，若深度为负数代表该处是高地（即两边都比该处矮）需要舍去，最后求和得到总水量。

**代码**

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        if(n==0)
            return 0;
        //left[i]表示i左边的最大值，right[i]表示i右边的最大值
        vector<int> left(n);
        vector<int> right(n);
        for(int i =1;i!=n;++i)
            left[i] = left[i-1] > height[i-1] ? left[i-1] : height[i-1];
        for(int i =n-2;i>=0;--i)
            right[i] = right[i+1] > height[i+1] ? right[i+1] : height[i+1];
        int water =0;
        for(int i =0;i!=n;++i)
        {
            int level = min(left[i],right[i]);//水平面高度
            water+=max(0,level - height[i]);//水柱深度，舍去负值，求和得总水量
        }
        return water;
    }
};
```
**分析**

时间复杂度：O(n)，遍历两次数组

空间复杂度：O(n)，额外定义了两个数组

**（3）双指针法**



<p align="center">
	<img src=./pictures/072001.PNG alt="Sample"  width="500">
	<p align="center">
		<em>collect rain water</em>
	</p>
</p>

设两个指针`left`与`right`；

再设`maxHeight`表示某个地方至少可以储水的高度，更新`maxHeight`的方法可看上图，接下来的问题是怎么更新`left`与`right`指针：

+ 若`height[left]<=height[right]`则`++left`
+ 否则`--right`
+ `cur`则取变化的那个指针，水量累加

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        if(height.empty()) return 0;
        int water=0,left =0,right = height.size()-1;
        int maxHeight = min(height[left],height[right]);
        int cur;
        while(left<right)
        {
            if(height[left]<=height[right]) 
                cur= (++left);
            else cur=(--right);
            maxHeight = max(maxHeight,min(height[left],height[right]));
            if(maxHeight>height[cur])
                water+=(maxHeight-height[cur]);
        }
        return water;
    }
};
```

时间复杂度：O(n)，双指针遍历一次数组

空间复杂度：O(1)，显而易见

## 最小覆盖子串

**题目**
给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字母的最小子串。

**示例**
输入: S = "ADOBECODEBANC", T = "ABC"
输出: "BANC"
**说明**
如果 S 中不存这样的子串，则返回空字符串 ""。
如果 S 中存在这样的子串，我们保证它是唯一的答案。
**代码**
```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        /*这样子的解法也类似滑动窗口*/
        vector<int> map(128,0);//做一个hash数组，存放所有能被表示出来的ASICII字符，初始化数组元素为0
        int start=0,minlen = INT_MAX;//窗口开始的位置，窗口最小长度，
        int cnt =0;//t中的字母在s的子串中出现的次数
        string res = "";//结果串
        for(auto c:t)//初始化hash数组，有效的字符的数组值将＞0
            ++map[c];
        for(int i=0;i!=s.size();++i)//i表示滑动窗口的右边界
        {
            //窗口右边界往右扩
            if(--map[s[i]]>=0)//map[s[i]]先执行--操作，若值仍>=0,表示s[i]为t中有效字符
                ++cnt;//此时计数加一
            while(cnt==t.size())//找到一个有效子串 或表示 变动左边界时仍处于一个有效子串当中
            {
                if(minlen>i-start+1)//更新最小子串
                {
                    minlen = i-start+1;
                    res = s.substr(start,minlen);
                }
                //检查左边界当前字符是否式t中有效字符
                if(++map[s[start]]>0)//map[s[i]]先执行++，若值>0才表示是有效字符
                    --cnt;
                ++start;//左边界朝右扩展
            }
        }
        return res;
    }
};
```
## [柱状图中最大的矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

**题目：**

 给定 n 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。
 求在该柱状图中，能够勾勒出来的矩形的最大面积。



![](https://img-blog.csdnimg.cn/20190328095324592.png)

以上是柱状图的示例，其中每个柱子的宽度为 1，给定的高度为 [2,1,5,6,2,3]。

![](https://img-blog.csdnimg.cn/20190328095332204.png)

图中阴影部分为所能勾勒出的最大矩形面积，其面积为 10 个单位。

**示例:**

输入: [2,1,5,6,2,3]

输出: 10

**解析：** 

这道题最好的做法是是使用单调栈，只要遍历一次就可求出最大面积。

**如何求最大矩形面积：** 

现在我们从头开始讲，如果要求只能遍历一次，那么如何求最大面积？
我想到一个思路，那就是先把能完全包含各个柱状图的矩形的最大面积求出来，然后求出其中最大值即可。以例题来说就是

*   能完全覆盖第 0 个柱子的最大矩形

![](https://img-blog.csdnimg.cn/20190328100531585.png)

*   能完全覆盖第 1 个柱子的最大矩形

![](https://img-blog.csdnimg.cn/20190328100617165.png)

*   能完全覆盖第 2 个柱子的最大矩形

![](https://img-blog.csdnimg.cn/20190328100648369.png)

*   能完全覆盖第 3 个柱子的最大矩形

![](https://img-blog.csdnimg.cn/20190328100625809.png)

*   能完全覆盖第 4 个柱子的最大矩形

![](https://img-blog.csdnimg.cn/20190328100655726.png)

*   能完全覆盖第 5 个柱子的最大矩形

![](https://img-blog.csdnimg.cn/20190328100702406.png)

如此这般，就能够覆盖所有分支而又不遗漏，将这 6 个矩形的面积比较下就知道最大面积了。

**如何求以某个柱子为高的最大矩形：** 

![](https://img-blog.csdnimg.cn/20190328100655726.png)

我们就以例题中第 4 个，高为 2 的柱子举例好了。
矩形的面积 = 高 * 宽。
我们很高兴的发现，在这个分支情况下，我们已经知道高为 2 了，那么宽度如何求呢？
通过观察，我们发现矩形的左边沿是左边第一个高比 2 小的柱子，右边沿是右边第一个高比 2 小的柱子（将高为 3 的柱子的右面看作还有一个高为 0 的柱子）
如此它的宽度是 6-（1+1）=4

这时可能你已经一头雾水了，6 是啥？1 是啥？为什么要加 1？
如果你这么问，我打赌你肯定是如下图这么想的。

![](https://img-blog.csdnimg.cn/20190328102343847.png)

如果你将柱子的左下角对应序号的话，或许会好一些。

![](https://img-blog.csdnimg.cn/20190328102649724.png)

我们说了矩形的左边沿是第 1 个柱子的右边，那可不就是（1+1）了吗？
矩形右边沿是第 6 个柱子的左边，直接就是 6.
宽度自然就是 6-（1+1）=4 了。

> 注意：基于各个高度的最大矩形是在出栈的时候计算的，因此必须要让所有高度都出栈。这里是利用单调栈的性质让其全部出栈，即在原始数组后添一个 0.

**如何寻找左右边沿：** 

我们已经说了，左边沿是左边第一个小于本柱子高的柱子的右边沿，右边沿也是同理（右边沿是右边第一个小于本柱子高的柱子的左边沿）。==这正好可以用单调栈== 
当第 i 个柱子进栈时，如果栈顶柱子（此处记作柱子 A）的高度低于或等于第 i 个柱子，则第 i 个柱子进栈；
如果高于第 i 个柱子，则出栈，并计算以柱子 A 为高的矩形最大面积。

*   高度：就是柱子 A 的高度
*   右边沿：正好是 i（由于单调栈的性质，第 i 个柱子就是右边第一个矮于 A 的柱子）
*   左边沿：单调栈中紧邻 A 的柱子。（如果 A 已经出栈，那么左边沿就是 A 出栈后的栈顶）而且是该柱子的右边，所以要 + 1.

因此，完全覆盖第 index 个柱子的最大矩形的面积如下（stk 是单调栈）

```c++
maxArea=heights[index]*(i - (stk.top() +1))
```

还有一种情况。当 A 出栈后，单调栈为空时，那就是说明，A 的左边没有比它矮的。左边沿就可以到 0.

```c++
maxArea=heights[index]*(stk.empty()? i:(i - stk.top() -1)))
```

**代码：** 

```c++
class Solution {
    public:
    int largestRectangleArea(vector<int>& heights) {
        //单调栈的应用
        stack<int> s;
        heights.push_back(0);   //建立一个哨兵，让heights遍历完时，
                                //让栈s中的元素全部出栈,防止最后一块柱型不能计算面积
        int n = heights.size();
        if(!n) return 0;
        
        int maxArea=0;
        for(int i=0;i<n;++i)
        {
            while(!s.empty() && heights[i]<=heights[s.top()])
            {
                int h= s.top();s.pop();
                //s为空时表示左边没有比h更低的柱体了
                maxArea = max(maxArea,heights[h]*(s.empty()?i:(i-s.top()-1)));
            }
            s.push(i);
        }
        return maxArea;
    }
};
```

## [寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii)

**题目**

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

请找出其中最小的元素。

注意数组中可能存在重复的元素。

示例 1：
```
输入: [1,3,5]
输出: 1
```
示例 2：
```
输入: [2,2,2,0,1]
输出: 0
```

**思路**

数组中有重复元素，简单的用二分法无法达到搜索的目的。简单的二分法仅仅大于小于关系确定下一次搜索的范围，但是有重复元素之后，还存在等于关系，以至于无法达到二分的目的，**关键是在这种情况下怎么缩小搜索范围！**

参考以下分析：

+ 旋转排序数组nums可以被拆分为2个排序数组nums1, nums2，并且nums1所有元素>=nums2所有元素；

+ 因此，考虑二分法寻找值nums[i]；

+ 设置left, right指针在nums数组两端，mid为中点，比较mid与right对应元素大小（若比较mid与left的话，无法对`[1,2,3]`这样的例子做出正确判断）：
  + 当nums[mid] > nums[right]时，一定满足mid < i <= right，因此left = mid + 1；
  + 当nums[mid] < nums[right]时，一定满足left < i <= mid，因此right = mid；
  + 当nums[mid] == nums[right]时，**是此题对比153题的难点**（原因是此题中数组的元素可重复，相等就难以判断最小值的指针区间）；先说结果：采用right = right - 1(**关键是缩小搜索范围！**)，下面证明：
    + 首先，此操作不会使数组越界，因为right > left > 0；
    + 其次，此操作不会使最小值丢失，证明：假设'nums[right]'是最小值，有两种情况：
      + 若nums[right]是唯一最小值：那就不可能满足判断条件nums[mid] == nums[right]，因为left != right且mid = left + right // 2 < right（向下取整）；
      + 若有其他元素和nums[right]同为最小值：还有最小值存在于[left, right -1]间，不会丢失最小值。
  
+ 以上是理论分析，可以用以下数组辅助思考：

  + [1, 2, 3]

  + [1, 1, 0, 1]

  + [1, 0, 1, 1, 1]

  + [1, 1, 1, 1]

**代码**

```c++
class Solution {
public:
    int findMin(vector<int>& nums) {
        int left = 0,right = nums.size()-1;
        while(left<=right)
        {
            int mid = (left+right)/2;
            if(nums[mid]<nums[right])
                right = mid;
            else if(nums[mid]>nums[right])
                left = mid+1;
            else right= right-1;
        }
        return nums[left];
    }
};
```


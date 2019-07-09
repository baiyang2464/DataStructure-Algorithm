### 贪心算法

#### 贪心算法与动态规划联系和区别

**联系**

1.都是一种推导算法

2.都是分解成子问题来求解，都需要具有最优子结构

**区别**

1. 策略不同

贪心

【关键词】：从根问题出发，由上往下，短视策略

如果把所有的子问题看成一棵树的话，贪心从根出发，每次依据贪心策略向下遍历最优子树即可（通常这个“最优”都是基于当前情况下显而易见的“最优”）；这样的话，就不需要知道一个节点的所有子树情况，于是构不成一棵完整的树。因此**选对贪心策略**是关键。

动态规划

【关键词】：从子问题出发，由下往上，枚举所有情况

是从较小规模的子问题出发，检查所有子问题，得出当前问题的最优解，然后构造规模更大的子问题并找其最优解，最终得到的是全局最优解。动态规划本质是穷举法，可以保证结果是最佳的。动态规划以最小的子问题为叶子结点，构建了一棵完整的解答树。

2. 时间复杂度

贪心算法由于采用短视的策略，时间复杂度往往是一维的；

动态规划本质是穷举，复杂度较高。

3. 处理问题的规模

动态规划主要运用于二维或三维问题，而贪心一般是一维问题。

**例子**

+ 活动安排问题

问题：

设有n个活动的集合E＝{1，2，…，n}，其中每个活动都要求使用同一资源，如演讲会场等，而在同一时间内只有一个活动能使用这一资源。每个活动i都有一个要求使用该资源的起始时间`si`和一个结束时间`fi`，且`si＜fi`。如何选择活动安排时间以尽可能的安排更多的活动又使被选择的活动彼此不冲突？

贪心策略：

优先选择结束时间最早的活动，且保证活动时间不相交。

```c++
证明：设m是按贪心策略制定的安排的第一个活动，设i是最优安排的第一个活动。
显然m.endtime < i.endtime成立，所以用m替换i不会有什么影响
证明贪心策略的第一个活动可以是最优解中第一个
进而第二个，第三个，推论在局部优先选择最早时间结束的活动构成的方案，就是一个最优解。
```
我们做了什么短视的策略：

优先选择结束时间最早的活动，这样每次都可留给后面的活动尽可能多的时间。

+ 0-1背包问题：

 这个问题应**比较**选择该物品和不选择该物品**所导致的最终方案**，然后再作出最好选择，由此就导出许多互相重叠的子问题，所以用动态规划（穷举，穷举，穷举）。



#### [加油站](https://leetcode-cn.com/problems/gas-station/)

在一条环路上有 N 个加油站，其中第 i 个加油站有汽油 gas[i] 升。

你有一辆油箱容量无限的的汽车，从第 i 个加油站开往第 i+1 个加油站需要消耗汽油 cost[i] 升。你从其中的一个加油站出发，开始时油箱为空。

如果你可以绕环路行驶一周，则返回出发时加油站的编号，否则返回 -1。

说明: 

+ 如果题目有解，该答案即为唯一答案。

+ 输入数组均为非空数组，且长度相同。

+ 输入数组中的元素均为非负数。

示例 1:

```
输入: 
gas  = [1,2,3,4,5]
cost = [3,4,5,1,2]

输出: 3

解释:
从 3 号加油站(索引为 3 处)出发，可获得 4 升汽油。此时油箱有 = 0 + 4 = 4 升汽油
开往 4 号加油站，此时油箱有 4 - 1 + 5 = 8 升汽油
开往 0 号加油站，此时油箱有 8 - 2 + 1 = 7 升汽油
开往 1 号加油站，此时油箱有 7 - 3 + 2 = 6 升汽油
开往 2 号加油站，此时油箱有 6 - 4 + 3 = 5 升汽油
开往 3 号加油站，你需要消耗 5 升汽油，正好足够你返回到 3 号加油站。
因此，3 可为起始索引。
```
示例 2:
```
输入: 
gas  = [2,3,4]
cost = [3,4,3]

输出: -1

解释:
你不能从 0 号或 1 号加油站出发，因为没有足够的汽油可以让你行驶到下一个加油站。
我们从 2 号加油站出发，可以获得 4 升汽油。 此时油箱有 = 0 + 4 = 4 升汽油
开往 0 号加油站，此时油箱有 4 - 3 + 2 = 3 升汽油
开往 1 号加油站，此时油箱有 3 - 3 + 3 = 3 升汽油
你无法返回 2 号加油站，因为返程需要消耗 4 升汽油，但是你的油箱只有 3 升汽油。
因此，无论怎样，你都不可能绕环路行驶一周。
```

**（一）暴力法**

检查每一个加油站：

+ 选择该加油站为出发站

+ 模拟汽车环路行驶，在每一个加油站检查我们还剩多少升汽油。看最终能否循环一圈。

这意味着 O(N^2)的时间复杂度。

每次选择一个新的加油站作为出发站，然后检查到之后的加油站还剩多少油，这里存在重复计算，要是选择新出发站时不覆盖之前检查过的加油站就好了！



**（二）只遍历一次数组的算法**

**首先注意两件事情**

+ 如果 `sum(gas) < sum(cost)` ，那么不可能环行一圈，这种情况下答案是 `-1` 。

+ 对于加油站 `i` ，如果 `gas[i] - cost[i] < 0` ，则不可能从这个加油站出发，因为在前往 `i + 1` 的过程中，汽油就不够了。

所以设置`total_tank = sum(gas) - sum(cost)` ，如果 `total_tank < 0`则返回 `-1` 。

引入变量 `curr_tank` ，记录从出发站到当前加油站时油箱里剩余的总油量。如果在某一个加油站 `curr_tank`比 `0` 小，意味着我们无法到达这个加油站。

**算法**

那么现在算法是很直接明了的：

1. 初始化 `total_tank` 和 `curr_tank` 为 0 ，并且选择 0 号加油站为起点。

2. 遍历所有的加油站：
   1. 每一步中，都通过加上 `gas[i] `和减去` cost[i]` 来更新` total_tank `和` curr_tank` 。
   2. 如果在` i + 1 `号加油站， `curr_tank < 0 `，将 `i + 1 `号加油站作为新的起点，同时重置 `curr_tank = 0 `，让油箱也清空。

3. 如果` total_tank < 0` ，返回` -1 `，否则返回 `starting station`。

**贪心策略**

<p align="center">
	<img src=./pictures/gas_station.PNG alt="Sample"  width="700">
	<p align="center">
		<em>加油站问题</em>
	</p>
</p>


每次我们选择出发站时只保证能走完剩余的路就好了，即只考虑新起点能否到达n-1号站，不考虑n-号站经0号站能否再返回新起点，这是由于引入了`curr_tank`得以完成；

又由于我们记录了`total_tank`，当`total_tank>0`则坑定存在能循环一圈的起点，当起点至n-1号站的`curr_tank>0`时，即还有盈余的油，其实`curr_tank>0`就表示行驶一段距离后盈余的油，`curr_tank>0`表示这段路还需要的油耗；

右图片可以看出新起点到n-1号站剩余的油一定可以cover从n-1号站到新起点需要的油耗。所以可以得到全局最优解。

**证明**

[看官方证明吧](<https://leetcode-cn.com/problems/gas-station/solution/jia-you-zhan-by-leetcode/>)



#### [分发糖果](https://leetcode-cn.com/problems/candy/)

**题目**

老师想给孩子们分发糖果，有 N 个孩子站成了一条直线，老师会根据每个孩子的表现，预先给他们评分。

你需要按照以下要求，帮助老师给这些孩子分发糖果：

每个孩子至少分配到 1 个糖果。
相邻的孩子中，评分高的孩子必须获得更多的糖果。
那么这样下来，老师至少需要准备多少颗糖果呢？

```
示例 1:

输入: [1,0,2]
输出: 5
解释: 你可以分别给这三个孩子分发 2、1、2 颗糖果。
示例 2:

输入: [1,2,2]
输出: 4
解释: 你可以分别给这三个孩子分发 1、2、1 颗糖果。
     第三个孩子只得到 1 颗糖果，这已满足上述两个条件。
```

**思路**

题本身可以用贪心法来做，我们用candy[n]表示每个孩子的糖果数，遍历过程中，

如果孩子i的rate大于左边孩子i-1 的rate，那么当前最好的选择自然是：给孩子i的糖果数=给孩子i-1的糖果数+1（且不需要判断是否candy[i+1]<=candy[i]，这是因为在每次更新前，当前学生的糖果数一定小于等于他左邻居的糖果数。）

如果孩子i的rate小于等于右边孩子i+1 的rate怎么办？这个时候就不大好办了，因为我们不知道当前最好的选择是给孩子i+1多少糖果。

**解决方法是：暂时不处理这种情况。等数组遍历完了，我们再一次从尾到头遍历数组（贪心策略）**

这回逆过来贪心，就可以处理之前略过的孩子。比较i与其右i+1，当ratings[i]>ratings[i+1]，则给i的糖要比i-1的多，因为有些i已经得到了更多的糖，所以要比较candy[i]与candy[i+1]+1的关系，小于时要candy[i]=candy[i+1]+1

最后累加candy[n]即得到最小糖果数。

```c++
class Solution {
public:
    int candy(vector<int>& ratings) {
        int n = ratings.size();
        vector<int> tmp(n,1);
        int sum=0;
        for(int i=1;i<n;++i)
            if(ratings[i]>ratings[i-1])
                tmp[i]=tmp[i-1] +1;

        for(int i=n-2;i>=0;--i)
            if(ratings[i]>ratings[i+1] && tmp[i]<=tmp[i+1])
                tmp[i]=tmp[i+1] +1;

        for(auto num:tmp)
            sum+=num;
        return sum;
    }
};
```

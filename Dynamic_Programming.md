### 动态规划概念

动态规划的思想了：动态规划（Dynamic Programming）是一种分阶段求解决策问题的数学思想。总结起来就是一句话，大事化小，小事化了。

+ 常见的动态规划问题

线性动规：拦截导弹，合唱队形，挖地雷，建学校，剑客决斗等；

区域动规：石子合并， 加分二叉树，统计单词个数，炮兵布阵等；

树形动规：贪吃的九头龙，二分查找树，聚会的欢乐，数字三角形等；

背包问题：01背包问题，完全背包问题，分组背包问题，二维背包，

### 0-1背包问题

有N件物品和一个容量为V的背包。第i件物品的价格（即体积，下同）是w[i]，价值是c[i]。求解将哪些物品装入背包可使这些物品的费用总和不超过背包容量，且价值总和最大。

这是最基础的背包问题，总的来说就是：选还是不选，这是个问题

相当于用`f[i][j]`​表示考虑前​`i​`个物品装入容量为`j`的背包中所可以获得的最大价值。

对于一个物品，只有两种情况

　　情况一: 第i件不放进去，这时所得价值为:`f[i-1][j]`

　　情况二: 第i件放进去，这时所得价值为：`f[i-1][j-c[i]]+w[i] `

状态转移方程为：

![](./pictures/mylatex20190624_145740.png)

```c
#include<stdio.h>
#include<algorithm>
using namespace std;
int f[1001][1001];
int main()
{
    int m, n,c[10001], w[10001];
     scanf("%d%d", &m, &n);					//m是物品种类，n是背包能承载的重量
     for(int i = 1; i <= m; i++)
       scanf("%d%d", &w[i], &c[i]);
     for(int i = 1; i <= m; i++)
     {
         for(int j = 0; j <= n; j++)
         {
             f[i][j] = f[i-1][j];
             if(j>=w[i])
           	 	f[i][j] = max(f[i][j], f[i-1][j-w[i]]+c[i]);
         }
     }
     printf("%d", f[m][n]);
     return 0;  
}
```

还可以进行空间优化

设 `f[v]`表示背包还可以放入`v`公斤物品时候能得到的最大价值（若背包最大能承受`n`重量，`f(n)`表示未放入任何物品时候的价值）， 则状态转移方程：`f[v]=max(f[v]，f[v-w[i]]+c[i]) `，当v>=w[i]，1<=i<=n 。

```c
#include<stdio.h>
#include<algorithm>
using namespace std;
const int maxm = 2001, maxn = 101;
int m, n;
int w[maxn], c[maxn];
int f[maxm]; 
int main()
{
    scanf("%d%d",&m, &n);            		//背包容量m和物品数量n
    for (int i=1; i <= n; i++)
        scanf("%d%d",&w[i],&c[i]);     		//每个物品的重量和价值
    for (int i=1; i <= n; i++)            	//设f(v)表示重量不超过v公斤的最大价值
        for (int v = m; v >= w[i]; v--)  	//注意是逆序
            f[v] = max(f[v-w[i]]+c[i], f[v]);
    printf("%d\n",f[m]);                    // f(m)为最优解
    return 0;
}
```



### 完全背包问题

有N种物品和一个容量为V的背包，每种物品都有无限件可用。第i种物品的费用是w[i]，价值是c[i]。求解将哪些物品装入背包可使这些物品的费用总和不超过背包容量，且价值总和最大。

完全背包和01背包十分相像， 区别就是完全背包物品有无限件。由之前的选或者不选转变成了选或者不选，选几件。

和0-1背包一样，有状态转移方程：

![](./pictures/mylatex20190624_152228.png)

```cpp
#include<stdio.h>
#include<algorithm>
using namespace std;
const int maxm=2001,maxn=101;
int n,m,v,i;
int c[maxn],w[maxn];
int f[maxm];
int main()
{
    scanf("%d%d",&m,&n);           		 //背包容量m和物品数量n
    for(i=1;i<=n;i++) 
        scanf("%d%d",&w[i],&c[i]);
    for(i=1;i<=n;i++)
        for(v=w[i]; v<=m; v++)          //设 f[v]表示重量不超过v公斤的最大价值
                                        //这里是v++ 顺序 区别于01背包 
            f[v]=max(f[v-w[i]]+c[i], f[v]);
    printf("%d\n", f[m]);           	// f[m]为最优解
    return 0;
}
```



### 多重背包问题

有N种物品和一个容量为V的背包。第i种物品最多有`n[i]`件可用，每件费用是`w[i]`，价值是`c[i]`。求解将哪些物品装入背包可使这些物品的费用总和不超过背包容量，且价值总和最大。

这里又多了一个限制条件，每个物品规定了可用的次数。

同理，可以得出状态转移方程：

![](./pictures/mylatex20190624_152655.png)

一道例题

【问题描述】

为了庆贺班级在校运动会上取得全校第一名成绩，班主任决定开一场庆功会，为此拨款购买奖品犒劳运动员。期望拨款金额能购买最大价值的奖品，可以补充他们的精力和体力。

【输入格式】

第一行二个数n(n<=500)，m(m<=6000)，其中n代表希望购买的奖品的种数，m表示拨款金额。 接下来n行，每行3个数，v、w、s，分别表示第I种奖品的价格、价值（价格与价值是不同的概念）和购买的数量（买0件到s件均可），其中v<=100，w<=1000，s<=10。

【输出格式】

第一行：一个数，表示此次购买能获得的最大的价值（注意！不是价格）。

【输入样例】

5 1000

80 20 4

40 50 9

30 50 7

40 30 6

20 20 1

【输出样例】

1040

```c
#include<stdio.h>
#include<algorithm>
using namespace std;
int v[6002], w[6002], s[6002];
int f[6002];
int n, m;
int main()
{
    scanf("%d%d",&n,&m);
    for (int i = 1; i <= n; i++)
        scanf("%d%d%d",&v[i],&w[i],&s[i]);
    for (int i = 1; i <= n; i++)
       for (int j = m; j >= 0; j--)
          for (int k = 0; k <= s[i]; k++)
          {
               if (j-k*v[i]<0) break;
               f[j] = max(f[j],f[j-k*v[i]]+k*w[i]);
          }
    printf("%d\n",f[m]);
    return 0;
}
```


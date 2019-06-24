

## [扰乱字符串](https://leetcode-cn.com/problems/scramble-string/)

**题目：**
[点击此处查看](https://leetcode-cn.com/problems/scramble-string/)

### 解法一

简单的说，就是s1和s2是scramble的话，那么必然存在一个在s1上的长度l1，将s1分成s11和s12两段，同样有s21和s22.那么要么s11和s21是scramble的并且s12和s22是scramble的；要么s11和s22是scramble的并且s12和s21是scramble的。

```cpp
class Solution {
 public:
	 bool isScramble(string s1, string s2) {
	 //递归退出条件
		 if (s1.size() != s2.size())return false;
		 if (s1 == s2)return true;
		 
		 string str1 = s1; string str2 = s2;
		 sort(str1.begin(),str1.end());
		 sort(str2.begin(),str2.end());
		 if (str1 != str2)return false;
		 for (int i = 1;i<s1.size();i++)
		 {
			 string s11 = s1.substr(0, i);
			 string s12 = s1.substr(i);
			 string s21 = s2.substr(0, i);
			 string s22 = s2.substr(i);
			 if (isScramble(s11, s21) && isScramble(s12, s22))return true;
			 s21 = s2.substr(s2.size() - i);
			 s22 = s2.substr(0, s2.size() - i);
			 if (isScramble(s11, s21) && isScramble(s12, s22))return true;
		 }
		 return false;
	 }
 };
```

### 解法二

三维动态规划

我们提出维护量$dp[i][j][n]$，其中i是s1的起始字符，j是s2的起始字符，而n是当前的字符串长度，$dp[i][j][len]$表示的是以i和j分别为s1和s2起点的长度为len的字符串是不是互为scramble。

有了维护量我们接下来看看递推式，也就是怎么根据历史信息来得到$dp[i][j][len]$。判断这个是不是满足，其实我们首先是把当前$s1[i…i+len-1]$字符串劈一刀分成左右两部分，然后分两种情况：

+ 第一种是左边和$s2[j…j+len-1]$左边部分是不是scramble，以及右边和$s2[j…j+len-1]$右边部分是不是scramble；
+ 第二种情况是左边和$s2[j…j+len-1]$右边部分是不是scramble，以及右边和$s2[j…j+len-1]$左边部分是不是scramble。

如果以上两种情况有一种成立，说明$s1[i…i+len-1]$和$s2[j…j+len-1]$是scramble的。而对于判断这些左右部分是不是scramble我们是有历史信息的，因为长度小于n的所有情况我们都在前面求解过了（也就是长度是最外层循环）。
上面说的是劈一刀的情况，对于$s1[i…i+len-1]$我们有$len-1$种劈法，在这些劈法中只要有一种成立，那么两个串就是scramble的。
总结起来递推式是
![Alt text](C:/Users/11060/Desktop/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E9%A2%98%E7%9B%AE/1560674039214.png)

对于所有$1<=k<len$，也就是对于所有$len-1$种劈法的结果求或运算。因为信息都是计算过的，对于每种劈法只需要常量操作即可完成，因此求解递推式是需要$O(len)$（因为len-1种劈法）。
如此总时间复杂度因为是三维动态规划，需要三层循环，加上每一步需要线行时间求解递推式，所以是$O(n^4)$。虽然已经比较高了，但是至少不是指数量级的，动态规划还是有很大优势，空间复杂度$O(n^3)$。
 代码如下：

```cpp
 class Solution {
public:
    bool isScramble(string s1, string s2) {
        //dp[i][j][len] s1中以i为地点，s2中以j为起点，长度为len的子串是否是扰乱字符串
        if(s1.length()!=s2.length()) return false;
        if(s1==s2) return true;
        int n = s1.length();
        //为什么dp的第三个维度是n+1，因为第三个维度表示长度，长度应该是1~n
        vector<vector<vector<bool>>> dp(n,vector<vector<bool>>(n,vector<bool>(n+1,false)));
        for(int i=0;i<n;++i)
            for(int j=0;j<n;++j)
                dp[i][j][1] = (s1[i]==s2[j]);
        
        for(int len=2;len<=n;++len)//len最大应是n才有意义
            for(int i=0;i<=n-len;++i)
                for(int j=0;j<=n-len;++j)
                    for(int k=1;k<len;++k)
                    {
                        if(dp[i][j][k] && dp[i+k][j+k][len-k] || dp[i][j+len-k][k] && dp[i+k][j][len-k])
                            dp[i][j][len] = true;
                    }
        return dp[0][0][n];
    }
};
```
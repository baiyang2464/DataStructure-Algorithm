#### [比较版本号](https://leetcode-cn.com/problems/compare-version-numbers/)

**代码**

比较两个版本号 version1 和 version2。
如果` version1 > version2 `返回 1，如果 `version1 < version2 `返回 -1， 除此之外返回 0。

你可以假设版本字符串非空，并且只包含数字和 `. `字符。

 `. `字符不代表小数点，而是用于分隔数字序列。

例如，`2.5` 不是“两个半”，也不是“差一半到三”，而是第二版中的第五个小版本。

你可以假设版本号的每一级的默认修订版号为` 0`。例如，版本号 `3.4 `的第一级（大版本）和第二级（小版本）修订号分别为 `3 `和 `4`。其第三级和第四级修订号均为` 0`。


示例 1:
```
输入: version1 = "0.1", version2 = "1.1"
输出: -1
```
示例 2:
```
输入: version1 = "1.0.1", version2 = "1"
输出: 1
```
示例 3:
```
输入: version1 = "7.5.2.4", version2 = "7.5.3"
输出: -1
```
示例 4：
```
输入：version1 = "1.01", version2 = "1.001"
输出：0
解释：忽略前导零，“01” 和 “001” 表示相同的数字 “1”。
```
示例 5：
```
输入：version1 = "1.0", version2 = "1.0.0"
输出：0
解释：version1 没有第三级修订号，这意味着它的第三级修订号默认为 “0”。
```

​	**思路(1)**

你的思路是在原来的两个字符串上把`.`分割的子串截取出来单独比较子串的大小，同时若子串以0开头还会压缩子串（去掉开头的0），若不能比较当前子串大小，就递归比较剩余串的大小。

这个做法比较繁琐（你花了一下午做出来的），特别是在处理一个一个字符串遍历完毕，另一个字符串还没遍历完毕（也可能遍历完毕）的情况，写了好多bug，很多没考虑清楚。

```c++
class Solution {
public:
    int compareVersion(string version1, string version2) {
        if(version1.empty() && version2.empty()) return 0;
        int right1=0,right2=0;
        int left1=0,left2=0;
        string str1,str2;
        while(right1<version1.length() && version1[right1]!='.') ++right1;
        while(right2<version2.length() && version2[right2]!='.') ++right2;
        while(left1<right1 && version1[left1]=='0') ++left1;
        while(left2<right2 && version2[left2]=='0') ++left2;
        
        str1= version1.substr(left1,right1-left1);
        str2= version2.substr(left2,right2-left2);
        int res = compare(str1,str2);
        if(res!=0) return res;
        if(right1!=version1.length()) right1++;
        else if(right2!=version2.length())
        {
            while(right2!=version2.length() && version2[right2]=='.' ||version2[right2]=='0') ++right2;
            if(right2!=version2.length()) return -1;
            else return 0;
        }
        if(right2!=version2.length()) right2++;
        else if(right1!=version1.length())
        {
            while(right1!=version1.length() && version1[right1]=='.'||version1[right1]=='0') ++right1;
            if(right1!=version1.length()) return 1;
            else return 0;
        }
        return compareVersion(version1.substr(right1),version2.substr(right2));
    }
    int compare(string &str1,string &str2)
    {
        int len1= str1.length();
        int len2= str2.length();
        
        if(len1<len2) return -1;
        if(len1>len2) return 1;
        int i=0;
        for(;i<len1&&i<len2;++i)
        {
            if(str1[i]<str2[i]) return -1;
            if(str1[i]>str2[i]) return 1;
        }
        if(i<len1) return 1;
        else if(i<len2) return -1;
        return 0;
    }
};
```

​	**思路(2)**

利用C++的istringstream可以将字符串按某个格式切割然后分段转换成int的特点（也可转其它类型），简化分割操作及比较大小操作。同时处理一个字符串遍历完毕另一个字符串还有的情况也很巧妙。

```c++
class Solution {
public:
    int compareVersion(string v1, string v2) {
        char c;
        int n1,n2;
        istringstream s1(v1);
        istringstream s2(v2);
        while(bool(s1>>n1)+bool(s2>>n2))//bool(s1>>n1)还有数字输出就返回1，否则返回0，以此判断两个字符串是否比较完
       {
            cout<<n1<<" "<<n2<<endl;
            if(n1>n2)return 1;
            else if(n1<n2)return -1;
            n1=0,n2=0;
            s1>>c;
            s2>>c;
        }
        return 0;
    }
};
```

**思路（3）**

不用istringstream也可以做，只是转换为整形比较时候用到了经典的字符串转整形的方法。

```c
int compareVersion(char* version1, char* version2) {
    for(int i = 0,j = 0;i < strlen(version1) || j < strlen(version2);){
        int v1 = 0,v2 = 0;
        while(i < strlen(version1) && version1[i] != '.'){
            v1 = v1 * 10 + (version1[i] - '0');
            i++;
        }
        i++;
        while(j < strlen(version2) && version2[j] != '.'){
            v2 = v2 * 10 + (version2[j] - '0');
            j++;
        }
        j++;
        if(v1 > v2)
            return 1;
        if(v1 < v2)
            return -1;
    }
    return 0;
}
```


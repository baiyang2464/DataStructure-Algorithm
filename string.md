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



#### [单词接龙II](https://leetcode-cn.com/problems/word-ladder-ii/)

给定两个单词（beginWord 和 endWord）和一个字典 wordList，找出所有从 beginWord 到 endWord 的最短转换序列。转换需遵循如下规则：

1. 每次转换只能改变一个字母。

2. 转换过程中的中间单词必须是字典中的单词。

说明:

+ 如果不存在这样的转换序列，返回一个空列表。
+ 所有单词具有相同的长度。
+ 所有单词只由小写字母组成。
+ 字典中不存在重复的单词。
+ 你可以假设 beginWord 和 endWord 是非空的，且二者不相同。

示例 1:

```
输入:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

输出:
[
  ["hit","hot","dot","dog","cog"],
  ["hit","hot","lot","log","cog"]
]
```
示例 2:
```
输入:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]

输出: []

解释: endWord "cog" 不在字典中，所以不存在符合要求的转换序列。
```

**思路**

(1)思路一

此题目时间要求比较严格，且要输出所有最短的路径序列，最开始思路是用DFS一层一层的遍历+单调栈找找最短的路径

单调栈找最短路径：

+ 若当前路径长度小于栈顶路径长度，则就一直出栈，
+ 若栈空或者当前路径等于栈顶路径长度，将当前路径进栈
+ 若当前路径大于栈顶路径长度，则丢弃当前路径

但是由于DFS及栈操作，超时了

（2）思路二

查看评论区广度优先搜索的方法，描述比较复杂，可以看代码注释

且比较邪乎的是，下面的代码是我改了几个变量名，就显示运行超时

**大体思路是**：从左右两边同时开工进行层次遍历（也可只从一编遍历），找`起始结点`和`末端结点`的各自的`邻接结点`，记录遍历过的结点，下一次只在没有遍历过的结点中找邻接结点，当两个搜索域相交时候就表示找到了可从`起始结点`到`末端结点`的路径，由于是层次遍历，所以可以保证第一次相遇时，凑成最短路径。

```python
class Solution:
    def findLadders(self, beginWord: str, endWord: str, wordList: List[str]) -> List[List[str]]:
        from collections import defaultdict
        if endWord not in wordList:return []
        curAdjNodes,anoAdjNodes,wordList,dic={beginWord},{endWord},set(wordList),defaultdict(set)
        letters,flag = set('abcdefghijklmnopqrstuvwxzy'),True
        while curAdjNodes:
            if len(curAdjNodes)>len(anoAdjNodes): 
                curAdjNodes,anoAdjNods,flag = anoAdjNodes,curAdjNodes,not flag #交换forw,back,取返flag，
                                                                               #交换广度遍历的方向
            wordList-=curAdjNodes		#将wordList中除掉已经遍历过的单词
            cur = set()          		#cur装此次遍历发现的邻接单词
            for word in curAdjNodes:
                for i in range(len(word)): #用a~z的每个字母来替换word中的一个字母，产生一个新单词
                    for letter in letters:
                        nextWord = word[0:i] + letter + word[i+1:]
                        if nextWord in wordList:
                            cur.add(nextWord)
                            if flag:dic[nextWord].add(word) #dic存放从前序遍历时，当前邻接单词的前一个单词
                            else:dic[word].add(nextWord)    #从后序遍历时候，顺序交换下
            if cur & anoAdjNodes:      #两个set有交集 s1={1,2,3},s2={3,4,5} ,s1 & s2 就为{3}
                res = [[endWord]]      #若两个set相交时候，表示从前遍历和从后遍历可以组合成一个完整的最短的路径
                while res[0][0] != beginWord:
                    res = [[x]+y for y in res for x in dic[y[0]]]
                return res 
            curAdjNodes= cur
        return []
```

这是可以通过的广度优先遍历的代码（只是和上面代码部分变量名不一样）

```python
class Solution:
    def findLadders(self, beginWord: str, endWord: str, wordList: List[str]) -> List[List[str]]:
        from collections import defaultdict
        if endWord not in wordList:return []
        forward,backward,wordList,dic={beginWord},{endWord},set(wordList),defaultdict(set)
        letters,flag = set('abcdefghijklmnopqrstuvwxzy'),True
        while forward:
            if len(forward)>len(backward): 
                forward,backward,flag = backward,forward,not flag #交换forw,back,取返flag，交换广度遍历的方向
            wordList-=forward 			#将wordList中除掉已经遍历过的单词
            cur = set()      			#cur装此次遍历发现的邻接单词
            for word in forward:
                for i in range(len(word)):  #用a~z的每个字母来替换word中的一个字母，产生一个新单词
                    for letter in letters:
                        nextWord = word[0:i] + letter + word[i+1:]
                        if nextWord in wordList:
                            cur.add(nextWord)
                            if flag:dic[nextWord].add(word) #dic存放从前序遍历时，当前邻接单词的前一个单词
                            else:dic[word].add(nextWord)    #从后序遍历时候，顺序交换下
            if cur & backward:      #两个set有交集 s1={1,2,3},s2={3,4,5} ,s1 & s2 就为{3}
                res = [[endWord]]   #若两个set相交时候，表示从前遍历和从后遍历可以组合成一个完整的最短的路径
                while res[0][0] != beginWord:
                    res = [[x]+y for y in res for x in dic[y[0]]]
                return res 
            forward = cur
        return []
```



#### [翻转字符串里的单词](https://leetcode-cn.com/problems/reverse-words-in-a-string/)

给定一个字符串，逐个翻转字符串中的每个单词。

示例 1：
```
输入: "the sky is blue"
输出: "blue is sky the"
```
示例 2：
```
输入: "  hello world!  "
输出: "world! hello"
解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
```
示例 3：
```
输入: "a good   example"
输出: "example good a"
解释: 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。
```

这道题很简单，

**python方法——熟悉一些操作**

```python
class Solution:
    def reverseWords(self, s: str) -> str:
        return " ".join(s.split()[::-1])
```

join()方法语法：

```python
str.join(sequence)
```

+ sequence -- 要连接的元素序列。
+ 返回通过指定字符连接序列中元素后生成的新字符串。

字符串翻转方法

```python
str[::-1]
```

**c语言方法**

要找到每个单词，将单词翻转，然后再将整个字符串翻转一次（当然还有空格符要处理）就得到结果



#### [最短回文串](https://leetcode-cn.com/problems/shortest-palindrome/)

给定一个字符串 s，你可以通过在字符串前面添加字符将其转换为回文串。找到并返回可以用这种方式转换的最短回文串。

示例 1:
```
输入: "aacecaaa"
输出: "aaacecaaa"
```
示例 2:
```
输入: "abcd"
输出: "dcbabcd"
```

（一）类似KMP算法的方法

**[算法](<https://leetcode-cn.com/problems/shortest-palindrome/solution/zui-duan-hui-wen-chuan-by-leetcode/>)**

+ 利用 KMP 算法的部分匹配表声场算法——找末端回文子串从字符串开始的最长前缀。
+ 创建新字符 s = s + "#" + reverse(s)，并使用该字符串进行部分匹配表生成。
  + 中间的 "#" 是必要的。如果没有#，两个字符串可能会发生混淆，造成错误的答案。举例而言，字符串 “aaaa”。如果不在中间插入 "#"，新字符串会是 “aaaaaaaa“， 最长前缀大小会成为 7，这显然是错的。因此，中间的分隔符是必要的。

+ 返回最大回文子串后的子串的反转（长度为 n-f[n_new-1]）+ 原字符串 s

```c++
class Solution {
public:
    string shortestPalindrome(string s) {
        int n = s.length();
        string so= s;
        string sr =s;
        reverse(sr.begin(),sr.end());
        s = s +"#"+ sr;            
        n = s.length();
        /*prefixLen[i]保存s[i]处，可与从s头部开始形成的最长前缀的长度*/
        vector<int> prefixLen(n,0);
                                   
        for(int i=1;i<n;++i)
        {
            int k = prefixLen[i-1];//当前位置的前缀长度，可从前一个位置的前缀长度基础上产生
            /*长度k转为下标时，刚好是与当前位置比较的位置*/
            while(k>0 && s[k]!=s[i])//若k长的前缀不匹配，则缩短前缀长度再来匹配
                k = prefixLen[k-1];
            if(s[k]==s[i])
                ++k;//长度和下标转换要加1
            prefixLen[i]=k;
        }
        return sr.substr(0,sr.length()-prefixLen[n-1]) + so;
    }
};
```

（二）找首端最长回文子串的方法

[最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)
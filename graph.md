### 图的存储方式

+ 邻接表



+ 邻接矩阵



### 图的遍历方式

+ 深度优先遍历

```c++
//图以邻接矩阵表示
//无环图
int visited[n] = {0};
void dfs(int *graph,int v,int n)
{
    visited[v]=1;
    for(int i=0;i<n;++i)
    {
        if(!visited[i])
            dfs(graph,i,n);
    }
}
```



+ 广度优先遍历



### 图的经典问题

+ 最小生成树

+ 最短路径算法
+ 拓扑排序

应用于判断有向图是否有环

由AOV网构造拓扑序列的拓扑排序算法主要是循环执行以下两步，直到不存在入度为0的顶点为止。

(1) 选择一个入度为0的顶点并输出之；

(2) 从网中删除此顶点及所有出边。

[![img](https://gss3.bdstatic.com/-Po3dSag_xI4khGkpoWK1HF6hhy/baike/s%3D220/sign=2d60fa290133874498c5287e610fd937/adaf2edda3cc7cd9565490a03401213fb80e914a.jpg)](https://baike.baidu.com/pic/拓扑排序/5223807/0/adaf2edda3cc7cd9565490a03401213fb80e914a?fr=lemma&ct=single)

循环结束后，若输出的顶点数小于网中的顶点数，则输出“有回路”信息，否则输出的顶点序列就是一种拓扑序列。



### 相关问题








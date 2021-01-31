---
title: 迪杰斯特拉（dijkstra）算法的C++实现
date: 2021-01-31 19:29:10
tags:
  - 数据结构与算法
categories:
  - 学习笔记
---
# 简介
迪杰斯特拉算法是由荷兰计算机科学家艾兹赫尔·戴克斯特拉在1956年发现的、使用类似广度优先搜索的方法解决赋权图的单源最短路径问题的算法。
# 算法实现
迪杰斯特拉算法主要有两种实现方式。两种实现方式的不同在于图的表示方法，分别为邻接矩阵法与邻接表法。
<!-- more -->
## 邻接矩阵法
```cpp
/**
输入参数
n：       节点数量
s：       起点编号
G：       图的邻接矩阵，G[m][n]表示节点m到节点n的权重，若G[m][n]=INT_MAX，表示m与n之间无连接
visited： 记录某个节点是否已经被访问
d：       记录从起点出发到各个节点的最短路径。除了起点之外，均初始化为INT_MAX
pre：     记录达到某个节点之前经过的节点
*/
void dijkstra(int n, int s, vector<vector<int>> G, vector<bool>& visited, vector<int>& d, vector<int>& pre) {
    for (int i = 0; i < n; ++i) pre[i] = i; // 前一个节点初始化为自身
    d[s] = 0; // 起点到达它自身的最短路径为0
    // 一共需要操作n次
    for (int i = 0; i < n; ++i) {
        int node = -1, min_dist = INT_MAX;
        // 选出本次要被操作的节点，选取要求是没有被访问过且距离起点的距离最小
        for (int j = 0; j < n; ++j) {
            if (!visited[j] && d[j] < min_dist) {    
                node = j;
                min_dist = d[j];
            }
        }
        if (node == -1) return; // 如果没有可以操作的节点了，直接返回
        visited[node] = true; // 把当前要操作的节点设置为已经访问
        // 对于选到的节点，更新经过它到达其他节点时，其他节点距离起点的最短路径
        for (int j = 0; j < n; ++j) {
            if (!visited[j] && d[node] + (long)G[node][j] < (long)d[j]) {
                d[j] = d[node] + G[node][j];
                pre[j] = node;
            }
        }
    }
}
```
## 邻接表法
```cpp
/**
比较函数，用于后面出现的小顶堆
*/
struct cmp {
    bool operator()(pair<int, int>& p1, pair<int, int>& p2) {
        return p1.first > p2.first;
    }
};

/**
输入参数
n：       节点数量
s：       起点编号
G：       图的邻接表，G[m]是与节点m相连接的节点列表，每个节点用pair<int, int>表示，pair的第一个元素为节点编号，第二个元素为与节点相连的边的权重
visited： 记录某个节点是否已经被访问
d：       记录从起点出发到各个节点的最短路径。除了起点之外，均初始化为INT_MAX
pre：     记录达到某个节点之前经过的节点
*/
void dijkstra2(int n, int s, vector<vector<pair<int, int>>>& G, vector<bool>& visited, vector<int>& d, vector<int>& pre) {
    for (int i = 0; i < n; ++i) d[i] = (i == s ? 0 : INT_MAX);
    priority_queue<pair<int, int>, vector<pair<int, int>>, cmp> Q; //初始化一个小顶堆，小顶堆的比较基准是两个不同节点到起点的距离大小
    Q.push(make_pair(d[s], s)); // 初始状态下，压入起点
    while (!Q.empty()) {
        // 选取本次操作的节点，选取要求是没有被访问过且距离起点的距离最小
        int node = Q.top().second;
        Q.pop();
        if (visited[node]) continue;
        visited[node] = true; // 把选取到的节点标记为已经访问
        // 对于正在操作的节点，遍历它的邻接点，更新经过它到达邻接点时，邻接点距离起点的最短路径
        for (int i = 0; i < G[node].size(); ++i) {
            int neighbor = G[node][i].first; // 邻接点的编号
            int neighborD = G[node][i].second; // 正在操作的节点距离邻接点的距离
            if (neighborD + d[node] < d[neighbor]) { // 判断邻接点距离起点是否有更小的路径，若有则更新
                d[neighbor] = neighborD + d[node];
                pre[neighbor] = node;
                Q.push(make_pair(d[neighbor], neighbor));
            }
        }
    }
}
```
# 参考资料
* [戴克斯特拉算法- 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E6%88%B4%E5%85%8B%E6%96%AF%E7%89%B9%E6%8B%89%E7%AE%97%E6%B3%95)
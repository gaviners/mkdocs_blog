# LeetCode: 802. 找到最终的安全状态

[TOC]

## 1、题目描述

在有向图中, 我们从某个节点和每个转向处开始, 沿着图的有向边走。 如果我们到达的节点是终点 (即它没有连出的有向边), 我们停止。

现在, 如果我们最后能走到终点，那么我们的起始节点是最终安全的。 更具体地说, 存在一个自然数 K,  无论选择从哪里开始行走, 我们走了不到 K 步后必能停止在一个终点。

哪些节点最终是安全的？ 结果返回一个有序的数组。

该有向图有 N 个节点，标签为 0, 1, ..., N-1, 其中 N 是 graph 的节点数.  图以以下的形式给出: graph[i] 是节点 j 的一个列表，满足 (i, j) 是图的一条有向边。

```
示例：
输入：graph = [[1,2],[2,3],[5],[0],[5],[],[]]
输出：[2,4,5,6]
这里是上图的示意图。
```

![](http://markdown-images-1251766755.cos.ap-beijing.myqcloud.com/notebook/2019-09-19-051008.png)

**提示：**

- graph 节点数不超过 10000.

- 图的边数不会超过 32000.

- 每个 graph[i] 被排序为不同的整数列表， 在区间 [0, graph.length - 1] 中选取。

## 2、解题思路

- 首先找到出度为0的节点假如结果集
- 然后遍历其他节点，找出其所有的下一条边都指向结果集中的节点，满足条件则加入结果集中

```python
class Solution:
    def eventualSafeNodes(self, graph: List[List[int]]) -> List[int]:
        from collections import defaultdict

        res = set()
        not_decide = set(list(range(len(graph))))
        flag = True

        in_degree = defaultdict(set)

        for index, l in enumerate(graph):
            if not l:
                res.add(index)
                not_decide.remove(index)
                continue
            for i in l:
                in_degree[i].add(index)

        while flag:
            flag = False
            delete_temp = []
            for decide in not_decide:
                temp = True
                for node in graph[decide]:
                    if node not in res:
                        temp = False
                        break
                if temp:
                    res.add(decide)
                    delete_temp.append(decide)
                    flag = True
            not_decide = not_decide - set(delete_temp)

        return sorted(res)
```










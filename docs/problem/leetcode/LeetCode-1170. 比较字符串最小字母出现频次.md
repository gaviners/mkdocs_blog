# LeetCode: 1170. 比较字符串最小字母出现频次

[TOC]

## 1、题目描述

我们来定义一个函数 `f(s)`，其中传入参数 `s` 是一个非空字符串；该函数的功能是统计 `s`  中（按字典序比较）最小字母的出现频次。

例如，若 `s = "dcce"`，那么 `f(s) = 2`，因为最小的字母是 `"c"`，它出现了 `2` 次。

现在，给你两个字符串数组待查表 `queries` 和词汇表 `words`，请你返回一个整数数组 `answer` 作为答案，其中每个 `answer[i]` 是满足 `f(queries[i]) < f(W)` 的词的数目，`W` 是词汇表 `words` 中的词。

 

**示例 1：**

```
输入：queries = ["cbd"], words = ["zaaaz"]
输出：[1]
解释：查询 f("cbd") = 1，而 f("zaaaz") = 3 所以 f("cbd") < f("zaaaz")。
```

**示例 2：**

```
输入：queries = ["bbb","cc"], words = ["a","aa","aaa","aaaa"]
输出：[1,2]
解释：第一个查询 f("bbb") < f("aaaa")，第二个查询 f("aaa") 和 f("aaaa") 都 > f("cc")。
```

**提示：**

- `1 <= queries.length <= 2000`
- `1 <= words.length <= 2000`
- `1 <= queries[i].length, words[i].length <= 10`
- `queries[i][j], words[i][j] 都是小写英文字母`

## 2、解题思路

- 统计出`words`中每个单词的长度并排序
- 使用二分查找，找出`queries`中每个字符串对应`words`中满足条件的数量

```python
import string
import bisect


class Solution:
    def numSmallerByFrequency(self, queries: [str], words: [str]) -> [int]:
        res = []
        word_nums = []
        query_nums = []

        for q in queries:
            for c in string.ascii_lowercase:
                if c in q:
                    query_nums.append(q.count(c))
                    break

        for w in words:
            for c in string.ascii_lowercase:
                if c in w:
                    word_nums.append(w.count(c))
                    break

        word_nums.sort()

        for num in query_nums:
            index = bisect.bisect(word_nums, num)
            res.append(len(word_nums) - index)

        return res
```


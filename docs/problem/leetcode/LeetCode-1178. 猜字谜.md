# LeetCode: 1178. 猜字谜

[TOC]

## 1、题目描述

外国友人仿照中国字谜设计了一个英文版猜字谜小游戏，请你来猜猜看吧。

字谜的迷面 `puzzle` 按字符串形式给出，如果一个单词 `word` 符合下面两个条件，那么它就可以算作谜底：

单词 `word` 中包含谜面 `puzzle` 的第一个字母。
单词 `word` 中的每一个字母都可以在谜面 `puzzle` 中找到。
例如，如果字谜的谜面是 `"abcdefg"`，那么可以作为谜底的单词有 `"faced", "cabbage",` 和 `"baggage"`；而 `"beefed"`（不含字母 `"a"`）以及 `"based"`（其中的 `"s"` 没有出现在谜面中）。
返回一个答案数组 `answer`，数组中的每个元素 `answer[i]` 是在给出的单词列表 `words` 中可以作为字谜迷面 `puzzles[i]` 所对应的谜底的单词数目。

 

**示例：**

```
输入：
words = ["aaaa","asas","able","ability","actt","actor","access"], 
puzzles = ["aboveyz","abrodyz","abslute","absoryz","actresz","gaswxyz"]
输出：[1,1,3,2,4,0]
解释：
1 个单词可以作为 "aboveyz" 的谜底 : "aaaa" 
1 个单词可以作为 "abrodyz" 的谜底 : "aaaa"
3 个单词可以作为 "abslute" 的谜底 : "aaaa", "asas", "able"
2 个单词可以作为 "absoryz" 的谜底 : "aaaa", "asas"
4 个单词可以作为 "actresz" 的谜底 : "aaaa", "asas", "actt", "access"
没有单词可以作为 "gaswxyz" 的谜底，因为列表中的单词都不含字母 'g'。
```

**提示：**

- `1 <= words.length <= 10^5`
- `4 <= words[i].length <= 50`
- `1 <= puzzles.length <= 10^4`
- `puzzles[i].length == 7`
- `words[i][j], puzzles[i][j] 都是小写英文字母。`
- `每个 puzzles[i] 所包含的字符都不重复。`

## 2、解题思路

- 使用位运算
- 每一个小写字母看成是1左移n位
- 首先将所有的谜底都进行位或运算
- 由于谜面的长度不会超过7，所以可以直接生成谜面的所有的子集

遍历子集实现如下：

```
j = temp
while j:
    j = (j - 1) & temp
```

从`temp`开始减小，不断地找出下一个满足条件的子集

然后判断首字母是否包含即可



```python
class Solution:
    def findNumOfValidWords(self, words: List[str], puzzles: List[str]) -> List[int]:
        import string
        from collections import defaultdict

        mapping = {}

        for index, c in enumerate(string.ascii_lowercase):
            mapping[c] = 1 << index

        words_bit = defaultdict(int)

        for word in words:
            temp = 0
            for c in word:
                temp |= mapping[c]
            words_bit[temp] += 1

        res = [0] * len(puzzles)

        for index, puzzle in enumerate(puzzles):
            # 谜面的二进制形式
            temp = 0
            for c in puzzle:
                temp |= mapping[c]

            # 生成所有的谜面子集
            j = temp
            while j:
                if (mapping[puzzle[0]]) & j:
                    res[index] += words_bit[j]
                j = (j - 1) & temp
        return res
```


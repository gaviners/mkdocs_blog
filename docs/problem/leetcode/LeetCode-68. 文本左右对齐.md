# LeetCode: 68. 文本左右对齐

[TOC]

## 1、题目描述

给定一个单词数组和一个长度 `maxWidth`，重新排版单词，使其成为每行恰好有 `maxWidth` 个字符，且左右两端对齐的文本。

你应该使用***“贪心算法”***来放置给定的单词；也就是说，尽可能多地往每行中放置单词。必要时可用空格 `' '` 填充，使得每行恰好有 `maxWidth` 个字符。

要求尽可能均匀分配单词间的空格数量。如果某一行单词间的空格不能均匀分配，则左侧放置的空格数要多于右侧的空格数。

文本的最后一行应为左对齐，且单词之间不插入额外的空格。

**说明:**

-   单词是指由非空格字符组成的字符序列。
-   每个单词的长度大于 `0`，小于等于 `maxWidth`。
-   输入单词数组 `words` 至少包含一个单词。

**示例:**

```
输入:
words = ["This", "is", "an", "example", "of", "text", "justification."]
maxWidth = 16
输出:
[
   "This    is    an",
   "example  of text",
   "justification.  "
]
```


**示例 2:**

```
输入:
words = ["What","must","be","acknowledgment","shall","be"]
maxWidth = 16
输出:
[
  "What   must   be",
  "acknowledgment  ",
  "shall be        "
]
解释: 注意最后一行的格式应为 "shall be    " 而不是 "shall     be",
     因为最后一行应为左对齐，而不是左右两端对齐。       
     第二行同样为左对齐，这是因为这行只包含一个单词。
```


**示例 3:**

```
输入:
words = ["Science","is","what","we","understand","well","enough","to","explain",
         "to","a","computer.","Art","is","everything","else","we","do"]
maxWidth = 20
输出:
[
  "Science  is  what we",
  "understand      well",
  "enough to explain to",
  "a  computer.  Art is",
  "everything  else  we",
  "do                  "
]


```



## 2、解题思路

-   关键点在于处理中间的空格，设置两个指针，`start`，`end`，代表当前的处理范围
-   然后不断的向右推进，并且处理中间的空格即可
-   需要注意的就是最后一行文字单独处理即可



```python
class Solution:
    def fullJustify(self, words: List[str], maxWidth: int) -> List[str]:
        length = len(words)
        ans = []
        start = 0
        end = 1
        cur_length = len(words[0])

        while start < length:
            while end < length:
                if cur_length + len(words[end]) + end - start <= maxWidth:
                    cur_length += len(words[end])
                    end += 1
                else:
                    break
            if start + 1 == end:
                ans.append(words[start] + " " * (maxWidth - cur_length))
            else:
                if end == length:
                    ans.append(" ".join(words[start:end]) + " " * (maxWidth - cur_length - (end - start - 1)))
                else:
                    gaps = end - start - 1
                    spaces = maxWidth - cur_length
                    remainder = spaces % gaps
                    base_spaces = spaces // gaps
                    if remainder:
                        split1 = " " * base_spaces + " "
                        split2 = " " * base_spaces
                        ans.append(split1.join(words[start:start + remainder + 1]) + split2 + split2.join(
                            words[start + remainder + 1:end]))
                    else:
                        split = " " * base_spaces
                        ans.append(split.join(words[start:end]))

            start = end
            end = start + 1
            cur_length = 0 if start >= length else len(words[start])
        return ans
```


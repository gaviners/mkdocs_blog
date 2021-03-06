# LeetCode: 751. IP 到 CIDR

[TOC]

## 1、题目描述



给定一个起始 IP 地址 ip 和一个我们需要包含的 IP 的数量 n，返回用列表（最小可能的长度）表示的 CIDR块的范围。 

CIDR 块是包含 IP 的字符串，后接斜杠和固定长度。例如：“123.45.67.89/20”。固定长度 “20” 表示在特定的范围中公共前缀位的长度。

```
输入：ip = "255.0.0.7", n = 10
输出：["255.0.0.7/32","255.0.0.8/29","255.0.0.16/32"]
解释：
转换为二进制时，初始IP地址如下所示（为清晰起见添加了空格）：
255.0.0.7 -> 11111111 00000000 00000000 00000111
地址 "255.0.0.7/32" 表示与给定地址有相同的 32 位前缀的所有地址，
在这里只有这一个地址。

地址 "255.0.0.8/29" 表示与给定地址有相同的 29 位前缀的所有地址：
255.0.0.8 -> 11111111 00000000 00000000 00001000
有相同的 29 位前缀的地址如下：
11111111 00000000 00000000 00001000
11111111 00000000 00000000 00001001
11111111 00000000 00000000 00001010
11111111 00000000 00000000 00001011
11111111 00000000 00000000 00001100
11111111 00000000 00000000 00001101
11111111 00000000 00000000 00001110
11111111 00000000 00000000 00001111

地址 "255.0.0.16/32" 表示与给定地址有相同的 32 位前缀的所有地址，
这里只有 11111111 00000000 00000000 00010000。

总之，答案指定了从 255.0.0.7 开始的 10 个 IP 的范围。

有一些其他的表示方法，例如：
["255.0.0.7/32","255.0.0.8/30", "255.0.0.12/30", "255.0.0.16/32"],
但是我们的答案是最短可能的答案。

另外请注意以 "255.0.0.7/30" 开始的表示不正确，
因为其包括了 255.0.0.4 = 11111111 00000000 00000000 00000100 这样的地址，
超出了需要表示的范围。
```

注：

- ip 是有效的 IPv4 地址。
- 每一个隐含地址 ip + x (其中 x < n) 都是有效的 IPv4 地址。
- n 为整数，范围为 [1, 1000]。



## 2、解题思路

首先按照二进制的形式，将首地址进行转换

```
255.0.0.7
==> 
11111111000000000000000000000111
```

一共10个地址，那么尾地址为：

```
11111111000000000000000000010000
==>
255.0.0.16
```



首先我们来匹配第一个地址如何才能够使用CIDR覆盖，也就是看这个地址后面最后一个1是第几位，如上面所示，最后一个1位第0位，因此，就需要使用32位掩码覆盖

然后下一个地址，

```
11111111000000000000000000001000
```

第一个1是第3位，可以使用29位掩码覆盖8个地址

下一个地址则是

```
11111111000000000000000000010000
```

这里仅剩下一个地址了，因此使用32位掩码



- 从第一个地址开始，选择掩码进行覆盖，这里有个条件，就是看初始地址中第一个1的位置
- 然后依次根据剩余地址的数量和1的位置计算掩码



```python
import socket, struct
class Solution:
    def ipToCIDR(self, ip: str, n: int) -> List[str]:
        res = []
        start = self.ip_to_number(ip)
        while n > 0:
            current = start & -start
            while current > n:
                current >>= 1
            res.append(self.number_to_ip(start) + "/" +str(32-self.zero_number(current)))
            start += current
            n -= current

        return res

    def ip_to_number(self, ip):
        """
        Convert an IP string to long number
        """
        packedIP = socket.inet_aton(ip)
        return struct.unpack("!L", packedIP)[0]

    def number_to_ip(self, number):
        """
        Convert an long number to IP string to
        :param number:
        :return:
        """
        return socket.inet_ntoa(struct.pack('!L', number))

    def zero_number(self, num):
        zero = 0
        for i in range(32):
            if not ((num >>i) & 1):
                zero += 1
            else:
                break
        return zero
```


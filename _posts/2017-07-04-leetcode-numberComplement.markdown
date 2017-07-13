---
layout: default
title:  "LeetCode(476)： Number Complement"
date:   2017-07-04 17:02:00
categories: LeetCode
---


# 在此处输入标题

标签（空格分隔）： 未分类

---

---
layout: default
title:  "LeetCode(476)： Number Complement"
date:   2017-07-04 17:02:00
categories: LeetCode
---


### Description:

Given a positive integer, output its complement number. The complement strategy is to flip the bits of its binary representation.

### Note:

The given integer is guaranteed to fit within the range of a 32-bit signed integer.
You could assume no leading zero bit in the integer’s binary representation.
 
> 
Example 1:
Input: 5
Output: 2
Explanation: The binary representation of 5 is 101 (no leading zero bits), and its complement is 010. So you need to output 2.
 

> Example 2:
Input: 1
Output: 0
Explanation: The binary representation of 1 is 1 (no leading zero bits), and its complement is 0. So you need to output 0.



解题思路：这个题的意思就是求出给出数据num的反码的十进制数。也就是num-->二进制num-->二进制num的反码-->二进制num反码的十进制表示。
首先把num转换为二进制字符串，然后遍历该字符串。按题目要求，应该从二进制最左边的1开始。如010就把左边的0去掉变为10。
我们可以设置一个flag，在遍历字符串时只要碰到1就把flag置为true，即开始取反码。
取反码的过程就比较简单了，只要1-->0 0-->1就可以了。
得到反码后再通过integer的内置方法转为十进制就可以了。
　　
```java
public class Solution {
    public static int findComplement(int num) {
        StringBuilder sbin=new StringBuilder(Integer.toBinaryString(num));
        boolean flag = false;
        for (int i = 0; i < sbin.length(); i++) {
            if (sbin.charAt(i) == '1'){
                flag = true;
            }
            if (flag) {
                sbin.setCharAt(i, sbin.charAt(i) == '1' ? '0' : '1');
            }
        }
        return Integer.valueOf(sbin.toString(), 2);
    }
}
```
**注意**：更改字符串是不能用String类型的，因为String在实现的时候是被设计为final类型的。如果想改变应该用StringBuilder或者StringBuffer。


### 一些思考:
一个经典的面试题是问String、StringBuilder、StringBuffer的区别。很多人在准备面试的时候估计背过好几遍却不知道究竟有什么用处，毕竟平时写代码用String就够了呀。上述例子也许可以稍微打消一点这种疑问了，String的不可变的特性导致我们无法简单地实现需要的代码逻辑（当然，用String也是可以实现的）。此时StringBuilder和StringBuffer就派上用场了。









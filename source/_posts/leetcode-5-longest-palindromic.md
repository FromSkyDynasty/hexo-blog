---
title: LeetCode题目 ( JAVA解决思路记录 ) - 5.字符串最长回文
date: 2018-04-09 19:18:24
tags:
    - leetcode
    - longest-palindromic-substring
    - java
categories:
    - java
    - Algorithm
---

### 题目描述

给定一个字符串 s，找到s中最长的[回文](https://zh.wikipedia.org/wiki/%E5%9B%9E%E6%96%87)子串。你可以假设 s 长度最长为1000。
<!-- more -->
原题目地址：[https://leetcode.com/problems/longest-palindromic-substring/description/](https://leetcode.com/problems/add-two-numbers)

中文版题目地址：[https://leetcode-cn.com/problems/longest-palindromic-substring/description/](https://leetcode-cn.com/problems/longest-palindromic-substring/description/)

### 标准答案
标准答案[点此](https://leetcode.com/problems/longest-palindromic-substring/solution/#approach-4-expand-around-center-accepted)查看

```java
public String longestPalindrome(String s) {
    int start = 0, end = 0;
    for (int i = 0; i < s.length(); i++) {
        int len1 = expandAroundCenter(s, i, i);
        int len2 = expandAroundCenter(s, i, i + 1);
        int len = Math.max(len1, len2);
        if (len > end - start) {
            start = i - (len - 1) / 2;
            end = i + len / 2;
        }
    }
    return s.substring(start, end + 1);
}

private int expandAroundCenter(String s, int left, int right) {
    int L = left, R = right;
    while (L >= 0 && R < s.length() && s.charAt(L) == s.charAt(R)) {
        L--;
        R++;
    }
    return R - L - 1;
}
```

### 答案思路

#### 第二层循环 `expandAroundCenter`

以`s[left]`到`s[right]`包含的字符串为中心，向左右扩展，寻找相等的字符。
若 `s[left] == s[right]` 则去比较 `s[left - 1]` 和 `s[right + 1]`,若相等，重复以上的步骤，若比较到两者不相等，或者到了字符串的头部或尾部，停止比较，找到以 `s[left] - s[right]`为中心的最长回文。

可以如下图所示表示：

![](https://oi4kggmuk.qnssl.com/images/longest-substring.png)

#### 第一层循环

从第一个字符串开始遍历，每次找到以`s[i]`和 `s[i] - s[i + 1]`(长度为奇数和偶数的)为中心的最长回文长度，然后使用其中较长的，再与前次结果相比较，若长度大于前次的，则选取该子串，然后根据中心`i/i+1`和长度，找到**该最长回文**的起始index和结束index（`start/end`），循环完成后得出最长子串。

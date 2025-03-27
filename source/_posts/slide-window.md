---
title: 定长滑动窗口
date: 2025.3.27
updated:
tags: 算法
categories: 算法杂谈
keywords: 滑动窗口
description: 关于定长滑动窗口类型的算法的刷题经验
---

# 定长滑动窗口

## 引入

> 计算所有长度恰好为 k 的子串中，最多可以包含多少个元音字母：
> 1. 暴力枚举所有子串？太慢
> 2. 如何以 $O(1)$ 时间复杂度计算出子串的元音个数？

通过**定长滑动窗口**，只考虑离开滑动窗口和进入滑动窗口的字母，即可实现这一点，因为中间部分的字母与上一个循环选中的字母相同，如果在下一个循环中重新运算，则出现了冗余计算。

## 步骤

一个基本的定长滑动窗口**模板**如下：
1. 进入窗口
2. 退出窗口
3. 更新相关统计量

## 模板

右指针通过 for 循环移动，左指针通过循环内条件移动：
1. 进入窗口（此时窗口长度为 k+1）
2. 退出窗口（当窗口长度为 k+1 时，退出窗口）
3. 更新值（经过上述操作后，窗口长度恰好为k，此时更新数值）

```cpp
class Solution {
public:
    int maxVowels(string s, int k) {
        int len = s.size();
        int l=0;
        int max_num=0,num=0;
        for(int r=0;r<len;r++){
            //进入窗口
            if(s[r]=='a'||s[r]=='e'||s[r]=='i'||s[r]=='o'||s[r]=='u'){
                num++;
            }
            //退出窗口，注意先更新值，再移动指针
            if(r-l+1>k){
                if(s[l]=='a'||s[l]=='e'||s[l]=='i'||s[l]=='o'||s[l]=='u'){
                    num--;
                }
                l++;
            }
            //更新值
            if(r-l+1==k)
                max_num=max(max_num,num);
        }
        return max_num;
    }
};
```

## 练习

+ [1456. 定长子串中元音的最大数目](https://leetcode.cn/problems/maximum-number-of-vowels-in-a-substring-of-given-length/)
+ [643. 子数组最大平均数 I](https://leetcode.cn/problems/maximum-average-subarray-i/)
+ [1343. 大小为 K 且平均值大于等于阈值的子数组数目](https://leetcode.cn/problems/number-of-sub-arrays-of-size-k-and-average-greater-than-or-equal-to-threshold/) 
+ [2090. 半径为 k 的子数组平均值](https://leetcode.cn/problems/k-radius-subarray-averages/)
+ [2379. 得到 K 个黑块的最少涂色次数](https://leetcode.cn/problems/minimum-recolors-to-get-k-consecutive-black-blocks/)
+ [1423. 可获得的最大点数](https://leetcode.cn/problems/maximum-points-you-can-obtain-from-cards/)
+ [1052. 爱生气的书店老板](https://leetcode.cn/problems/grumpy-bookstore-owner/)

### 1423. 可获得的最大点数

关于这个问题共有两种思考方式，一种是正向思维，采用前后拼接的方式，再进行滑动窗口，就是我自己的做法

```cpp
class Solution {
public:
    int maxScore(vector<int>& cardPoints, int k) {
        //前后拼接
        for(int i=0;i<k;i++){
            cardPoints.push_back(cardPoints[i]);
        }
        int len=cardPoints.size();
        int l=len-2*k,sum=0,max_sum=0;
        for(int r=len-2*k;r<len;r++){
            sum+=cardPoints[r];
            if(r-l+1>k){
                sum-=cardPoints[l];
                l++;
            }
            if(r-l+1==k)
                max_sum=max(max_sum,sum);
        }
        return max_sum;
    }
};
```

这种做法的空间复杂度是 `O(k)` 因为采用了额外的空间进行拼接，除此之外，还可以采用逆向思维：

```python
class Solution:
    def maxScore(self, cardPoints: List[int], k: int) -> int:
        n = len(cardPoints)
        m = n - k
        min_s = s = sum(cardPoints[:m])
        for i in range(m, n):
            s += cardPoints[i] - cardPoints[i - m]
            min_s = min(min_s, s)
        return sum(cardPoints) - min_s
```

这种做法不需要消耗额外空间，空间复杂度为 `O(1)`。

## 进阶

+ [3439. 重新安排会议得到最多空余时间 I](https://leetcode.cn/problems/reschedule-meetings-for-maximum-free-time-i/)

### 3439. 重新安排会议得到最多空余时间

时间块的滑动等价于**相邻空闲块的合并**，”很容易“（~~一点都不容易~~）看出两个空闲块的合并可以由一次移动块操作得来，并可以等价的出现在左侧或者右侧；那么，问题就可以转换为进行k次合并，所得到的最大连续空闲块长度，即**定长滑动窗口**问题

>**tips**
>这种贪心问题始终不太会做，而且及其容易**爆零**，建议多尝试贪心题


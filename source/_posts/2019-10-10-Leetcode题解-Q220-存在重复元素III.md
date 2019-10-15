---
title: Leetcode题解-Q220-存在重复元素III
typora-root-url: ../../source/
date: 2019-10-10 19:17:02
mathjax: true
tags:
- leetcode
- 滑动窗口
- TreeSet
---
链接：https://leetcode-cn.com/problems/contains-duplicate-iii

<!--more-->

# 存在重复元素III



## 题目

给定一个整数数组，判断数组中是否有两个不同的索引 i 和 j，使得 nums [i] 和 nums [j] 的差的绝对值最大为 t，并且 i 和 j 之间的差的绝对值最大为 ķ。

示例 1:

> 输入: nums = [1,2,3,1], k = 3, t = 0
> 输出: true

示例 2:

> 输入: nums = [1,0,1,1], k = 1, t = 2
> 输出: true

示例 3:

> 输入: nums = [1,5,9,1,5,9], k = 2, t = 3
> 输出: false

## 分析

判断数组中是否存在距离不超过k的两个元素，他们的差值大小不大于t。

换句话说即在大小为min(k+1, n)的窗口中存在两个元素的不大于t。

~~最开始理解错误，理解成了大小为k+1的窗口中任意两个元素的差值不大于t。这时即可使用滑动窗口的最大值/最小值的解法，找到一个窗口使得max-min <= t。~~

## 暴力解法

遍历计算
$$
|num[i] - nums[j]|\leq t，i \in [0,n), j \in [i+1 , i+k]
$$


时间复杂度为
$$
O(n\times min(k,n))
$$
空间复杂度为
$$
O(1)
$$

## 二叉搜索树优化

暴力解法中i的遍历是必要的，即窗口的移动。

窗口的移动伴随着头部元素的移除和尾部元素的加入，在新增元素后，已有元素的差值是不变的，新增了
$$
|nums[i]-nums[i-j]|,j\in [1,k]
$$
即nums[i-j]为原来窗口的值，如果nums[i-j]是有序的，那我们只需要找到nums[i]相邻的值即可更新当前窗口的最小插值。所以需要保持窗口元素的有序性。

AVL树能很好的满足我们的需求，在Java中TreeSet（红黑树）实现了这一数据结构。

```java
static class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        if (nums == null || nums.length < 2 || k < 1) {
            return false;
        }
        TreeSet<Integer> set = new TreeSet<>();
        for (int i = 0; i < nums.length; i++) {
            Integer pre = set.ceiling(nums[i]);
            if (pre != null && pre <= nums[i] + t) return true;

            Integer succ = set.floor(nums[i]);
            if (succ != null && nums[i] <= succ + t) return true;

            set.add(nums[i]);
            if (set.size() > k) {
                set.remove(nums[i - k]);
            }
        }
        return false;
    }
}
```


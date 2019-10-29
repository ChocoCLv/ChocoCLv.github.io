---
title: Leetcode题解-跳跃游戏I&II
typora-root-url: ../../source/
date: 2019-10-26 15:39:56
tags:
- leetcode
- 贪心
- bfs
---



Q55 跳跃游戏I： https://leetcode-cn.com/problems/jump-game/ 

Q45 跳跃游戏II： https://leetcode-cn.com/problems/jump-game-ii/ 

<!--more-->

# 跳跃游戏I&II

给定一个非负整数数组nums[]，nums[i]表示在位置i能跳到的最远距离。

## 跳跃游戏I

给定nums[]，问能否跳到最后一个位置.

示例 1:

> 输入: [2,3,1,1,4]
> 输出: true
> 解释: 我们可以先跳 1 步，从位置 0 到达 位置 1, 然后再从位置 1 跳 3 步到达最后一个位置。

![1572081547210](/imgs/1572081547210.png)

示例 2:

> 输入: [3,2,1,0,4]
> 输出: false
> 解释: 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。

![1572081945192](/imgs/1572081945192.png)

### 递归（超时）

从nums[0]出发，递归尝试step=1...nums[i]，直到当前index>=nums.length-1，否则不能到达。

这种方法过于暴力。

### 贪心

简单分析后发现，0的位置才会影响结果，如果数组元素全部非零，则一定可以到达最后一个位置。所以我们如果能跨过所有的0那么就能道道最后一个位置。

从位置i出发最远可以到达的位置：
$$
from(i)=nums[i]+i
$$
所以需要从左向右遍历，记录当前可以到达的最远的位置。

```java
class Solution {
    public boolean canJump(int[] nums) {
        int max = 0;
        for(int i = 0; i < nums.length - 1;i++){
            //是否能越过这个0
            if(nums[i] == 0 && max <= i){
                return false;
            }
            max = Math.max(max, i+nums[i]);
        }
        return true;
    }
}
```

## 跳跃游戏II

假设一定能到达最后一个位置，求最小跳跃次数

### bfs

从i出发，可以到达i+1...i+nums[i]，即可以看成可达关系

![1572081547210](/imgs/1572081547210.png)

可以转换成

![1572083200620](/imgs/1572083200620.png)

所以问题就变成了有向无权图的单源单宿最短路。可以直接使用bfs求解。

```java
class Solution {
    public int jump(int[] nums) {
        int[] steps = new int[nums.length];
        for(int i=0;i<nums.length;i++){
            steps[i] = nums.length;
        }
        steps[0] = 0;
        for(int i = 0;i<nums.length;i++){
            for(int j=i+1;j<nums.length && j<=nums[i]+i;j++){
                steps[j]=Math.min(steps[i]+1, steps[j]);
            }
        }
        return steps[nums.length-1];
    }
}
```

bfs时间复杂度为
$$
O(n+e),e \leq n*(n-1)/2  =>  O(n^2)
$$


### 贪心

从i出发时，跳到[i, nums[i]+i]范围内下一次可以跳到的最远的位置。即
$$
max(nums[i]+i),i \in [i,nums[i]+i]
$$

```java
class Solution {
    public int jump(int[] nums) {
        int end = 0;
        int maxPosition = 0; 
        int steps = 0;
        for(int i = 0; i < nums.length - 1; i++){
            //找能跳的最远的
            maxPosition = Math.max(maxPosition, nums[i] + i); 
            if( i == end){ //遇到边界，就更新边界，并且步数加一
                end = maxPosition;
                steps++;
            }
        }
        return steps;
    }
}
```

时间复杂度为
$$
O(n)
$$
反证法可简单证明该贪心算法的正确性。
# 相向双指针

* Author: ZhenHaoCl
* Date: 2023/11/18
* Brief: 关于相向双指针的介绍与简单应用

## 什么是相向双指针？

* 一种算法，可以用来解决一些特定问题

* 两个指针，分别指向列表的头尾，它们两个移动时是相向的，一般在它们交汇时就结束循环

## 为什么要使用相向双指针

* 降低时间复杂度，让程序运行更快

##  如何使用

* 例题[11. 盛最多水的容器 - 力扣（LeetCode）](https://leetcode.cn/problems/container-with-most-water/)
* 分析这道题目，我们可以知道接水容器的容量为容器的底部宽度乘上左右两边中的短边长度

### 1.暴力破解

这是我们最容易想到的一种办法，也就是套两个循环，找出最大的容器

```python
class Solution {
    public int maxArea(int[] height) {
        int len = height.length;

        int max = 0;
        for(int i = 0; i < len; i++){
            for(int j = i + 1; j < len; j++){
                max = Math.max((j - i) * Math.min(height[j], height[i]), max);
            }
        }
        return max;
    }
}

```

### 2.相向双指针

其实逻辑和暴力破解类似，都是遍历，但是使用相向双指针可以减少一些不必要的遍历，也就减少了一次的循环

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        ans = left = 0											# 左指针指向数组的左端点
        right = len(height) - 1									# 右指针指向数组右端点
        while left < right:										# 遍历
            # 计算此时容器的容积，并更新
            area = (right - left) * min(height[left], height[right])	
            ans = max(ans, area)
            if height[left] < height[right]:					# 如果容器左壁的高度小于右壁的高度，右移动左指针
                left += 1
            else:												# 否则移动左移右指针
                right -= 1
        return ans

```

## 总结

* 双指针的主要作用就是减少循环的嵌套，从而降低时间复杂度，减少程序运行时间

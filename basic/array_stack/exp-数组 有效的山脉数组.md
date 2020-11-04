---
title: exp-数组 有效的山脉数组
date: 2020-11-03 22:42:05
categories: 刷题笔记
tags: Java
---

**题目**

[leetcode941](https://leetcode-cn.com/problems/shortest-distance-to-a-character/)

给定一个整数数组 A，如果它是有效的山脉数组就返回 true，否则返回 false。

让我们回顾一下，如果 A 满足下述条件，那么它是一个山脉数组：

A.length >= 3
在 0 < i < A.length - 1 条件下，存在 i 使得：
A[0] < A[1] < ... A[i-1] < A[i]
A[i] > A[i+1] > ... > A[A.length - 1]

<img src="/img/leetcode/mountain_array.png.png">

**思路**

1.先找到最大值记录下坐标i。
2.一个变量记录元素与最大值的差
3.i的左边，当前元素与最大值的差与变量比较，应该越来越大
4.i的右边，当前元素与最大值的差与变量比较，应该越来越小

**代码**

```JAVA
class Solution {
    public boolean validMountainArray(int[] A) {
        int length = A.length;
        int pre = -10000;
        int next =  10000;
        if(length == 0){
            return false;
        }
        //找到最大值
        int cur = A[0],index = 0;
        for(int i = 1  ; i<length ; i++){
            if(A[i] <= cur ){
                break;
            }
            cur = A[i];
            index = i ;
        }
        //如果最大值在第一个元素和最后一个返回false
        if(index == 0 || index == length-1){
            return false;
        }
        //循坏比较
        for(int i = 0; i<length;i++){
            if(i<index){
                //说明在最大值的左边
                if(A[i] - cur <= pre){
                return false ;
                }
                pre = A[i] - cur ;
            }
            if(i>index){
                //说明在最大值右边
                if(A[i] == cur || A[i] - cur >= next){
                    return false;
                }
                next = A[i] - cur ;
            }
        }
        return true;
    }
}
```

**优化**

第二天起来看，感觉写的太复杂了。要多花时间在思考上面，而不是写代码QAQ
转换了下思维，可以从开头往右找峰值，找到峰值返回；然后从后面往左找峰值，找到峰值返回，再比较峰值数的索引是不是一样就行了。这样想想好像比之前的思路更简洁一点，至少代码是简洁了好多。

```JAVA
class Solution {
    public boolean validMountainArray(int[] A) {
        int length = A.length;
        if(length < 3){
            return false;
        } 
        //左右两个指针
        int left = 0,right = length-1;
        //从左往又找最大值
        while(left < length-1 && A[left] < A[left+1]){
            left++;
        }
        //从右往左找
       while(right > 0 && A[right] < A[right-1]){
            right--;
        }
        if(left == 0 || right == length-1 ||left != right){
            return false;
        }
        return true;
    }
}
```


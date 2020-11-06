---
title: 和为k的子数组
date: 2020-11-05 19:28:23
categories: 刷题笔记
tags:
---

## 综述

适用场景：题目要求连续的限制
好处:优化时间复杂度


### 引入

前缀和的思路是对于一个给定的数组 nums，我们额外开辟一个前缀和数组进行预处理。

有N个正整数的数组A，要求一个新数组B，数组B的第i个元素是原数组A第0到第i个数的和。
如[1,2,3,4,5,6]的前缀和pre = [0,1,3,6,10,15,21],也就是pre[i+1] = pre[i] + nums[i]。

pre[i]就是nums[0..i-1]的和。

```Java
int n = nums.length;
// 前缀和数组
int[] pre = new int[n + 1];
pre[0] = 0;
for (int i = 0; i < n; i++)
    pre[i + 1] = pre[i] + nums[i];
```

**连续子数组总个数**

求数组的连续子数组总个数

这里连续指的是数组的索引连续，比如[1,3,4]的连续子数组有[1],[3],[4],[1,3],[3,4],[1,3,4]，总个数为6

思路:
总的连续子数组个数等于=以索引为 0 结尾的子数组个数 + 以索引为 1 结尾的子数组个数 + … + 以索引为 n - 1 结尾的子数组个数.

以索引0结尾的子数组个数:[1] 1个
以索引1结尾的子数组个数:[3],[1,3] 2个
以索引2结尾的子数组个数:[4],[3,4],[1,3,4] 3个
以索引i结尾的子数组个数:[i],[i,i-1],[i,i-1,i-2..] i+1个

所以这道题可以直接用等差数列求和公式 (1 + n) * n / 2，其中n数组长度。

还可以利用前缀和的思路，索引为2结尾的子数组个数 = 索引为1结尾的子数组个数 + 1,边遍历边求和。

```Java
int countSubArray(int [] nums){
    int count,pre;
    for(int i =0;i<nums.length;i++){
        pre += 1;   //当前索引结尾的个数 = 前一个索引结尾的+1
        count += pre;//总的
    }
    return count;
}
```

### 560.和为K的子数组

**题目**
给定一个整数数组和一个整数 k，你需要找到该数组中和为 k 的连续的子数组的个数。

示例：

```
输入:nums = [1,1,1], k = 2
输出: 2 , [1,1] 与 [1,1] 为两种不同的情况。
说明 :
```

数组的长度为 [1, 20,000]。
数组中元素的范围是 [-1000, 1000] ，且整数 k 的范围是 [-1e7, 1e7]。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/subarray-sum-equals-k
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

**思路**
题目求的是xxx的连续的子数组个数,题目要求连续的限制可以考虑用前缀和。
如何快速得到某个子数组的和？可以利用之前说的 pre[i]就是nums[0..i-1]的和,那么 nums[i,j] = pre[j+1] - pre[i]

**代码**

```Java
class Solution {
    public int subarraySum(int[] nums, int k) {
        //构造前缀和
        int length = nums.length;
        int pre [] = new int [length + 1];
        int count = 0 ;
        for(int i=0;i<length;i++){
            //前缀和数组
            pre[i+1] = pre[i] + nums [i];
            
        }
       
        // 记录符合条件的个数
         int ans = 0;
         // 穷举所有子数组
        for (int j = 1; j <= length; j++)
            for (int i = 0; i < j; i++){  
            //子数组的和nums[i,j] = pre[j+1] - pre[i] == K ?
            // i<j j<=length
                 if (pre[j] - pre[i] == k)
                    ans++;
            }
        return ans;
    }
}
```

**优化**

前缀和 + hash表

利用前缀和快速的得到了某个子数组的和，但是穷举了所有子数组所以效率很低。

```
  for (int j = 1; j <= length; j++)
        for (int i = 0; i < j; i++){  
                 if (pre[j] - pre[i] == k)
                    ans++;
        }
```

第二个循环在计算有几个i能够使得pre[j] - pre[i] = k,每找到一个i就把结果加一。

if语句里面的条件判断移项可以写成

```
//这样看来，判断似乎和i没有关系了，左边是pre[j]，与第一层循环有关
if(pre[j] == pre[i] - k)
ans++
```

可以转换为考虑"以i结尾的和为 k 的连续子数组个数时"只要统计有多少个前缀和为 pre[i] -− k的pre[j]即可。

使用哈希表，我们建立哈希表 mp，以和为键，出现次数为对应的值，记录 pre[i] 出现的次数.
同时，由于pre[i]的计算只与前一项的答案有关，因此我们可以不用建立 pre 数组，直接用pre变量来记录 pre[i−1] 的答案即可。

执行过程，nums =  [3 4 7 ]， k = 7 
mp = {(0,1)}
i=0时，pre=3, pre-k=-4,count=0,mp={(0,1),(3,1)}
i=1时，pre=3+4=7,pre-k=0,count=1,mp={(0,1),(3,1),(7,1)}
i=2时，pre=7+7=14，pre-k=7,cout=2,mp={(0,1),(3,1),(7,1),(14,1)}

```JAVA
public class Solution {
    public int subarraySum(int[] nums, int k) {
        int count = 0, pre = 0;
        // map：前缀和 -> 该前缀和出现的次数
        HashMap < Integer, Integer > mp = new HashMap < > ();
        //基本的
        mp.put(0, 1);
        for (int i = 0; i < nums.length; i++) {
            //当前的和 = 上一个和 + 当前数组
            pre += nums[i];
            //如果前面有这个前缀和，则直接更新答案
            if (mp.containsKey(pre - k)) {
                count += mp.get(pre - k);
            }
            mp.put(pre, mp.getOrDefault(pre, 0) + 1);
        }
        return count;
    }
}


```






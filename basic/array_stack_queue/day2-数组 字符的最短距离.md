
**题目**

[leetcode821](https://leetcode-cn.com/problems/shortest-distance-to-a-character/)

给定一个字符串 S 和一个字符 C。返回一个代表字符串 S 中每个字符到字符串 S 中的字符 C 的最短距离的数组。

示例 1:
输入: S = "loveleetcode", C = 'e'
输出: [3, 2, 1, 0, 1, 0, 0, 1, 2, 2, 1, 0]

说明:
字符串 S 的长度范围为 [1, 10000]。
C 是一个单字符，且保证是字符串 S 里的字符。
S 和 C 中的所有字母均为小写字母。

**思路**
1. 初始化一个数组distanceArr，赋最大值。
2. 找到S中与C相等的字符。
3. 先往左边扫，直到扫到第一个或者distanceArr[left] < distance。
4. 再往右边扫，直到扫到下一个C或者最后一个数组元素。

说明：
- 往右扫时扫到下一个C就停止而不用比是不是最短距离的原因是：以下一个C为基准往左扫的时候会比较距离。
- 数组元素默认值为0，0已经是最小距离了，为方便比较赋最大值	。

**代码**

```JAVA
import java.util.Arrays;
class Solution {
    public int[] shortestToChar(String S, char C) {
        int length = S.length();
        int distanceArr [] = new int [length];
        //刚开始数组的距离不能全部为0,因为这样就是最小的距离的没办法比较之后更改
        Arrays.fill(distanceArr,Integer.MAX_VALUE);
        for(int i = 0 ; i < length ; i++){
            //S中找到C
            if(C == S.charAt(i)){
                //记录左指令与右指针，并且距离distance
                int left = i,right = i + 1,distance = 0 ;
                //往左边扫,扫到最开始或者遇见数组中距离比distance小的结束
                while(left >= 0 && distanceArr[left] > distance){
                    distanceArr[left--] = distance++ ; 
                }
                //跳出循环之后，应该更新distance
                distance = 1 ;
                //往右边扫,扫到下一个C或者最后一个数组元素
                while(right < length && C != S.charAt(right) ) {
                    distanceArr[right++] = distance ++;
                }
            }
        }
        return distanceArr;
    }
}
```

**反思**
可以先对可能情况进行分类帮助分析：
 1.只有左边有C字符： sdafC...
 2.左右两边都有C字符：...CdsfsC...
 3.只有右边有C字符：...Cdsff

**其他解法**

```JAVA
class Solution {
    public int[] shortestToChar(String S, char C) {
        int length = S.length();
        int distanceArr [] = new int [length];
        int pre = - 10000; //刚开始没有e距离就无限大,因为后面的-pre
        //先从左往右遍历
        for(int i = 0 ; i < length ; i++){
            //每一个元素计算与pre的距离，遇见C把C的当前位置更新为pre
            //looexxevx v离第二个e的距离小于第一个e所以更新e的位置为pre
            if(C == S.charAt(i)){
                pre = i;
            }
            distanceArr[i] = i - pre;
        }
        //从右往前遍历
        pre = 10000;
        for(int i =length-1 ; i>=0 ;i--){
            if(C == S.charAt(i)){
                pre = i;
            }
            distanceArr[i] = Math.min(distanceArr[i], pre - i);
        }
        return distanceArr;
    }
}
```
---
title: day4-栈 字符串解码
date: 2020-11-04 20:08:11
categories: 刷题笔记
tags: JavaScript
---

**题目**

[leetcode194.字符串解码](https://leetcode-cn.com/problems/shortest-distance-to-a-character/)

给定一个经过编码的字符串，返回它解码后的字符串。
编码规则为: k[encoded_string]，表示其中方括号内部的 encoded_string 正好重复k次。注意k保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 k ，例如不会出现像 3a 或 2[4] 的输入。

示例 ：

```
输入：s = "3[a]2[bc]"
输出："aaabcbc"

输入：s = "3[a2[c]]"
输出："accaccacc"

输入：s = "2[abc]3[cd]ef"
输出："abcabccdcdcdef"

输入：s = "abc3[cd]xyz"
输出："abccdcdcdxyz"
```

**思路**

看见方括号，首先想到用栈。

题目给定的条件有四种可能出现的字符
1.字母 -> 直接拼接str
2.数字 -> 拼接到数字字符串
3.[   -> 当前的str与当前的num入栈,清空str与num
4.]   -> {str,num}出栈,当前的str是需要重复的字符串，{}里的str是[前的字符串

示例: `xy3[a2[c]]`

```
   | str | num |stack
x  | "x" |"" | []
y  | "xy"|"" | []
3  | "xy"|"3"| []
[  | ""| ""| [{str:"xy",num:"3"}]
a  | "a" | ""| [{str:"xy",num:"3"}]
2  | "a" | "2"| [{str:"xy",num:"3"}]
[  | "" | ""| [{str:"xy",num:"3"},{str:"a",num:"2"}]
c  | "c"|" " |[{str:"xy",num:"3"},{str:"a",num:"2"}]
]  | "a" + "cc"|"" |[{str:"xy",num:"3"}]
]  | "xy" + "accaccacc"|"" |[]
```

**代码**

```js
/**
 * @param {string} s
 * @return {string}
 */
var decodeString = function(s) {
    let num = '',str='';
    let stack = [] ;
    for(let char of s){
        if(!isNaN(char)){
            num += char;     
        }else if(char == "["){
          stack.push({str,num});
          str ="";
          num ="";
        }else if(char =="]"){
        let date = stack.pop();
        str = date.str + str.repeat(date.num);
        }else{
        str += char;
        }
    }
    return str;
};
```

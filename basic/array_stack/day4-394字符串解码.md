
**题目**

[leetcode394.字符串解码](https://leetcode-cn.com/problems/shortest-distance-to-a-character/)

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

**其他解法**

这道题是一个不断的解码之后拼接的问题，把答的字符串分解成一个个小的字符串进行解码。

比如:"3[a2[c]d]2[b]xy",可以拆分成"3[a2[c]d]","2[b]","xy"。"[]" 里可能嵌套 "[]" ,所以需要递归的只有拆分后有"[]"的部分。

这时套入递归的三个问题

1.终止条件(基线条件)是什么?
    什么时候递归结束,"]"结束递归

2.应该返回什么？
    题目对字符串进行解码，返回的应该是解好码的字符串，"3[a2[c]d]" 这种情况可以看出这种递归其实是从内到外的,不仅要返回解码好的"cc",还应该返回"d"的下标,也就是"]"的下一个位置给上一级递归继续进行,直到最外层的"]"解析完毕。

3.本级递归应该做什么？递归的目的是什么？
   首先本层应该要对字符串进行解码，其次是要与已经解好码的字符串进行拼接。结合2按要求进行返回。

```JS
const type = {
    isAlpha: s => /[a-z]/i.test(s),
    isDigit: s => /[0-9]/.test(s),
    isOpenParen: s => s === '[',
    isCloseParen: s => s === ']',
};

/**
 * @param {string} s
 * @return {string}
 */
var decodeString = function (s, i = 0) {
    // 从 i 开始遍历字符串
    let decoded = ''; // 解密字符串
    let cnt = ''; // 累计次数
    while (i < s.length) {
        if (type.isAlpha(s[i])) {
            decoded += s[i];
            i++;
        } else if (type.isDigit(s[i])) {
            cnt += s[i];
            i++;
        } else if (type.isOpenParen(s[i])) {
            //从内层的递归中获取解码好的字符串与下一次从哪里开始的坐标
            const [pattern, nextIndex] = decodeString(s, i + 1);
            decoded += pattern.repeat(Number(cnt));
            cnt = '';
            i = nextIndex;
            continue;
        } else if (type.isCloseParen(s[i])) {
            return [decoded, i + 1];
        }
    }
    return decoded;
};
```

[参考](https://github.com/suukii/91-days-algorithm/blob/master/basic/array-stack-queue/04.decode-string.md)
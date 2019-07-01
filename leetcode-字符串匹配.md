## 字符串匹配
### 10-Regular Expression Matching
#### expression
Given an input string (`s`) and a pattern (`p`), implement regular expression matching with support for `'.'` and `'*'`.
```
'.' Matches any single character.
'*' Matches zero or more of the preceding element.
```
The matching should cover the **entire** input string (not partial).
**Note:**
-   `s` could be empty and contains only lowercase letters `a-z`.
-   `p` could be empty and contains only lowercase letters `a-z`, and characters like `.` or `*`.
**Example 1:**
```
Input:
s = "aa"
p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
```
**Example 2:**
```
Input:
s = "aa"
p = "a*"
Output: true
Explanation: '*' means zero or more of the precedeng element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
```
#### comprehension
_表示之前那个字符可以有0个，1个或是多个，就是说，字符串a_b，可以表示b或是aaab，即a的个数任意.需要用递归Recursion来解.
#### 思路
-   若p为空，s也为空，返回true，反之，返回false
-   若p的长度为1，s的长度也为1，且相同或是p为'.'则返回true，否则，返回false
-   若p的第二个字符不是'*',若此时s为空返回false，否则判断首字符是否匹配，且从各自的第二个字符开始调用递归函数匹配
-   若p的第二个字符是'*'，进行下列循环：条件是若s不为空且首字符匹配（包括p[0]），调用递归函数匹配s和去掉前两个字符的p（这样做的原因是假设此时的星号的作用是让前面的字符出现0次，验证是否匹配），若匹配返回true，否则s去掉首字母（因为此时首字母匹配了，我们可以去掉s的首字母，而P由于星号的作用，可以有任意个首字母，所以不用去掉），继续进行循环
-   返回调用递归函数匹配s和去掉前两个字符的p的结果（这么做的原因是处理星号无法匹配的内容，比如s="ab", p="a_b"，直接进入while循环后，我们发现"ab"和"b"不匹配，所以s变成"b"，那么此时跳出循环后，就到最后的return来比较"b"和"b"了，返回true。再举个例子，比如s="", p="a_"，由于s为空，不会进入任何的if和while，只能到最后的return来比较了，返回true，正确）。
```
class Solution{
public:
	bool isMatch(string s, string p) {
		if (p.empty())
			return s.empty();
		if (p.size() == 1) {
			return (s.size() == 1 &&( s[0] == p[0] || p[0] == '.'));
		}
		if (p[1] != '*') {
			if (s.empty())
				return false;
			return (s[0] == p[0] || p[0] == '.') && isMatch(s.substr(1), p.substr(1));
		}
		while (!s.empty() && (s[0] == p[0] || p[0] == '.')) {
			if (isMatch(s, p.substr(2)))
				return true;
			s = s.substr(1);
		}
		return isMatch(s, p.substr(2));
	}
};
```
### 44-Wildcard Matching
#### 描述
Given an input string (`s`) and a pattern (`p`), implement wildcard pattern matching with support for  `'?'`  and  `'*'`.
'?' Matches any single character.
'*' Matches any sequence of characters (including the empty sequence).
The matching should cover the  **entire**  input string (not partial).
**Note:**
-   `s` could be empty and contains only lowercase letters  `a-z`.
-   `p`  could be empty and contains only lowercase letters  `a-z`, and characters like  `?` or `*`.
**Example 1:**
```
Input:
s = "adceb"
p = "*a*b"
Output: true
Explanation: The first '*' matches the empty sequence, while the second '*' matches the substring "dce".
```
#### 解决
该题中‘\*’可以匹配任何字符，与上题不同。
p串中星号的位置很重要，用 jStar 来表示，还有星号匹配到s串中的位置，使用 iStart 来表示，这里 iStar 和 jStar 均初始化为 -1，表示默认情况下是没有星号的。然后再用两个变量i和j分别指向当前s串和p串中遍历到的位置。
开始进行匹配，若i小于s串的长度，进行 while 循环。若当前两个字符相等，或着p中的字符是问号，则i和j分别加1。若 p[j] 是星号，那么我们要记录星号的位置，jStar 赋为j，此时j再自增1，iStar 赋为i。若当前 p[j] 不是星号，并且不能跟 p[i] 匹配上，那么此时就要靠星号了，若之前星号没出现过，那么就直接跪，比如 s = "aa" 和 p = "c\*"，此时 s[0] 和 p[0] 无法匹配，虽然 p[1] 是星号，但还是跪。如果星号之前出现过，可以强行续一波命，比如 s = "aa" 和 p = "*c"，当发现 s[1] 和 p[1] 无法匹配时，但是好在之前 p[0] 出现了星号，我们把 s[1] 交给 p[0] 的星号去匹配。至于如何知道之前有没有星号，这时就能看出 iStar 的作用了，因为其初始化为 -1，而遇到星号时，其就会被更新为i，那么我们只要检测 iStar 的值，就能知道是否可以使用星号续命。虽然成功续了命，匹配完了s中的所有字符，但是之后我们还要检查p串，此时没匹配完的p串里只能剩星号，不能有其他的字符，将连续的星号过滤掉，如果j不等于p的长度，则返回false
```
class Solution {
public:
    bool isMatch(string s, string p) {
        int i = 0, j = 0, jstar = -1, istar = -1;
	    while (i < s.size()) {
		    if (s[i] == p[j] || p[j] == '?') {
			    ++i; ++j;
		    }
		    else if (p[j] == '*') {
		    	istar = i;
		    	jstar = j++;
		    }
		    else if (istar >= 0) {
		    	i = ++istar;
		    	j = jstar + 1;
		    }
		    else
		    	return false;
	    }   
	    while (j < p.size() && p[j] == '*')
	    	++j;
	    return j == p.size();
    }
};
```

以上一输出

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5NzA4ODI0NDgsMTI4MzYyOTU4OF19
-->
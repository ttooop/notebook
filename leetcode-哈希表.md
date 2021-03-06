# 哈希表
## 30-Substring with Concatenation of All Words
### 描述
You are given a string,  **s**, and a list of words,  **words**, that are all of the same length. Find all starting indices of substring(s) in  **s**  that is a concatenation of each word in  **words**  exactly once and without any intervening characters.
**Example 1:**
**Input:
  s =** "barfoothefoobarman",
 **words =** ["foo","bar"]
**Output:** `[0,9]`
**Explanation:** Substrings starting at index 0 and 9 are "barfoor" and "foobar" respectively.
The output order does not matter, returning [9,0] is fine too.
### 解决
就是说给定一个长字符串，再给定几个长度相同的单词，让我们找出串联给定所有单词的子串的起始位置。假设 words 数组中有n个单词，每个单词的长度均为 len，那么实际上这道题就让我们出所有长度为 n\*len 的子串，使得其刚好是由 words 数组中的所有单词组成。那么我们就需要经常判断s串中长度为 len 的子串是否是 words 中的单词，为了快速的判断，就要使用哈希表，同时由于 words 数组可能有重复单词，所以就要用 HashMap 来建立所有的单词和其出现次数之间的映射，即统计每个单词出现的次数。
我们需要遍历s中所有长度为 n\*len 的子串，当剩余子串的长度小于 n\*len 时，就不用再判断了。所以我们的i从0开始，到 (int)s.size() - n\*len 结束就可以了，注意这里一定要将 s.size() 先转为整型数，再进行解法。一定要形成这样的习惯，一旦 size() 后面要减去数字时，先转为 int 型，因为 size() 的返回值是无符号型，一旦减去一个比自己大的数字，则会出错。对于每个遍历到的长度为 n\*len 的子串，需要验证其是否刚好由 words 中所有的单词构成，检查方法就是每次取长度为 len 的子串，看其是否是 words 中的单词。为了方便比较，我们建立另一个 HashMap，一旦取出的单词不在 words 中，直接 break 掉，否则就将其在新的 HashMap 中的映射值加1，还要检测若其映射值超过原 HashMap 中的映射值，也 break 掉，因为就算当前单词在 words 中，但若其出现的次数超过 words 中的次数，还是不合题意的。在 for 循环外面，若j正好等于n，说明我们检测的n个长度为 len 的子串都是 words 中的单词，并且刚好构成了 words，则将当前位置i加入结果 res 即可。
```
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        if(s.empty()||words.empty())
            return {};
        vector<int> ans;
        int n=words.size(),size=words[0].size();
        unordered_map<string,int> wordc;
        for(auto &word:words)
            ++wordc[word];
        for(int i=0;i<=(int)s.size()-n*size;++i){
            unordered_map<string,int> strc;
            int j=0;
            for(j=0;j<n;++j){
                string t=s.substr(i+j*size,size);
                if(!wordc.count(t))
                    break;
                ++strc[t];
                if(strc[t]>wordc[t])
                    break;
            }
            if(j==n)
                ans.push_back(i);
        }
        return ans;
    }
};
```
## 76-Minimum Window Substring
Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).
**Example:**
**Input: S** = "ADOBECODEBANC", **T** = "ABC"
**Output:** "BANC"
最小窗口子串
用哈希表来实现，哈希表来统计T串中字母的个数，遍历S串，对于S中每个遍历到的字母，都在哈希表中的映射值减1，若减1后映射值仍大于等于0，说明当前遍历到的字母是T串中的字母，使用计数器cnt，使其自增1。当cnt和T串字母个数相等时，说明此时窗口已经包含了T串中的所有字母，此时更新一个minlen和ans,这里minlen是用来记录出现过的包含T串所有字母的最短子串的长度，ans是这个最短子串。最后需要收缩左边界，由于遍历的时候对映射值减1 了，所以要把1加回来。
```
class Solution {
public:
    string minWindow(string s, string t) {
        string ans="";
        unordered_map<char,int> lettercnt;
        int left=0,cnt=0,minlen=INT_MAX;
        for(char c:t)
            lettercnt[c]++;
        for(int i=0;i<s.size();++i){
            if(--lettercnt[s[i]]>=0)
                cnt++;
            while(cnt==t.size()){
                if(minlen>i-left+1){
                    minlen=i-left+1;
                    ans=s.substr(left,minlen);
                }
                if(++lettercnt[s[left]]>0)
                    --cnt;
                ++left;
            }
        }
        return ans;
    }
};
```
以上一输出

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzNzMxMDI2ODUsLTg3NjcwODA2NywtMT
czMzE1MTg0XX0=
-->
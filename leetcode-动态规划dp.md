## 动态规划dp
 ### 64-Minimum Path Sum
 Given a  _m_  x  _n_  grid filled with non-negative numbers, find a path from top left to bottom right which  _minimizes_  the sum of all numbers along its path.
**Note:**  You can only move either down or right at any point in time.
**Example:**
**Input:**
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
**Output:** 7
**Explanation:** Because the path 1→3→1→1→1 minimizes the sum.
 ```
class Solution{
public:
	int minPathSum(vector<vector<int>>& grid){
		int m=grid.size(),n=grid[0].size();
		int dp[m][n];dp[0][0]=grid[0][0];
		for(int i=1;i<m;++i)
			dp[i][0]=dp[i-1][0]+grid[i][0];
		for(int j=1;j<n;++j)
			dp[0][j]=dp[0][j-1]+grid[0][j];
		for(int i=1;i<m;++i){
			for(int j=1;j<n;++j){
				dp[i][j]=dp[i-1][j-1]+min(dp[i-1][j],dp[i][j-1]);
			}
		}
		return dp[m-1][n-1];
	}
};
```
### 97-Interleaving String
Given  _s1_,  _s2_,  _s3_, find whether  _s3_  is formed by the interleaving of  _s1_  and  _s2_.
**Example 1:**
**Input:** s1 = "aabcc", s2 = "dbbca", _s3_ = "aadbbcbcac"
**Output:** true
**Example 2:**
**Input:** s1 = "aabcc", s2 = "dbbca", _s3_ = "aadbbbaccc"
**Output:** false
解：
动态规划
```
Ø d b b c a
Ø T F F F F F
a T F F F F F
a T T T T T F
b F T T F T F
c F F T T T T
c F F F T F T
```
大前提：s1长度+s2长度=s3长度，不等则返回false
当s1和s2是空串时，s3也必须是空串，因此直接给dp[0][0]赋值为true。
然后进行dp的边缘初始化：
第0行和第0列，按位与s3进行比较，若相同且上衣位置为true则赋值为true，反之赋值false
非边缘位置dp[i][j]：
与当前位置的左边和上边的位置有关，只要有一条路同，则这个点就可以为true，若左边为true，那么我们去除当前对应的s2中的字符串s2[j-1]和s3中对应的位置的字符s[j-1+i]相比，若相等则赋值为true，反之赋值false。因此状态方程为：
$$
dp[i][j]=(dp[i-1][j]\&\&s1[i-1]==s3[i-1+j])||(dp[i][j-1]\&\&s2[j-1]==s3[j-1+i])
$$
```
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        int n1=s1.size(),n2=s2.size(),n3=s3.size();
        if(n1+n2!=n3)
            return false;
        vector<vector<bool>> dp(n1+1,vector<bool>(n2+1));
        dp[0][0]=true;
        for(int i=1;i<=n1;++i){
           dp[i][0]=dp[i-1][0]&&(s1[i-1]==s3[i-1]);
        }
        for(int j=1;j<=n2;++j){
            dp[0][j]=dp[0][j-1]&&(s2[j-1]==s3[j-1]);
        }
        for(int i=1;i<=n1;++i){
            for(int j=1;j<=n2;++j){
                dp[i][j]=(dp[i-1][j]&&s1[i-1]==s3[i-1+j])||(dp[i][j-1]&&s2[j-1]==s3[j-1+i]);
            }
        }
        return dp[n1][n2];
    }
};
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwMzIyMTQyMTEsLTg3MzIwMTMwOF19
-->
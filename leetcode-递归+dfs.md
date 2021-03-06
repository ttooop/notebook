## 递归+dfs
### 51-N-Queens
#### 描述
n皇后问题
#### 解决
先建立一个n\*n的全部都是'.'的数组，从0行开始递归，在递归函数中，首先判断现在行数是否是n，若是则说明n个皇后已经放好，则直接插入到ans中。否则的话，遍历该行的所有列位置，行和列都确定后，开始验证当前位置是否会产生冲突，通过子函数isvalid来判断，子函数中，首先验证该列是否有冲突，就遍历之前的所有行，若某一行相同列有皇后则返回false；再验证两个对角线是否有冲突，通过坐标转换实现，若最后返回true，则放下新皇后，进行下一行的递归操作。
```
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
		vector<vector<string>> ans;
		vector<string> queens(n,string(n,'.'));
		helper(0,queens,ans);
		return ans;
	}
	void helper(int currow,vector<string>& queens,vector<vector<string>>& ans){
		int n=queens.size();
		if(currow==n){
			ans.push_back(queens);
			return ;
		}
		for(int i=0;i<n;++i){
			if(isvalid(queens,currow,i)){
				queens[currow][i]='Q';
				helper(currow+1,queens,ans);
				queens[currow][i]='.';
			}
		}
	}
	bool isvalid(vector<string>& queens,int row,int col){
		int n=queens.size();
		for(int i=0;i<n;++i){
			if(queens[i][col]=='Q')
				return false;
		}
		for(int i=row-1,j=col-1;i>=0&&j>=0;i--,j--){
			if(queens[i][j]=='Q')
				return false;
		}
		for(int i=row-1,j=col+1;i>=0&&j<n;i--,j++){
			if(queens[i][j]=='Q')
				return false;
		}
		return true;
	}
};
```
```
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> res;
        vector<int> queencol(n,-1);
        helper(0,queencol,res);
        return res;
    }
    void helper(int currow,vector<int>& queencol,vector<vector<string>>& res){
        int n=queencol.size();
        if(currow==n){
            vector<string> out(n,string(n,'.'));
            for(int i=0;i<n;++i){
                out[i][queencol[i]]='Q';
            }
            res.push_back(out);
            return ;
        }
        for(int i=0;i<n;++i){
            if(isvalid(queencol,currow,i)){
                queencol[currow]=i;
                helper(currow+1,queencol,res);
                queencol[currow]=-1;
            }
        }
    }
    bool isvalid(vector<int>& queencol,int row,int col){
        for(int i=0;i<row;++i){
            if(col==queencol[i]||abs(row-i)==abs(col-queencol[i]))
                return false;
        }
        return true;
    }
};
```
### 52-N-Queens
求n皇后个数问题，使用pos，pos[row]=col表示第row行的皇后在列col上，所以判断的时候就直接判断该row之前的pos[i]是否有等于col的，若有就返回false，对角线上的判断，通过绝对值abs(i-row)==abs(pos[i]-col)
```
class Solution {
public:
    int totalNQueens(int n) {
        int ans=0;
        vector<int> pos(n,-1);
        helper(pos,0,ans);
        return ans;
    }
    void helper(vector<int>& pos,int row,int& ans){
        if(row==pos.size()){
            ans++;
            return ;
        }
        for(int col=0;col<pos.size();++col){
            if(isvalid(pos,row,col)){
                pos[row]=col;
                helper(pos,row+1,ans);
                pos[row]=-1;
            }
        }
    }
    bool isvalid(vector<int>& pos,int row,int col){
        for(int i=0;i<row;++i){
            if(pos[i]==col||abs(i-row)==abs(pos[i]-col))
                return false;
        }
        return true;
    }
};
```
### 90-SubsetsⅡ
求子集，但是所得的集合中不能有重复的
**Example:**
**Input:** [1,2,2]
**Output:**
[
  [2], [1],[1,2,2],  [2,2], [1,2], []
]
解：
先排序！然后通过循环跳过重复的
```
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> out;
        sort(nums.begin(),nums.end());
        helper(0,nums,out,ans);
        return ans;
    }
    void helper(int start,vector<int>& nums,vector<int>& out,vector<vector<int>>& ans){
        ans.push_back(out);
        for(int i=start;i<nums.size();++i){
            out.push_back(nums[i]);
            helper(i+1,nums,out,ans);
            out.pop_back();
            while(i+1<nums.size()&&nums[i+1]==nums[i])
                ++i;
        }
    }
};
```

以上已输出
### 200. Number of Islands
```
**Example 2:**
**Input:**
11000
11000
00100
00011
**Output:** 3
```
解决：：DFS
```
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        //DFS
        if(grid.empty()||grid[0].empty())
            return 0;
        int m=grid.size(),n=grid[0].size(),ans=0;
        vector<vector<bool>> visited(m,vector<bool>(n));
        for(int i=0;i<m;++i){
            for(int j=0;j<n;++j){
                if(grid[i][j]=='0'||visited[i][j])
                    continue;
                helper(grid,visited,i,j);
                ++ans;
            }
        }
        return ans;
    }
    void helper(vector<vector<char>>& grid,vector<vector<bool>>& visited,int i,int j){
        if(i<0||i>=grid.size()||j<0||j>=grid[0].size()||grid[i][j]=='0'||visited[i][j])
            return ;
        visited[i][j]=true;
        helper(grid,visited,i-1,j);
        helper(grid,visited,i,j-1);
        helper(grid,visited,i+1,j);
        helper(grid,visited,i,j+1);
    }
};
```

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTY3MjUyMzUwNSwtMTg0NzcyMzU1NF19
-->
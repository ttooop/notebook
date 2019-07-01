## 二叉树
### 94. Binary Tree Inorder Traversal
中序遍历二叉数
**Example:**
**Input:** [1,null,2,3]
```
   1
    \
     2
    /
   3
   ```
**Output:** [1,3,2]
解：
递归方法
```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        inorder(root,res);
        return res;
    }
    void inorder(TreeNode* root,vector<int>& res){
        if(!root){
            return ;
        }
        if(root->left)
            inorder(root->left,res);
        res.push_back(root->val);
        if(root->right)
            inorder(root->right,res);
    }
};
```
### 96-Unique Binary Search Trees
给定数字n，求1-n可组成的二叉树个数
**Example:**
**Input:** 3
**Output:** 5
**Explanation:** Given _n_ = 3, there are a total of 5 unique BST's:
```
   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
   ```
   解：
   卡塔兰树——排列规律
$$
C_0=1
$$
$$
C_{n+1}=\sum_{i=0}^nC_iC_{n-i}
$$
```
class Solution {
public:
    int numTrees(int n) {
        vector<int> dp(n+1);
        dp[0]=dp[1]=1;
        for(int i=2;i<=n;++i){
            for(int j=0;j<i;++j){
                dp[i]+=dp[j]*dp[i-j-1];
            }
        }
        return dp[n];
    }
};
```
### 95. Unique Binary Search Trees II
求所有二叉树的可能情况
```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<TreeNode*> generateTrees(int n) {
        if(n==0)
            return {};
        return helper(1,n);
    }
    vector<TreeNode*> helper(int start,int end){
        if(start>end)
            return {nullptr};
        vector<TreeNode*> res;
        for(int i=start;i<=end;++i){
            auto left=helper(start,i-1),right=helper(i+1,end);
            for(auto a:left){
                for(auto b:right){
                    TreeNode *node=new TreeNode(i);
                    node->left=a;
                    node->right=b;
                    res.push_back(node);
                }
            }
        }
        return res;
    }
};
```
### 98-Validate Binary Search Tree
判断二叉树是否是二叉搜索树，即左子结点的数都小于根节点，右子节点的都大于根节点
**Example 2:**
```
    5
   / \
  1   4
     / \
    3   6
```
**Input:** [5,1,4,null,null,3,6]
**Output:** false
**Explanation:** The root node's value is 5 but its right child's value is 4.
解：
先通过中序遍历遍历二叉树，得到结果数列，然后进行检查，若所得数列不是递增的，那么就返回false
```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        //先进行中序遍历，得到的数组进行检查，若不是递增顺序，则返回false
        if(!root)
            return true;
        vector<int> res;
        inorder(root,res);
        for(int i=0;i<res.size()-1;++i){
            if(res[i+1]<=res[i])
                return false;
        }
        return true;
    }
    void inorder(TreeNode* root,vector<int>& res){
        if(!root)
            return ;
        inorder(root->left,res);
        res.push_back(root->val);
        inorder(root->right,res);
    }
};
```

<!--stackedit_data:
eyJoaXN0b3J5IjpbNzE4ODA5NjM0LC0xODcxNzc1NzY2XX0=
-->
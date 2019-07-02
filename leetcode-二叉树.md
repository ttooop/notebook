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

以上已输出

### 99-Recover Binary Search Tree
还原一个二叉搜索树：
**Example 2:**

**Input:** [3,1,4,null,null,2]
```
  3
 / \
1   4
   /
  2
```
**Output:** [2,1,4,null,null,3]
```
  2
 / \
1   4
   /
  3
  ```
  解：
 建立两个动态数组，一个存放通过中序遍历得到的二叉树结点值，另一个用于存储这些结点
 然后将vals中的值进行排序，最后赋值给结点中的值，使其满足二叉搜索树
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
    void recoverTree(TreeNode* root) {
        if(!root)
            return ;
        vector<int> vals;
        vector<TreeNode*> list;
        inorder(root,list,vals);
        sort(vals.begin(),vals.end());
        for(int i=0;i<vals.size();++i){
            list[i]->val=vals[i];
        }
    }
    void inorder(TreeNode* root,vector<TreeNode*>& list,vector<int>& vals){
        if(!root)
            return ;
        inorder(root->left,list,vals);
        list.push_back(root);
        vals.push_back(root->val);
        inorder(root->right,list,vals);
    }
};
 ```
 ### 105-Construct Binary Tree from Preorder and Inorder Traversal
根据先序遍历序列和中序遍历序列建立二叉树
For example, given
preorder = [3,9,20,15,7]
inorder = [9,3,15,20,7]
Return the following binary tree:
```
    3
   / \
  9  20
    /  \
   15   7
   ```
 解：
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
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        return buildtree(preorder,0,preorder.size()-1,inorder,0,inorder.size()-1);
    }
    TreeNode* buildtree(vector<int>& preorder,int pleft,int pright,vector<int>& inorder,int ileft,int iright){
        if(pleft>pright||ileft>iright)
            return NULL;
        int i=0;
        for(ileft;i<=iright;++i){
            if(preorder[pleft]==inorder[i])
                break;
        }
        TreeNode *cur=new TreeNode(preorder[pleft]);
        cur->left=buildtree(preorder,pleft+1,pleft+i-ileft,inorder,ileft,i-1);
        cur->right=buildtree(preorder,pleft+i-ileft+1,pright,inorder,i+1,iright);
        return cur;
    }
};
```
### 106. Construct Binary Tree from Inorder and Postorder Traversal
由中序遍历序列和后序遍历序列建立二叉树
For example, given
inorder = [9,3,15,20,7]
postorder = [9,15,7,20,3]
Return the following binary tree:
```
    3
   / \
  9  20
    /  \
   15   7
   ```
解：
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
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        return buildtree(inorder,0,inorder.size()-1,postorder,0,postorder.size()-1);
    }
    TreeNode* buildtree(vector<int>& inorder,int ileft,int iright,vector<int>& postorder,int pleft,int pright){
        if(ileft>iright||pleft>pright)
            return NULL;
        int i=0;
        for(i=ileft;i<=iright;++i){
            if(postorder[pright]==inorder[i])
                break;
        }
        TreeNode* cur=new TreeNode(postorder[pright]);
        cur->left=buildtree(inorder,ileft,i-1,postorder,pleft,pleft+i-ileft-1);
        cur->right=buildtree(inorder,i+1,iright,postorder,pleft+i-ileft,pright-1);
        return cur;
    }
};
```
### 110. Balanced Binary Tree
判断二叉树是否是平衡二叉树（叶子结点的深度差小于1）
解：
调用getdepth函数，获得左右子节点的深度，求绝对值差
getdepth函数通过递归实现
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
    bool isBalanced(TreeNode* root) {
        if(!root)
            return true;
        if(abs(getdepth(root->left)-getdepth(root->right))>1)
            return false;
        return isBalanced(root->left)&&isBalanced(root->right);
    }
    int getdepth(TreeNode* root){
        if(!root)
            return 0;
        return 1+max(getdepth(root->left),getdepth(root->right));
    }
};
```
### 112-Path Sum
给定二叉树和一个sum值，查看是否有从根节点到叶结点之和是否等于sum
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
    bool hasPathSum(TreeNode* root, int sum) {
        if(!root)
            return false;
        if(!root->left&&!root->right&&root->val==sum)
            return true;
        return hasPathSum(root->left,sum-root->val)||hasPathSum(root->right,sum-root->val);
    }
};
```
### Path SumⅡ
求二叉树所有路径的和加起来是sum的所有组合
解：
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
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        vector<vector<int>> ans;
        vector<int> out;
        helper(root,sum,out,ans);
        return ans;
    }
    void helper(TreeNode* root,int sum,vector<int>& out,vector<vector<int>>& ans){
        if(!root)
            return ;
        out.push_back(root->val);
        if(!root->left&&!root->right&&root->val==sum){
            ans.push_back(out);
        }
        helper(root->left,sum-root->val,out,ans);
        helper(root->right,sum-root->val,out,ans);
        out.pop_back();
    }
};
```
### Flatten Binary Tree to Linked List
平铺二叉树
For example, given the following tree:
```
    1
   / \
  2   5
 / \   \
3   4   6
The flattened tree should look like:
1
 \
  2
   \
    3
     \
      4
       \
        5
         \
          6
```
解：
递归方法，先找到最左子节点，然后返回到其父节点，把其父节点和右子节点断开，将原左子节点脸上父节点的右子节点，再把原右子节点连到新右子节点上，返回上一父节点做相同操作
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
    void flatten(TreeNode* root) {
        if(!root)
            return ;
        if(root->left)
            flatten(root->left);
        if(root->right)
            flatten(root->right);
        TreeNode *tmp=root->right;
        root->right=root->left;
        root->left=NULL;
        while(root->right)
            root=root->right;
        root->right=tmp;
    }
};
```

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTg4MDQ3NjQ3OF19
-->
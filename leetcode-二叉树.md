## 二叉树
### 94. Binary Tree Inorder Traversal
中序遍历二叉数
**Example:**
**Input:** [1,null,2,3]
   1
    \
     2
    /
   3
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

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTcyMTIyNDczM119
-->
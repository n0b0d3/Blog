---
title: 二叉树的后序遍历
date: 2017-05-30 12:41:12
tags: 算法
---

#### 二叉树的后序遍历


对于二叉树的后序遍历，按照“左孩子-右孩子-根节点”的顺序进行访问，其递归算法如下：

```c++
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
```
```c++
void preOrder(TreeNode * root) {
    if(!root) return;
    preOrder(root->left); 
    preOrder(root->right);
    cout << root->val << ' ';
}
```

二叉树的后序非递归算法是三种遍历方式中最复杂的一种，其思路如下：

1. 检查当前节点的左右子树是否都为空，若都为空，则访问节点，同时从栈中取出下一节点，并保留刚才访问的节点。

2. 若上次访问节点不为空，同时，当前节点的左孩子或者右孩子是刚访问的节点，则访问节点，并从栈中取出下一节点，同时保留刚才访问的节点。

3. 若不满足上述条件，将不为空的孩子节点入栈。

```c++
vector<int> postorderTraversal(TreeNode *root) {
    vector<int> ret;
    stack<TreeNode *> result;
    if(root == NULL)
        return ret;
    result.push(root);
    TreeNode * cur = NULL;
    TreeNode * pre = NULL;
    while(!result.empty()) {
        cur = result.top();
        if((cur->left == NULL && cur->right == NULL) ||
                (pre != NULL && (pre == cur->right || pre == cur->left))) {
            ret.push_back(cur->val);
            result.pop();
            pre = cur;
        }
        else {
            if(cur->right) {
                result.push(cur->right);
            }
            if(cur->left) {
                result.push(cur->left);
            }
        }
    }
    return ret;
}
```

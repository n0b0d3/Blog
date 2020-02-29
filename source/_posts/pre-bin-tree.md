---
title: 二叉树的前序遍历
date: 2017-05-24 12:36:52
tags: 算法
---

#### 二叉树的前序遍历

二叉树是一种重要的数据结构，很多数据结构都是基于二叉树的衍生。

对于二叉树的前序遍历，按照“根节点-左孩子-右孩子”的顺序进行访问。二叉树的前序遍历递归算法十分简单：

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
    cout << root->val << ' ';
    preOrder(root->left);
    preOrder(root->right);
}
```

二叉树的前序非递归算法稍微复杂一点，其思路如下：

对于任一节点 P：

1. 访问节点 P，并将节点 P 入栈

2. 判断节点 P 节点的左孩子是否为空，不为空，则迭代查找左孩子，并将节点入栈，直到为空；若为空，从栈顶取出节点作为 P 节点，然后查找 P 的右孩子节点。

3. 直到 P 为空并且栈为空，遍历结束。

```c++
vector<int> preorderTraversal(TreeNode *root) {
    vector<int> result;
    stack<TreeNode *> ret;
    TreeNode * p = root;
    while(p != NULL || !ret.empty()) {
        while(p != NULL) {
            result.push_back(p->val);
            ret.push(p);
            p = p->left;
        }
        if(!ret.empty()) {
            p = ret.top();
            ret.pop();
            p = p->right;
        }
    }
    return result;
}
```

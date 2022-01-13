题源：https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/

第一种方法：中序遍历该二叉搜索树，得到一个升序序列之后直接返回结果

```c++
class Solution {
public:

    void midTraverse(vector<int> &s,TreeNode* root) {
        if (root) {
            midTraverse(s, root->left);
            s.push_back(root->val);
            midTraverse(s, root->right);
        }
    }

    int kthLargest(TreeNode* root, int k) {
        //先中序遍历排序，排出一个递增的序列，然后找倒数第k个数
        vector<int> s;
        midTraverse(s,root);
        return s[s.size() - k];
    }
};
```

第二种方法：按照右->根->左的顺序（中序遍历倒序）来遍历二叉树，在遍历的过程中返回结果

```c++

class Solution {
public: 
    int res;
    int kthLargest(TreeNode* root, int k) {
        dfs(root,k);
        return res;
    }
    void dfs(TreeNode* root ,int &k) 
    {
        if(!root) return;
        dfs(root->right,k); //右
        k--;
        if(!k) res = root->val; //根
        dfs(root->left,k); //左
    }
};
```


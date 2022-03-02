题源：https://leetcode-cn.com/problems/path-sum-ii/

深度遍历，代码如下：

```c++
class Solution {
public:
    void dfs(vector<vector<int>>& res, TreeNode* root, int targetSum, vector<int> tmp) {
        if(root == nullptr) return;
        tmp.emplace_back(root->val);
        if(targetSum == root->val && !root->left && !root->right) res.emplace_back(tmp);
        else {
            targetSum = targetSum - root->val;
            if(root->left) dfs(res, root->left, targetSum, tmp);
            if(root->right) dfs(res, root->right, targetSum, tmp);
        }
    }

    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        vector<vector<int>> res;
        dfs(res, root, targetSum, {});
        return res;
    }
};
```

我的代码执行用时16ms，内存消耗为33MB，但是官方的dfs只需要8ms，19.4MB，代码如下：

```c++
class Solution {
public:
    vector<vector<int>> ret;
    vector<int> path;

    void dfs(TreeNode* root, int targetSum) {
        if (root == nullptr) {
            return;
        }
        path.emplace_back(root->val);
        targetSum -= root->val;
        if (root->left == nullptr && root->right == nullptr && targetSum == 0) {
            ret.emplace_back(path);
        }
        dfs(root->left, targetSum);
        dfs(root->right, targetSum);
        path.pop_back();
    }

    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        dfs(root, targetSum);
        return ret;
    }
};
```

主要是它把res和tmp放到外面去了，没有作为函数参数来传递。
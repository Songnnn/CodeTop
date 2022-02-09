题源：https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/

这个是自底向上的层序遍历，最简单的方法就是正常层序遍历，然后把结果倒过来就行。

无论用什么方法，遍历的时候都只能是自顶向下地遍历，这里我用队来实现层序遍历，用栈来保存遍历结果。

```c++
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        queue<TreeNode*> q;
        stack<vector<int>> s;
        vector<vector<int>> res;
        if(!root) return res;
        q.push(root);
        while(!q.empty()) {
            int size = q.size();
            vector<int> tmp;
            while(size--) {
                TreeNode* top = q.front();
                q.pop();
                tmp.emplace_back(top->val);
                if(top->left) q.push(top->left);
                if(top->right) q.push(top->right);
            }
            s.push(tmp);
        }

        while(!s.empty()) {
            res.emplace_back(s.top());
            s.pop();
        }
        return res;
    }
};
```


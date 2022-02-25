题源：https://leetcode-cn.com/problems/kth-smallest-element-in-a-sorted-matrix/

要求的是第K小的元素，我们需要搞清楚，n*n的矩阵中，大小顺序是怎么排的。

由于每行每列都是升序，所以大小顺序是斜着排的。比如对于[[1,5,9],[10,11,13],[12,13,15]]这样一个3*3的矩阵来说，升序序列是[1],[5,10],[9,11,12],[13,13],[15]，但其实这个升序并不严格。

我们需要先判断第K小的元素在哪条斜线上。由于升序并不严格，所以我们需要检查两条斜线，这个步骤需要$O(n)$的时间。

随后我们对两条斜线的数排序，这个步骤需要$O(logn)$的时间，然后我们就找到答案了。这个方法需要很细心，我懒得写了。

还有一种方法是归并，其实这题有点类似“合并K个升序链表”。每一行其实就是一个有序的链表，我们每次从各个链表头中选出最小的一个，进行K次这样的操作，就可以得到第K小元素。

具体操作的话，我们维护一个优先队列，该队列中存放了每行的第一个元素，我们需要弹出k次队头。关键的一点就是，如果我们让num[i]\[0]出队，那num[i]\[1]就需要入队，因为num[i]\[1]也有可能是当前的最小。代码很好看，如下：

```c++
class Solution {
public:
    int kthSmallest(vector<vector<int>>& matrix, int k) {
        struct point {
            int val, x, y;
            point(int val, int x, int y) :val(val), x(x), y(y) {}
            bool operator > (const point& p) const { return this->val >= p.val; }
        };
        priority_queue<point, vector<point>, greater<point>> q;
        int n = matrix.size();
        for (int i = 0; i < n; i++) q.emplace(matrix[i][0], i, 0);
        for (int i = 0; i < k - 1; i++) {
            point top = q.top();
            q.pop();
            if (top.y != n - 1) q.emplace(matrix[top.x][top.y + 1], top.x, top.y + 1);
        }
        return q.top().val;
    }
};
```




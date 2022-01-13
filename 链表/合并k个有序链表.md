题源：https://leetcode-cn.com/problems/merge-k-sorted-lists/

没有比这更简单的题了，这题其实就是“合并两个有序链表”的麻烦版。

对于两个有序链表，以时间复杂度为$O(n+m)$，空间复杂度为$O(1)$的代价进行合并：

这里清晰一下思路：

1. 首先设置一个空的头节点head，方便操作
2. 然后设置一个tail指针（tail初始等于head），**指向下一个插入位置的前一个位置**，设置aPtr和bPtr两个指针，分别指向两个有序链表的**待排序部分**的第一个节点
3. 当aPtr和bPtr都不为空时，取较小值合并，修改链表的next；只要aPtr和bPtr有一个为空，那将非空的直接并过去就行了

由于不使用任何额外空间，所以只能原地调整next指针来完成合并，要注意next的修改顺序

代码如下：

```c++
ListNode* mergeTwoLists(ListNode *a, ListNode *b) {
    if ((!a) || (!b)) return a ? a : b;
    ListNode head, *tail = &head, *aPtr = a, *bPtr = b;
    while (aPtr && bPtr) {
        if (aPtr->val < bPtr->val) {
            tail->next = aPtr; aPtr = aPtr->next;
        } else {
            tail->next = bPtr; bPtr = bPtr->next;
        }
        tail = tail->next;
    }
    tail->next = (aPtr ? aPtr : bPtr);
    return head.next;
}
```

本题中，直接用一个变量ans维护已经合并好的链表，然后依次并过去就行了

时间复杂度为$O(k^2*n)$，k是链表个数，n是链表的最长长度，第k次合并是$O(k*n)$，总共有k次，所以是k平方，空间复杂度为$O(1)$，代码如下：

```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode *a, ListNode *b) {
        if ((!a) || (!b)) return a ? a : b;
        ListNode head, *tail = &head, *aPtr = a, *bPtr = b;
        while (aPtr && bPtr) {
            if (aPtr->val < bPtr->val) {
                tail->next = aPtr; aPtr = aPtr->next;
            } else {
                tail->next = bPtr; bPtr = bPtr->next;
            }
            tail = tail->next;
        }
        tail->next = (aPtr ? aPtr : bPtr);
        return head.next;
    }

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        ListNode *ans = nullptr;
        for (size_t i = 0; i < lists.size(); ++i) {
            ans = mergeTwoLists(ans, lists[i]);
        }
        return ans;
    }
};
```

还可以再优化一下，不是一个个并过去，而是用分治的方法，两两合并，时间复杂度为$O(kn*logk)$，递归会使用栈空间，所以空间复杂度为$O(logk)$，代码如下：

```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode *a, ListNode *b) {
        if ((!a) || (!b)) return a ? a : b;
        ListNode head, *tail = &head, *aPtr = a, *bPtr = b;
        while (aPtr && bPtr) {
            if (aPtr->val < bPtr->val) {
                tail->next = aPtr; aPtr = aPtr->next;
            } else {
                tail->next = bPtr; bPtr = bPtr->next;
            }
            tail = tail->next;
        }
        tail->next = (aPtr ? aPtr : bPtr);
        return head.next;
    }

    ListNode* merge(vector <ListNode*> &lists, int l, int r) {
        if (l == r) return lists[l];
        if (l > r) return nullptr;
        int mid = (l + r) >> 1;
        return mergeTwoLists(merge(lists, l, mid), merge(lists, mid + 1, r));
    }

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        return merge(lists, 0, lists.size() - 1);
    }
};
```

还可以使用优先队列，取每个链表的待合并部分的第一位，即最小值，放入优先队列（队头到队尾默认升序），每次从优先队列里拿队头就行了。时间复杂度为$O(kn*logk)$，优先队列中的元素最多是k个，所以空间复杂度为$O(k)$，代码如下：

```c++
class Solution {
public:
    struct Status {
        int val;
        ListNode *ptr;
        bool operator < (const Status &rhs) const {
            return val > rhs.val;
        }
    };

    priority_queue <Status> q;

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        for (auto node: lists) {
            if (node) q.push({node->val, node});
        }
        ListNode head, *tail = &head;
        while (!q.empty()) {
            auto f = q.top(); q.pop();
            tail->next = f.ptr; 
            tail = tail->next;
            if (f.ptr->next) q.push({f.ptr->next->val, f.ptr->next});
        }
        return head.next;
    }
};
```












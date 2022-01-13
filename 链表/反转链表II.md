题源：https://leetcode-cn.com/problems/reverse-linked-list-ii/

没什么好说的，基本就是翻转链表的思路，链表题记得一定要画图。

我更喜欢递归的翻转函数，代码如下：

```c++
class Solution {
public:
    ListNode* rev(ListNode* head, ListNode* tail) {
        if(head == tail || head == nullptr) return head;
        ListNode* tmp = rev(head->next, tail);
        tmp->next = head;
        head->next = nullptr;
        return head;
    }

    ListNode* reverseBetween(ListNode* head, int left, int right) {
        if(head->next == nullptr || left == right) return head;
        ListNode* pre = head; //start的前一个节点
        ListNode* start = head; //需要翻转的链表部分的第一个节点
        for(int i = 0; i < left - 1; i++) {
            pre = start;
            start = start->next;
        }
        ListNode* tail = head; //需要翻转的链表部分的最后一个节点
        for(int i = 0; i < right - 1; i++) tail = tail->next;
        ListNode* last = tail->next; //后面不需要翻转的部分
        rev(start, tail);
        if(pre == start) { //当start就是head的时候
            pre->next = last;
            return tail;
        }
        pre->next->next = last;
        pre->next = tail;
        return head;
    }
};
```

还有迭代的方法，一边遍历一边翻转，代码如下：

```c++
class Solution {
public:
    ListNode *reverseBetween(ListNode *head, int left, int right) {
        // 设置 dummyNode 是这一类问题的一般做法
        ListNode *dummyNode = new ListNode(-1);
        dummyNode->next = head;
        ListNode *pre = dummyNode;
        for (int i = 0; i < left - 1; i++) {
            pre = pre->next;
        }
        ListNode *cur = pre->next;
        ListNode *next;
        for (int i = 0; i < right - left; i++) {
            next = cur->next;
            cur->next = next->next;
            next->next = pre->next;
            pre->next = next;
        }
        return dummyNode->next;
    }
};
```


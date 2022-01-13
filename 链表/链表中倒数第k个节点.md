题源：https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/

最简单的方法那肯定是遍历两次链表，第一次得到链表的长度，第二次返回结果

代码如下：

```c++
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        //遍历两边链表
        int size = 0;
        ListNode* p = head;
        while(p) {
            size++;
            p = p->next;
        }

        int cnt = 0;
        while(head) {
            cnt++;
            if(cnt == size - k + 1) break;
            head = head->next;
        }
        return head;
    }
};
```

更快的方法是快慢指针，我一开始想了双指针，我最开始想的是让fast和low相隔size-k，这样的话fast就直接返回结果了，但是size不知道，然后我就没多想了。其实倒数第k个节点，其实就是与链表尾部相隔k个节点的节点，所以可以让low=head，fast指针领先low指针k步，然后一起向前，当fast走到尾部的时候，low就到了。代码如下：

```c++
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) 
    {
        auto pFast = head;
        // 快指针向前走k步
        while(k-- > 0)
            pFast = pFast->next;
        // 初始化慢指针
        auto pSlow = head;
        // 快慢指针同时走，当快指针走到NULL时慢指针正好走到倒数第k个节点
        while(pFast != NULL)
        {
            pSlow = pSlow->next;
            pFast = pFast->next;
        }
        return pSlow;
    }
};
```


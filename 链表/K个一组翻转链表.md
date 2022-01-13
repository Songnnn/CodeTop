题源：https://leetcode-cn.com/problems/reverse-nodes-in-k-group/

本题是翻转链表的升级版，不过操作还是一样的，就是更绕一点，思路需要很清晰，做的时候一定一定要画图。

时间复杂度为$O(N)$，代码如下：

```c++
class Solution {
public:
    ListNode* reverse(ListNode* start, ListNode* end) {
        if(start == end) return start;
        ListNode* temp = reverse(start->next, end);
        temp->next = start;
        start->next = nullptr;
        return start;
    }
    
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* low = head;
        ListNode* fast = head;
        ListNode* next_head = nullptr;

        int tag = 0;
        while(fast) {
            for(int i = 0; i < k - 1; i++) {
                if(fast) fast = fast->next;
            }
            if(fast == nullptr) break;
            ListNode* temp = fast->next; //翻转之前，保存下一翻转块的头
            reverse(low, fast); //翻转之后，low和fast指向的位置不变
            
            if(tag == 1) { //不是第一次进行翻转
                next_head->next = fast;
                next_head = low; //保存好已翻转块的尾部
            }
            if(tag == 0) { //第一次进行翻转
                tag = 1;
                next_head = low;
                head = fast;
            }
            low = temp;
            fast = low;
        }
        if(low != nullptr) next_head->next = low; //如果还剩少于k个节点，直接连上就行
        return head;
    }
};
```

reverse(low, fast)函数，将low到fast的节点翻转，并且low和fast指向的节点不变。


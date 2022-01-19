题源：https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/

前后指针遍历即可，为了算法的普适性，在最前方加入头节点dummy，代码如下：

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if(head == nullptr) return nullptr;
        ListNode* dummy = new ListNode(101);
        dummy->next = head;
        ListNode* slow = dummy;
        ListNode* fast = slow->next;
        
        while(fast) {
            ListNode* ffast = fast->next;
            if(ffast == nullptr || fast->val != ffast->val) {
                slow = fast;
                fast = slow->next;
            }
            else {
                while(ffast && fast->val == ffast->val) {
                    fast = ffast;
                    ffast = ffast->next;
                }
                fast = ffast;
                slow->next = fast;
            }            
        }
        return dummy->next;
    }
};
```


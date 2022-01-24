题源：https://leetcode-cn.com/problems/restore-ip-addresses/

回溯即可，代码如下：

```c++
class Solution {
public:
    void helper(string s, vector<string>& res, int cnt, string tmp) {
        if(cnt <= 0) return;
        for(int i = 1; i < 4; i++) {
            if(s.size() < i) break;
            string sub = s.substr(0, i);
            if(s[0] == '0' && i > 1) continue; //0不可以作为非零数字的开头
            else if(stoi(sub) >= 0 && stoi(sub) <= 255) {
                string surplus = s.substr(i, s.size() - i);
                if(surplus.size() == 0 && cnt == 1) res.emplace_back(tmp + sub);
                else helper(surplus, res, cnt - 1, tmp + sub + ".");
            }
        }
    }

    vector<string> restoreIpAddresses(string s) {
        vector<string> res;
        if(s.size() < 4 || s.size() > 12) return res;
        helper(s, res, 4, "");
        return res;
    }
};
```

cnt表示还需要填入的IP字段数量，最开始是4，唯一要注意的地方就是这里不可以有先导0。
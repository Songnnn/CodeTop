题源：https://leetcode-cn.com/problems/top-k-frequent-words/

一看就知道必然要用到哈希，这里的难点是，单词只能作为键，那其出现次数就是值，但是我们需要先对其值进行排序，map是只对键排序的。也想过将出现次数作为键，但是出现次数有可能相等，所以还是不行。于是只好转存vector再做排序。代码如下：

```c++
class Solution {
public:
    static bool cmp(const pair<string, int> a, const pair<string, int> b) {
        if(a.second == b.second) return a.first < b.first;
        return a.second > b.second;
    }

    vector<string> topKFrequent(vector<string>& words, int k) {
        unordered_map<string, int> hash;
        for(int i = 0; i < words.size(); i++) hash[words[i]]++;
        
        vector<pair<string, int>> tmp(hash.begin(), hash.end());
        sort(tmp.begin(), tmp.end(), cmp);
        vector<string> res;
        for(auto it = tmp.begin(); it != tmp.end(); it++) {
            res.emplace_back(it->first);
            if(--k == 0) break;
        }
        return res;
    }
};
```

时间复杂度主要是排序的过程，其他倒是没什么。当然，还可以直接创建一个自定义排序规则的优先队列。时间复杂度一样的。
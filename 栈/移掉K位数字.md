题源：https://leetcode-cn.com/problems/remove-k-digits/

无论移掉哪K位数字，最后的结果位数都是固定的。所以我们要尽可能移除前面的更大的数字，比如“143289”，“4”的移除优先级要比“9”更高，因为移除“4”之后的数字为“13289”，移除“9”之后的数字为“14328”，“13289”要小于“14328”。所以我们可以对“143298”中的每个数字进行排序，设置一个优先队列，谁的排除优先级最高谁就是队列的top，代码如下：

```c++
class Solution {
public:
    
    string removeKdigits(string num, int k) {

        auto cmp = [=](int i, int j) {
            string s1 = num;
            string s2 = num;
            s1.erase(i, 1);
            s2.erase(j, 1);
            return stoi(s1) > stoi(s2);
        };

        priority_queue<int, vector<int>, decltype(cmp)> q(cmp);
        for (int i = 0; i < num.size(); i++) q.push(i);
        //排序失败
        return num;
    }
};

```

然而，排序过程并没有按照我的预想排序，排序失败了，所以这个方法还是不行。

其实我们可以总结出排序的规律：**从左往右遍历，对于每个num[i]，如果num[i] > num[i+1]，那么num[i]就需要被移除。**所以这里我们可以使用单调栈。

代码如下：

```c++
class Solution {
public:
    string removeKdigits(string num, int k) {
        vector<char> stk;
        for (auto& digit: num) {
            while (stk.size() > 0 && stk.back() > digit && k) {
                stk.pop_back();
                k -= 1;
            }
            stk.push_back(digit);
        }

        for (; k > 0; --k) {
            stk.pop_back();
        }

        string ans = "";
        bool isLeadingZero = true;
        for (auto& digit: stk) {
            if (isLeadingZero && digit == '0') {
                continue;
            }
            isLeadingZero = false;
            ans += digit;
        }
        return ans == "" ? "0" : ans;
    }
};
```


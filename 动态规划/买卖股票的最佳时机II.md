题源：https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/

本题的难点在于你可以多次买入卖出。

还是动态规划，dp[i]表示prices[0..i]进行多次交易的最大利润，只有当prices[i]大于prices[i-1]的时候，dp才需要更新，因为如果prices[i]小于prices[i-1]，那永远轮不到prices[i]卖出，只有当出现更好的卖出机会时，dp才需要更新。

当prices[i]大于prices[i-1]时，有两种情况：

- prices[i-1]被卖出了，那我们直接改在prices[i]卖出即可，此时利润要比dp[i-1]多出prices[i] - prices[i-1]，即dp[i] = dp[i - 1] + prices[i] - prices[i - 1]
- prices[i-1]没有被卖出，那我们直接买入prices[i-1]，卖出prices[i]即可（要注意，prices[i-1]之前不会有更低的价格可以买入了），即dp[i] = dp[i - 1] + prices[i] - prices[i - 1];

可以看到两种情况是一样的。

时间复杂度和空间复杂度都是$O(n)$，代码如下：

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int size = prices.size();
        if(size == 1) return 0;
        vector<int> dp(size);
        for(int i = 1; i < size; i++) {
            dp[i] = dp[i - 1];
            if(prices[i] > prices[i - 1]) {
                dp[i] = dp[i - 1] + prices[i] - prices[i - 1];
            }
        }
        return dp[size - 1];
    }
};
```

看见题解中有人给出了更清晰的解释，那就是每天都买卖。比如[7,1,5,3,6,7,4]，买7卖1会亏，所以不计入利润；买1卖5，赚4；买5卖3会亏；买3卖6，赚3；买6卖7，赚1；买7卖4会亏，所以总共赚8。

看了下提交记录，发现我半年前做过这题，用的居然是二分法，时空复杂度都很差，这里贴一下。。

```c++
class Solution {
public:
    int func(vector<int> prices, int left, int right) {
        if(right - left == 0) return 0;
        else if(right - left == 1) {
            int p = prices[right] - prices[left];
            if(p > 0) return p;
            else return 0;
        }
        int mid = (left + right) / 2;
        int left_min = prices[left];
        int right_max = prices[mid];
        for(int i = left; i <= mid; i++) {
            if(prices[i] < left_min) left_min = prices[i];
        }
        for(int i = mid; i<= right; i++) {
            if(prices[i] > right_max) right_max = prices[i];
        }
        int max1 = right_max - left_min;
        int max2 = func(prices, left, mid) + func(prices, mid, right);
        return max1>max2?max1:max2;
    }

    int maxProfit(vector<int>& prices) {
        //二分法
        int size = prices.size();
        return func(prices, 0, size - 1);
    }
};
```


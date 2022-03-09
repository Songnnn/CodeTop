题源：https://leetcode-cn.com/problems/coin-change-2/

经典背包问题，dp[i]\[j]表示用coins[0]到coins[i]凑成j的方案数，代码如下：

```c++
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int size = coins.size();
        vector<vector<int>> dp(size, vector<int>(amount + 1));
        //初始化第一行
        for(int j = 1; j < amount + 1; j++) {
            if(j % coins[0] == 0) dp[0][j] = 1;
        }
        //初始化第一列，要注意，令coins[0..i]凑成0的方案为一种
        for(int i = 0; i < size; i++) dp[i][0] = 1;            
        
        for(int i = 1; i < size; i++) {
            for(int j = 1; j < amount + 1; j++) {
                dp[i][j] = dp[i - 1][j]; //不拿coins[i]
                if(coins[i] <= j) dp[i][j] += dp[i][j - coins[i]];
            }
        }
        return dp[size - 1][amount];
    }
};
```

精简一下，代码如下：

```c++
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int size = coins.size();
        vector<int> dp(amount + 1);
        dp[0] = 1;
        //初始化第一行
        for(int j = 1; j < amount + 1; j++) {
            if(j % coins[0] == 0) dp[j] = 1;
        }
            
        for(int i = 1; i < size; i++) {
            for(int j = 1; j < amount + 1; j++) {
                if(coins[i] <= j) dp[j] += dp[j - coins[i]];
            }
        }
        return dp[amount];
    }
};
```


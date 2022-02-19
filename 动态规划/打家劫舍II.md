题源：https://leetcode-cn.com/problems/house-robber-ii/

回溯会超时，这里可以用动态规划，设dp[i]表示以nums[i]为结尾的最大金额，**nums[i]不一定被偷**

递推关系如下：
$$
d p[i]=\max (d p[i-2]+n u m s[i], d p[i-1])
$$
本题的难点在于房屋是环状的，这意味着nums[0]和nums[n-1]不能同时被偷，其实这对于dp[i]之前的推导没有任何影响，但是在推导dp[i]的值时就麻烦了，我最开始的想法是设置一个tag，标记第一个房屋是否被偷，这样来判断能不能偷最后一个，但是这个tag维护不了，在推导dp[i]的时候，你无法判断它取的最大值是否包含第一个房屋。所以这里可以用一个巧妙的方法，**对于非环，我们完全不用考虑这个问题**，所以对于nums = [1,2,3,1]，我将[1,2,3]看作非环，计算其最大金额（即表示我一定不偷最后一个）；再将[2,3,1]看作非环，计算其最大金额（即表示我一定不偷第一个），取两者的最大值即可。

时间复杂度为 $O(n)$，省略了空间没有用dp数组，代码如下：

```c++
class Solution {
public:
    int robRange(vector<int>& nums, int start, int end) {
        int first = nums[start], second = max(nums[start], nums[start + 1]);
        for (int i = start + 2; i <= end; i++) {
            int temp = second;
            second = max(first + nums[i], second);
            first = temp;
        }
        return second;
    }

    int rob(vector<int>& nums) {
        int length = nums.size();
        if (length == 1) return nums[0];
        if (length == 2) return max(nums[0], nums[1]);
        return max(robRange(nums, 0, length - 2), robRange(nums, 1, length - 1));
    }
};
```

不过也可以设dp[i]表示以nums[i]为结尾的最大金额，**nums[i]一定要被偷**。

这样的话，dp[i+1]就等于nums[i+1]加上dp[0]到dp[i-1]的最大值，时间复杂度更高，代码如下：

```c++
class Solution {
public:
    int func(vector<int> &nums, int s, int e) {
        vector<int> dp(e - s + 1);
        dp[0] = nums[s];
        dp[1] = nums[s + 1];
        int res = max(dp[0], dp[1]);
        for(int i = 2; i < e - s + 1; i++) {
            int max = 0;           
            for(int j = 0; j < i - 1; j++) {
                if(dp[j] > max) max = dp[j];
            }
            dp[i] = nums[i + s] + max;//注意这里是nums[i+s]，[2,3,1]的时候下标后移了
            if(dp[i] > res) res = dp[i];
        }
        return res;
    }

    int rob(vector<int>& nums) {
        //dp[i]表示以nums[i]为结尾的最大金额，nums[i]必须被偷
        int size = nums.size();
        if(size == 1) return nums[0];
        if(size == 2) return max(nums[0], nums[1]);
        return max(func(nums, 0, size - 2), func(nums, 1, size - 1));
    }
};
```


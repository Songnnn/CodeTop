题源：https://leetcode-cn.com/problems/unique-paths-ii

回溯方法最简单，代码如下：

```c++
class Solution {
public:
    void dfs(vector<vector<int>>& obstacleGrid, int& res, int i, int j) {
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        if(i == m - 1 && j == n - 1 && obstacleGrid[i][j] == 0) {
            res++;
            return;
        }
        else if(i >= m || j >= n || obstacleGrid[i][j] == 1) return;
        dfs(obstacleGrid, res, i, j + 1); //向右走
        dfs(obstacleGrid, res, i + 1, j); //向下走
    }

    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int res = 0;
        dfs(obstacleGrid, res, 0, 0);
        return res;
    }
};
```

暴力虽好，可惜会超时，转战动态规划，dp[i]\[j]表示终点为(i, j)时的路线数，代码如下：

```c++
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        if(obstacleGrid[m - 1][n - 1] == 1) return 0;
        vector<vector<int>> dp(m, vector<int>(n));
        //初始化第一行
        for(int j = 0; j < n; j++) {
            if(obstacleGrid[0][j] == 1) break;
            dp[0][j] = 1;
        }
        //初始化第一列
        for(int i = 0; i < m; i++) {
            if(obstacleGrid[i][0] == 1) break;
            dp[i][0] = 1;
        }
        for(int i = 1; i < m; i++) {
            for(int j = 1; j < n; j++) {
                if(obstacleGrid[i - 1][j] == 0) dp[i][j] += dp[i - 1][j];
                if(obstacleGrid[i][j - 1] == 0) dp[i][j] += dp[i][j - 1];
            }
        }
        return dp[m - 1][n - 1];
    }
};
```

二维dp一般都可以转成一维dp，省点时间复杂度，代码如下：

```c++
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        if(obstacleGrid[m - 1][n - 1] == 1) return 0;
        vector<int> dp(n);
        vector<int> aux(m);
        for(int j = 0; j < n; j++) {
            if(obstacleGrid[0][j] == 1) break;
            dp[j] = 1;
        }
        for(int i = 0; i < m; i++) {
            if(obstacleGrid[i][0] == 1) break;
            aux[i] = 1;
        }
        for(int i = 1; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(j == 0) dp[j] = aux[i];
                else if(obstacleGrid[i][j] == 1) dp[j] = 0;
                else if(obstacleGrid[i][j - 1] == 0) dp[j] += dp[j - 1];
            }
        }
        return dp[n - 1];
    }
};
```

或许还能再省一点，能到$O(1)$也说不定
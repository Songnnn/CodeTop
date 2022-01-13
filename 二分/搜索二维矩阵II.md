题源：https://leetcode-cn.com/problems/search-a-2d-matrix-ii/

对于target，首先在第一行从左向右遍历找到第一个大于target的数，其下标为j；然后在第一列找到第一个大于target的数，其下标为i，则target就在左上角为(0, 0)，右下角为(i - 1, j - 1)的矩形中。

随后遍历小矩形中的每一行，对每一行进行二分查找，时间复杂度为$O(mlogn)$，并不算高效，代码如下：

```c++
class Solution {
public:
    bool helper(vector<vector<int>>& matrix, int k, int left, int right, int target) {
        if(left > right) return false;
        while(left <= right) {
            int mid = left + (right - left) / 2; 
            if(matrix[k][mid] == target) return true;
            else if(matrix[k][mid] > target) right = mid - 1;
            else if(matrix[k][mid] < target) left = mid + 1;
        }
        return false;
    }

    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size();
        int n = matrix[0].size();
        int i = 0, j = 0;
        while(j < n && matrix[0][j] <= target) j++; 
        while(i < m && matrix[i][0] <= target) i++;
        for(int k = 0; k < i; k++) {
            if(helper(matrix, k, 0, j - 1, target)) return true;
        }
        return false;
    }
};
```

官方题解使用了STL自带的库函数，速度要快一些：

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        for (const auto& row: matrix) {
            auto it = lower_bound(row.begin(), row.end(), target);
            if (it != row.end() && *it == target) return true;            
        }
        return false;
    }
};
```

太优雅了，性能还更高。

还可以使用Z型搜索，以矩阵的右上角为起点，对于当前位置(x, y)，我们希望在以原矩阵的左下角为左下角，当前位置为右上角的矩阵中进行搜索。

- 如果mat(x, y) == target，return true
- 如果mat(x, y) > target，由于每一列的元素都是升序排列的，那么在当前的搜索矩阵中，所有位于第y列的元素都是严格大于target的，因此我们可以将它们全部忽略，即将y减少1，向左搜索
- 如果mat(x, y) < target，由于每一行的元素都是升序排列的，那么在当前的搜索矩阵中，所有位于第x行的元素都是严格大于target的，因此我们可以将它们全部忽略，即将x增加1，向下搜索

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size(), n = matrix[0].size();
        int x = 0, y = n - 1;
        while (x < m && y >= 0) {
            if (matrix[x][y] == target) {
                return true;
            }
            if (matrix[x][y] > target) {
                --y;
            }
            else {
                ++x;
            }
        }
        return false;
    }
};
```

每次我们都会排除一行或一列，所以时间复杂度为$O(m+n)$，真正的高效算法。
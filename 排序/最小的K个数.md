题源：https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/

经典topK问题，最简单的方法当然是先排序，不过时间复杂度很差，是$O(nlogn)$，下面介绍其他方法。

省事一点的方法就是直接用优先队列，优先队列的底层是堆，我们维持一个大小为K的大顶堆即可，时间复杂度可以降到$O(nlogk)$，代码如下：

```c++
class Solution {
public:
    vector<int> getLeastNumbers(vector<int>& arr, int k) {
        vector<int> vec(k, 0);
        if (k == 0) { // 排除 0 的情况
            return vec;
        }
        priority_queue<int> Q;
        for (int i = 0; i < k; ++i) {
            Q.push(arr[i]);
        }
        for (int i = k; i < (int)arr.size(); ++i) {
            if (Q.top() > arr[i]) {
                Q.pop();
                Q.push(arr[i]);
            }
        }
        for (int i = 0; i < k; ++i) {
            vec[i] = Q.top();
            Q.pop();
        }
        return vec;
    }
};
```

这里我们可以使用快速排序。我们每次快排，都能将arr数组分成以partition为界的两部分，左边部分都是小于等于partition的，右边部分都是大于partition的。我们要取最小的K个数字（这K个数字不必有序），那就有以下几种情况：

- partition左边部分的数字刚好有K个，那直接返回这K个数字即可。
- partition左边部分的数字大于K个，那我们需要继续对左半边进行快排。
- partition左边部分的数字小于K个，那我们需要继续对右半边进行快排。

最坏情况下的时间复杂度是$O(n^2)$，但是平均复杂度是$O(n)$，代码如下：

```c++
class Solution {
public:
    void swap(int& a, int& b) {
        int c = a;
        a = b;
        b = c;
    }

    int qsort(vector<int>& arr, int i, int j) {
        //if(i == j) return i;
        swap(arr[i], arr[(i + j) / 2]);
        int partition = arr[i];
        int left = i, right = j;
        while(left < right) {
            while(left < right && arr[right] >= partition) right--;
            while(left < right && arr[left] <= partition) left++;
            swap(arr[left], arr[right]);
        }
        swap(arr[i], arr[left]);
        return left;
    }

    vector<int> getLeastNumbers(vector<int>& arr, int k) {
        int size = arr.size();
        int left = 0, right = size - 1;
        vector<int> res;
        while(k > 0) {
            int nums = qsort(arr, left, right) - left + 1; //从arr[left]到arr[partition]的数字个数
            if(k == nums) {
                for(int i = 0; i < k; i++) res.emplace_back(arr[i + left]);
                k = k - nums - 1;
            }
            else if(nums < k){
                for(int i = 0; i < nums; i++) res.emplace_back(arr[i + left]);
                left = nums + left;
                k = k - nums;
            }
            else if(nums > k) {
                right = nums + left - 1 - 1;
            }            
        }
        return res;
    }
};
```


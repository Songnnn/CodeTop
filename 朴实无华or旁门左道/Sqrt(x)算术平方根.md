题源：https://leetcode-cn.com/problems/sqrtx/

暴力：

```c++
class Solution {
public:
    int mySqrt(int x) {
        if(x == 1 || x == 0) return x;
        unsigned int i = 1;
        for(; i <= x / 2 + 1; i++) {
            if(i * i == x) break;
            else if(i * i > x) return i - 1;
        }
        return i;
    }
};
```

二分查找：

```c++
class Solution {
public:
    int mySqrt(int x) {
        int l = 0, r = x, ans = -1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if ((long long)mid * mid <= x) {
                ans = mid;
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return ans;
    }
};
```

还可以用牛顿迭代法，求C的平方根其实就是求$y=x^2-C$的零点，我们任取一个x0，在(x0, y)上作切线，随后得到一个更接近零点的x1，继续这个操作，就可以无限逼近零点，图示如下：

![image-20220125105602702](https://github.com/Songnnn/CodeTop/blob/main/images/x%E7%9A%84%E5%B9%B3%E6%96%B9%E6%A0%B9.png)


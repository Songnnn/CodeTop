##### 双栈

没什么好说的，一个运算符栈，一个运算数栈，最关键的是运算符的优先级判断

```c++
class Solution {
public:
    bool cmp(char& x, char& y) {
        //比较运算符优先级，如果x优先级大于y则返回true
        if(x == '*' || x == '/') return true;
        else if (x == '-' && (y == '+' || y == '-')) return true; //减法没有结合律，有减法就先做吧
        return false;
    }

    int cal(stack<int>& nums, stack<char>& op) {
        int y = nums.top();
        nums.pop();
        int x = nums.top();
        nums.pop();
        char oper = op.top();
        op.pop();
        if (oper == '+') return x + y;
        else if (oper == '-') return x - y;
        else if (oper == '*') return x * y;
        else return x / y;
    }

    int calculate(string s) {
        //符号栈和数值栈
        int size = s.size();
        stack<int> nums;
        stack<char> op;
        for (int i = 0; i < size;) {
            if (s[i] == ' ') {
                i++;
                continue;
            }
            else if (s[i] == '+' || s[i] == '-' || s[i] == '*' || s[i] == '/') {
                if (op.empty()) op.push(s[i]);
                else {
                    while (!op.empty() && cmp(op.top(), s[i])) nums.push(cal(nums, op));
                    op.push(s[i]);
                }
                i++;
            }
            else {
                string num;
                //这里运算符需要一直出栈，直到遇到优先级更大的运算符
                while (s[i] >= '0' && s[i] <= '9') num += s[i++]; 
                nums.push(stoi(num));
            }
        }

        while (!op.empty()) nums.push(cal(nums, op));
        return nums.top();
    }
};
```


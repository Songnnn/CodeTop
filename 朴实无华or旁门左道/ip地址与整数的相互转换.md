将ip地址"10.0.3.193"转成10进制整数res：

思路肯定是先转成二进制，再转成十进制res。首先将string按“.”分开，再stoi转成整数，得到10，0，3，193。10是ip地址的第一段，即0到8位，由于ip地址总共有32位，所以让10的二进制左移24位即可。0左移16位，3左移8位，194左移0位，将得到的四位左移之后的数一起做**或运算**，即可得到最终的结果。图示如下：

![image-20211227162000201](https://github.com/Songnnn/CodeTop/blob/main/images/IP地址与整数的相互转换.png)

代码如下：

```c++
//将字符串按照"."隔开
vector<string> sub(string s) {
    int start = 0;
    vector<string> res;
    for (int i = 0; i < s.size(); i++) {
        if (s[i] == '.') {
            res.emplace_back(s.substr(start, i - start));
            start = i + 1;
        }
        else if(i == s.size() - 1) res.emplace_back(s.substr(start, i - start + 1));
    }
    return res;
}

int main() {
    string ip = "10.0.3.193";
    //ips[0] = "10", ips[1] = "0", ips[2] = "3", ips[3] = "193"
    vector<string> ips = sub(ip);
    unsigned int ip1 = stoi(ips[0]) << 24;
    unsigned int ip2 = stoi(ips[1]) << 16;
    unsigned int ip3 = stoi(ips[2]) << 8;
    unsigned int ip4 = stoi(ips[3]);
    unsigned res = ip1 | ip2 | ip3 | ip4;
    cout << res << endl;

	return 1;
}
```

注意这里要用无符号整数，否则高位为1的时候就变负数了，或者直接用long。

如果是整数转成ip地址，那就先将整数与255做**与运算**，得到24-32位，再将整数左移8位，再与255做与运算，得到整数的16-24位，最后拼起来就是ip地址了。

代码如下：

```c++
int main() {
    unsigned int ip = 167773121;
    string ips;
    for (int i = 0; i < 4; i++) {
        int tmp = ip & 255;
        ip = ip >> 8;
        ips = to_string(tmp) + "." + ips;
    }
    ips = ips.substr(0, ips.size() - 1); //去掉最后的.
    cout << ips << endl;
	return 1;
}
```


# D - Sum of Divisors

[Link](https://atcoder.jp/contests/abc172/tasks/abc172_d)

## Solution

做的时候偷懒用了一手[OEIS](https://oeis.org/A143127)，题解则用了一手巧妙的转换，具体如下：

如果按题意暴力模拟的话代码是这样的：

    ans = 0
    for i = 1 to N:
        for j = 1 to N: //其实这里可以剪枝成1-i，不过为了方便下面做更大的优化先不这么搞
            if i % j == 0 then ans += i
    return ans

显然O(N^2)，会t，于是我们将内外循环调个个儿，就变成了这样：

    ans = 0
    for j = 1 to N:
        for i = 1 to N:
            if i % j == 0 then ans += i
    return ans

然后我们可以~~惊奇地~~发现，此时题意变成了求j从1到N，g(j)的和，而g(j)是不超过N的j的**所有倍数的和**

那么一个数X不超过N的倍数有多少个呢？这是可以O(1)算出来的，有N/X个。

所以g(X) = 1\*X+2\*X+3\*X+...+(N/X)\*X = ((1+N/X)\*(N/X)/2)\*X

答案最终就变成了：

    ans = 0
    for i = 1 to N:
        ans += ((1+N/i)*(N/i)/2)*i
    return ans

成功干到了O(N)，收工。

## Code

    #include <algorithm>
    #include <cmath>
    #include <iostream>
    #include <string>
    using namespace std;
        
    int main()
    {
        long long n, ans = 0;
        cin >> n;
        for (int i = 1; i <= n; i++) ans += i * (n / i) * (n / i + 1) / 2;
        cout << ans << endl;
        return 0;
    }

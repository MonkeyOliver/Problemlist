# C - Tsundoku

[Link](https://atcoder.jp/contests/abc172/tasks/abc172_c)

## Solution

好久没写题竟然被这种水题卡了很久，一开始想了个假贪心，后来看了题解的前半部分又写了个假二分，我太挫了。

两种做法，前缀和+双指针（O(N+M)）或前缀和+二分（O(NlogM)），前缀和处理后可以把题目转换成求满足a[i] + b[j] < K的最大i + j，接下来就怎么搞都行了。~~（假二分就是这么来的）~~

需要注意数据范围。

## Code

    #include <algorithm>
    #include <iostream>
    #include <string>
    using namespace std;

    const int maxn = 2e5 + 10;
    long long a[maxn], b[maxn];

    int main()
    {
        int n, m, ans = 0;
        long long k;
        cin >> n >> m >> k;
        for (int i = 1; i <= n; i++) cin >> a[i];
        for (int i = 1; i <= m; i++) cin >> b[i];
        for (int i = 1; i <= n; i++) a[i] += a[i - 1];
        for (int i = 1; i <= m; i++) b[i] += b[i - 1];
        // Solution1
        for (int i = 0; i <= n; i++) {
            if (k < a[i]) break;
            int j = upper_bound(b, b + m + 1, k - a[i]) - b - 1;
            ans = max(ans, i + j);
        }
        // Solution2
        int j = m;
        for (int i = 0; i <= n; i++) {
            if (k < a[i]) break;
            while(b[j] > k - a[i]) j--;
            ans = max(ans, i + j);
        }
        cout << ans << endl;
        return 0;
    }

# 91. Decode Ways

[Link](https://leetcode.com/problems/decode-ways/)

## 题解

dp

若当前数字s[i]不为0且s[i-1]与s[i]所组成的两位数合法，则dp[i] = dp[i-1] + dp[i-2]，

若当前数字s[i]不为0且s[i-1]与s[i]所组成的两位数不合法，则dp[i] = dp[i-2]，

若当前数字s[i]为0则dp[i] = 0。

初始化见代码

## 代码

    class Solution {
    public:
        int numDecodings(string s) {
            int n = s.size();
            int dp[100000];
            memset(dp, 0, sizeof(dp));
            dp[0] = (s[0] != '0');
            dp[1] = (s[0] != '0' && s[1] != '0');
            for(int i = 2; i <= n; i++){
                if(s[i] != '0'){
                    dp[i] = dp[i - 1];
                    int tmp = (s[i - 2] - '0') * 10 + (s[i - 1] - '0');
                    if(tmp <= 26 && s[i - 1] != '0')dp[i] += dp[i - 2];
                    else if(tmp <= 26 && s[i - 1] =='0')dp[i] = dp[i - 2];
                }
                else dp[i] = 0;
            }
            return dp[n];
        }
    };

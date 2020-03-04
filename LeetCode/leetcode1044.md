# 1044. Longest Duplicate Substring

[Link](https://leetcode.com/problems/longest-duplicate-substring/)

## Solution

裸的后缀数组板子题，关于后缀数组详见[后缀数组](https://oi-wiki.org/string/sa/)（当然了切leetcode还是不要直接抄模板比较好）

## Code

    class Solution {
    public:
        string s;
        int sa[100010], rk[100010];
        int rk2[100010];//第二关键字
        int tong[100010];//基数排序辅助数组
        int height[100010];

        void SA(int len) {
            int i, M = 127, N = len, cnt = 1;
            for (i = 1; i <= N; i++) rk[i] = s[i], rk2[i] = i;
            for (i = 0; i <= M; i++) tong[i] = 0;
            for (i = 1; i <= N; i++) tong[s[i]]++;
            for (i = 1; i <= M; i++) tong[i] += tong[i - 1];
            for (i = N; i >= 1; i--) sa[tong[s[i]]--] = i;
            for (int k = 1; cnt < N; M = cnt, k <<= 1) {
                cnt = 0;
                for (i = 1; i <= k; i++) rk2[++cnt] = N - k + i;
                for (i = 1; i <= N; i++)if (sa[i] > k) rk2[++cnt] = sa[i] - k;
                for (i = 0; i <= M; i++) tong[i] = 0;
                for (i = 1; i <= N; i++) tong[rk[i]]++;
                for (i = 1; i <= M; i++) tong[i] += tong[i - 1];
                for (i = N; i >= 1; i--) sa[tong[rk[rk2[i]]]--] = rk2[i];
                memcpy(rk2, rk, sizeof(rk));
                for (i = 1, cnt = 0; i <= N; ++i)
                    rk[sa[i]] = (rk2[sa[i]] == rk2[sa[i - 1]] && rk2[sa[i] + k] == rk2[sa[i - 1] + k]) ? cnt : ++cnt;
            }
        }

        void GetHeight(int n) {
            for (int i = 1, k = 0; i <= n; ++i) {
                if (k) --k;
                while (s[i + k] == s[sa[rk[i] - 1] + k]) ++k;
                height[rk[i]] = k;
            }
        }
        
        string longestDupSubstring(string S) {
            s = S;
            int len = s.size();
            s = "#" + s;
            SA(len);
            GetHeight(len);
            int maxv = -1, max_pos = 0;
            for (int i = 1; i <= len; i++)
                if (height[i] >= maxv)max_pos = i, maxv = height[i];
            if (!maxv)return "";
            string ans(s.begin() + sa[max_pos], s.begin() + sa[max_pos] + maxv);
            return ans;
        }
    };

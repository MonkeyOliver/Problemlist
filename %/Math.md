<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>

# Math

## 快速幂

    LL qPow(LL a, LL b, LL mod) {
        LL ans = 1, bas = a;
        while (b) {
            if (b & 1)ans = ans * bas%mod;
            bas = bas * bas%mod;
            b >>= 1;
        }
        return ans;
    }

## 快速乘法

    LL qMul(LL a, LL b, LL mod) {
        LL ans = 0, bas = a;
        while (b) {
            if (b & 1)ans = (ans + bas) % mod;
            bas = (bas + bas) % mod;
            b >>= 1;
        }
        return ans;
    }

## 模意义下x的乘法逆元（快速幂法）

    LL inv(LL x, LL mod) {
        return qPow(x, mod - 2, mod);
    }

## 模意义下的组合数

    LL C(LL n, LL m, LL mod) {
        if (n < m)return 0;
        if (m > n - m)m = n - m;
        LL tmp1 = 1, tmp2 = 1;
        for (int i = 0; i < m; i++)tmp1 = (tmp1*(n - i)) % mod, tmp2 = (tmp2*(i + 1)) % mod;
        return tmp1 * qPow(tmp2, mod - 2, mod) % mod;
    }

## Lucas定理

    //用于求解大组合数，模数mod必须为质数
    LL Lucas(LL n, LL m, LL mod) {
        if (!m) return 1;
        return (C(n % mod, m % mod, mod) * Lucas(n / mod, m / mod, mod)) % mod;
    }

## 欧几里得GCD

    LL gcd(LL a, LL b) { return b ? gcd(b, a % b) : a; }

## 扩展欧几里得

    //返回gcd(a,b), x和y为ax+by=gcd(a,b)的一组特解
    LL exgcd(LL a, LL b, LL&x, LL&y) {
        if (!b) { x = 1; y = 0; return a; }
        LL d = exgcd(b, a%b, x, y);
        LL z = x; x = y; y = z - y * (a / b);
        return d;
    }

## 欧拉法线性筛**素数**

    int prime[maxn + 10], not_pr[maxn + 10];

    void get_list() {
        int tot = 0;
        for (int i = 2; i <= maxn; i++) {
            if (!not_pr[i]) prime[++tot] = i;
            for (int j = 1; j <= tot && i*prime[j] <= maxn; j++) {
                not_pr[i*prime[j]] = 1;//合数标为1，prime[j]是合数i*prime[j]的最小素因子
                if (i%prime[j] == 0) break;//比一个合数大的质数和该合数的乘积可用一个更大的合数和比其小的质数相乘得到
            }
        }
    }

## 费马小定理

**若p为质数**，$a^p\equiv a(\bmod\ p)$

## 欧拉定理

**若a，n互质**，$a^{\phi(n)}\equiv 1(\bmod\ n)$

**若a，n互质**，对于任意正整数b，有$a^b\equiv a^{b\bmod\phi(n)}(\bmod\ n)$

**若a，n不一定互质且$b>\phi(n)$**，有$a^b\equiv a^{b\bmod\phi(n)+\phi(n)}(\bmod\ n)$

## 欧拉函数$\phi(n)$（$O\sqrt{n}$）

    LL phi(LL n) {
        LL ans = n, m = sqrt(n);
        for (LL i = 2; i <= m; i++) {
            if (n%i == 0) {
                ans = ans / i * (i - 1);
                while (n%i == 0) n /= i;
            }
        }
        if (n > 1) ans = ans / n * (n - 1);
        return ans;
    }

## 博弈

### Nim

初始状态异或非0：非平衡开局：先手必胜，后手必败

初始状态异或为0：平衡开局：先手必败，后手必胜

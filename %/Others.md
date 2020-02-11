# xjb模板

## 二分诀窍

- 求下界，即求满足x>=val或x>val的最**小**x，左闭右开返回下界
- 求上界，即求满足x<=val或x<val的最**大**x，左闭右开返回下界-1

代码：

    LL l = 0, r = n + 1;
    while (l < r) {
        int m = (l + r) / 2;
        if (judge(m))l = m + 1;//上下界judge()略有不同
        else r = m;
    }
    return l;//下界
    return l - 1;//上界

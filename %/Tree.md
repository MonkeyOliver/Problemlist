# Tree

## 线段树（区间修改查询区间和）

    typedef long long LL;
    const int maxn = 200010 * 4;

    int n, q;
    int src[maxn];

    class segTree {
    public:
        void build(int o, int s, int t) {
            laz[o] = 0;
            if (s == t) { sum[o] = src[s]; return; }
            int mid = (s + t) / 2;
            build(lson(o), s, mid);
            build(rson(o), mid + 1, t);
            push_up(o);
        }

        void update(int l, int r, int k) {
            update(1, 1, n, l, r, k);
        }

        LL query(int l, int r) {
            return query(1, 1, n, l, r);
        }

    private:
        LL laz[maxn], sum[maxn];
        int lson(int x) { return x << 1; }
        int rson(int x) { return x << 1 | 1; }
        void push_up(int x) { sum[x] = sum[lson(x)] + sum[rson(x)]; }
        void push_down(int x, int l, int r) {
            int mid = (l + r) / 2;
            laz[lson(x)] += laz[x];
            laz[rson(x)] += laz[x];
            sum[lson(x)] += laz[x] * (mid - l + 1);
            sum[rson(x)] += laz[x] * (r - mid);
            laz[x] = 0;
        }
        void update(int o, int s, int t, int l, int r, int k) {
            if (l > t || r < s)return;
            else if (l <= s && t <= r) { sum[o] += k * (t - s + 1); laz[o] += k; return; }
            push_down(o, s, t);
            int mid = (s + t) / 2;
            update(lson(o), s, mid, l, r, k);
            update(rson(o), mid + 1, t, l, r, k);
            push_up(o);
        }
        LL query(int o, int s, int t, int ql, int qr) {
            if (ql > t || qr < s)return 0;
            else if (ql <= s && t <= qr)return sum[o];
            push_down(o, s, t);
            int mid = (s + t) / 2;
            return query(lson(o), s, mid, ql, qr) + query(rson(o), mid + 1, t, ql, qr);
        }
    };

    int main() {
        LL o, l, r, k;
        cin >> n >> q;
        for (int i = 1; i <= n; i++)cin >> src[i];
        segTree ans;
        ans.build(1, 1, n);
        while (q--) {
            cin >> o;
            if (o == 1) {
                cin >> l >> r >> k;
                ans.update(l, r, k);
            }
            else {
                cin >> l >> r;
                cout << ans.query(l, r) << endl;
            }
        }
        return 0;
    }

## 静态主席树（查询区间第k大）

    typedef long long LL;
    const int maxn = (2e5 + 10) * 20;

    class Ctree {
    public:
        int build(int l, int r) {
            int rt = ++tot;
            cnt[rt] = 0;
            if (l < r) {
                int mid = (l + r) / 2;
                lson[rt] = build(l, mid);
                rson[rt] = build(mid + 1, r);
            }
            return rt;
        }

        int insert(int pre, int l, int r, int x) {
            int rt = ++tot;
            lson[rt] = lson[pre], rson[rt] = rson[pre];
            cnt[rt] = cnt[pre] + 1;
            if (l < r) {
                int mid = (l + r) / 2;
                if (x <= mid)lson[rt] = update(lson[pre], l, mid, x);
                else rson[rt] = update(rson[pre], mid + 1, r, x);
            }
            return rt;
        }

        int query(int u, int v, int l, int r, int k) {
            if (l >= r)return l;
            int x = cnt[lson[v]] - cnt[lson[u]], mid = (l + r) / 2;
            if (x >= k)return query(lson[u], lson[v], l, mid, k);
            else return query(rson[u], rson[v], mid + 1, r, k - x);
        }

    private:
        int tot = 0;
        int cnt[maxn];//子树个数
        int lson[maxn], rson[maxn];
    };

    int trees[maxn], src[maxn];
    vector<int>a;

    int main() {
        int n, q, l, r, k;
        cin >> n >> q;
        Ctree ans;
        a.push_back(-1e9 + 10);//下标从1开始
        for (int i = 1; i <= n; i++) { cin >> src[i]; a.push_back(src[i]); }
        sort(a.begin(), a.end());
        a.erase(unique(a.begin(), a.end()), a.end());//离散化
        int m = a.size();
        trees[0] = ans.build(1, m);//原始空树
        for (int i = 1; i <= n; i++)trees[i] = ans.insert(trees[i - 1], 1, m, lower_bound(a.begin(), a.end(), src[i]) - a.begin());
        while (q--) {
            cin >> l >> r >> k;
            cout << a[ans.query(trees[l - 1], trees[r], 1, m, k)] << endl;
        }
        return 0;
    }

## LCA（倍增法）

    int n, m, s;
    vector<int>G[maxn];
    int fa[maxn][31], dep[maxn], lg2[maxn];

    void dfs(int rt, int f) {
        fa[rt][0] = f;
        dep[rt] = dep[f] + 1;
        for (int i = 1; i < 31; i++) {
            fa[rt][i] = fa[fa[rt][i - 1]][i - 1];//核心之一:f的2^i祖先等于f的2^(i-1)祖先的2^(i-1)祖先，2^i=2^(i-1)+2^(i-1)
        }
        int sz = G[rt].size();
        for (int i = 0; i < sz; i++) {
            if (G[rt][i] != f)dfs(G[rt][i], rt);
        }
    }

    void init() {
        for (int i = 1; i <= n; i++)lg2[i] = lg2[i - 1] + (1 << lg2[i - 1] == i);
    }

    int lca(int x, int y) {
        if (dep[x] < dep[y])swap(x, y);
        while (dep[x] > dep[y])x = fa[x][lg2[dep[x] - dep[y]] - 1];
        if (x == y)return x;
        for (int i = lg2[dep[x]] - 1; i >= 0; i--)
            if (fa[x][i] != fa[y][i])x = fa[x][i], y = fa[y][i];
        return fa[x][0];
    }

    int main() {
        int x, y;
        cin >> n >> m >> s;
        init();
        for (int i = 0; i < n - 1; i++) {
            cin >> x >> y;
            G[x].push_back(y);
            G[y].push_back(x);
        }
        dfs(s, 0);
        while (m--) {
            cin >> x >> y;
            cout << lca(x, y) << endl;
        }
        return 0;
    }

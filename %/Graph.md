# Graph

## 前向星

    int head[maxn], cnt = 0;

    struct edge {
        int u, v, nxt;
    }edges[maxn];

    void add_edge(int u, int v) {
        edges[++cnt].u = u;
        edges[cnt].v = v;
        edges[cnt].nxt = head[u];
        head[u] = cnt;
    }

## 并查集

    int fa[maxn];

    void init(int n) {
        for (int i = 1; i <= n; i++)fa[i] = i;
    }

    int findr(int x) {
        if (fa[x] == x)return x;
        else return fa[x] = findr(fa[x]);
    }

    void merge(int x, int y) {
        int xr = findr(x);
        int yr = findr(y);
        if (xr != yr)fa[xr] = yr;
    }

## 匈牙利（二分图最大匹配）

    const int maxn = 110;

    int n, G[maxn][maxn], vis[maxn], match[maxn];

    bool dfs(int u) {
        for (int i = 1; i <= n; i++) {
            if (G[u][i] && !vis[i]) {
                vis[i] = 1;
                if (!match[i] || dfs(match[i])) {
                    match[i] = u;
                    return true;
                }
            }
        }
        return false;
    }

## 分层最短路

    #define make_pair mpr
    const int maxn = 200000 + 10;
    using namespace std;

    int n, m, k, s, t;
    vector<pair<int, int>>G[maxn];
    int vis[maxn], dis[maxn];

    void addEdge(int u, int v, int c = 0) {
        G[u].push_back(mpr(v, c));
    }

    void dijk(int s) {
        memset(dis, INT_MAX, sizeof(dis));
        dis[s] = 0;
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>>q;
        q.push(mpr(0, s));
        while (!q.empty()) {
            int u = q.top().second; q.pop();
            if (!vis[u]) {
                vis[u] = 1;
                for (vector<pair<int,int>>::iterator i = G[u].begin(); i != G[u].end(); i++) {
                    int v = (*i).first, c = (*i).second;
                    if (dis[v] > dis[u] + c) {
                        dis[v] = dis[u] + c;
                        q.push(mpr(dis[v], v));
                    }
                }

            }
        }
    }

    int main() {
        scanf("%d%d%d%d%d", &n, &m, &k, &s, &t);
        int u, v, c;
        for (int i = 0; i < m; i++) {
            scanf("%d%d%d", &u, &v, &c);
            addEdge(u, v, c);
            addEdge(v, u, c);
            for (int j = 1; j <= k; j++) {
                addEdge(u + (j - 1)*n, v + j * n);
                addEdge(v + (j - 1)*n, u + j * n);
                addEdge(u + j * n, v + j * n, c);
                addEdge(v + j * n, u + j * n, c);
            }
        }
        for (int i = 1; i <= k; i++)addEdge(t + (i - 1)*n, t + i * n);
        dijk(s);
        printf("%d\n", dis[t + k * n]);
        return 0;
    }

## 最大流dinic

    vector<int> G[maxn];
    map<pair<int, int>, int>Edges;
    int n, m;
    int dep[maxn];

    int bfs(int s, int t) {
        memset(dep, 0, sizeof(dep));
        queue<int>q;
        q.push(s);
        dep[s] = 1;
        while (!q.empty()) {
            int cur = q.front(); q.pop();
            for (auto i : G[cur]) {
                pair<int, int>e = make_pair(cur, i);
                if (Edges[e] && !dep[i]) {
                    dep[i] = dep[cur] + 1, q.push(i);
                    if (i == t)return dep[t];
                }
            }
        }
        return dep[t];
    }

    int dfs(int u, int t, int flow) {
        if (u == t)return flow;
        int rest = flow, f;
        for (auto v : G[u]) {
            pair<int, int>e = make_pair(u, v);
            pair<int, int>antie = make_pair(v, u);
            if (Edges[e] && dep[v] == dep[u] + 1) {
                f = dfs(v, t, min(Edges[e], rest));
                if (!f)dep[v] = 0;
                Edges[e] -= f;
                Edges[antie] += f;
                rest -= f;
            }
        }
        return flow - rest;
    }

    int dinic(int s, int t) {
        int maxflow = 0;
        while (bfs(s, t))maxflow += dfs(s, t, INT_MAX);
        return maxflow;
    }

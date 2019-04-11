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

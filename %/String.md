# String

## KMP

    int nxt[maxn];
    vector<int>ans;

    void GetNextVal(string&pat) {
        int plen = pat.size(), i = 0, j = -1;
        nxt[0] = -1;
        while (i < plen) {
            while (j != -1 && pat[i] != pat[j])j = nxt[j];
            nxt[++i] = ++j;
        }
    }

    void KMP(string&pat, string&src) {
        int i = 0, j = 0, slen = src.size(), plen = pat.size();
        GetNextVal(pat);
        while (i < slen) {
            while (j != -1 && src[i] != pat[j])j = nxt[j];
            i++, j++;
            if (j >= plen) {
                //根据题意进行魔改
                ans.push_back(i - j + 1);
                //可重叠
                j = nxt[j];
                //不可重叠
                //j = 0;
            }
        }
    }

## KMP求最小循环节

    int n = strlen(str);
    int len = n - nxt[n];
    if (len != n && n % len == 0)puts("0");//是一个完整的有循环节的字符串
    else printf("%d\n", len - (n - n / len * len));//需要补充几个字母后才能成为有循环节的字符串

## 后缀数组+Height数组[1-n]

    const int maxn = 1e6 + 10;

    int sa[maxn], sa2[maxn], rnk[maxn], tong[maxn], a[maxn];//SA用
    int rak[maxn], height[maxn];//GetHeight用

    void SA(int len) {
        int i, M = 127, N = len, cnt = 1;//M为字符集大小，根据题意进行修改
        for (i = 1; i <= N; i++) rnk[i] = a[i], sa2[i] = i;
        //按首字母进行第一次基数排序
        for (i = 0; i <= M; i++) tong[i] = 0;
        for (i = 1; i <= N; i++) tong[a[i]]++;
        for (i = 1; i <= M; i++) tong[i] += tong[i - 1];
        for (i = N; i >= 1; i--) sa[tong[a[i]]--] = i;
        //k为倍增长度
        for (int k = 1; cnt < N; M = cnt, k <<= 1) {
            cnt = 0;
            for (i = 1; i <= k; i++) sa2[++cnt] = N - k + i;
            for (i = 1; i <= N; i++)
                if (sa[i] > k) sa2[++cnt] = sa[i] - k;
            //用第二关键字对第一关键字进行基数排序
            for (i = 0; i <= M; i++) tong[i] = 0;
            for (i = 1; i <= N; i++) tong[rnk[i]]++;
            for (i = 1; i <= M; i++) tong[i] += tong[i - 1];
            for (i = N; i >= 1; i--) sa[tong[rnk[sa2[i]]]--] = sa2[i];
            swap(rnk, sa2);
            rnk[sa[1]] = cnt = 1;
            for (i = 2; i <= N; i++)
                rnk[sa[i]] = (sa2[sa[i - 1]] == sa2[sa[i]] && sa2[sa[i - 1] + k] == sa2[sa[i] + k]) ? cnt : ++cnt;
        }
    }

    void GetHeight(int len) {
        int i, j, k = 0, N = len;
        for (i = 1; i <= N; i++) rak[sa[i]] = i;
        for (i = 1; i <= N; height[rak[i++]] = k)
            for (k ? k-- : 0, j = sa[rak[i] - 1]; a[i + k] == a[j + k]; k++);
        return;
    }

    int main() {
        int t;
        cin >> t;
        string s;
        while (t--) {
            cin >> s;
            int len = s.size();
            for (int i = 0; i < len; i++) a[i + 1] = s[i] - '0';
            SA(len);
            GetHeight(len);
            //height[i]=sfx(sa[i-1])和sfx(sa[i])的最长公共前缀，也就是排名相邻两个后缀的最长公共前缀。
            //询问某两个后缀的最长公共前缀可以转化为求height数组区间最小值（可以用RMQ）
            //ans为本质不同的子串个数法一
            LL ans = (len + 1)*len / 2;
            for (int i = 1; i <= len; i++)ans -= height[i];
            //本质不同的子串个数法二
            LL ans = 0;
            for (int i = 1; i <= len; i++)ans += (len - sa[i] + 1 - height[i]);
            //可重叠最长重复子串
            max(height);
            cout << ans << endl;
        }
        return 0;
    }

## 后缀数组求不可重叠最长重复子串

    //利用height数组分组
    bool judge(int k, int len) {
        int Max = sa[1], Min = sa[1];
        for (int i = 2; i <= len; i++) {
            if (height[i] >= k)Max = max(Max, sa[i]), Min = min(Min, sa[i]);
            else Max = sa[i], Min = sa[i];
            if (Max - Min >= k)return true;
        }
        return false;
    }

    //main函数（假设sa数组和height数组已求出）
    //二分长度（即二分答案）
    int L = 0, R = n, ans = 0;
    while (L <= R) {
        int mid = (L + R) / 2;
        if (judge(mid, n))L = mid + 1, ans = max(ans, mid);
        else R = mid - 1;
    }
    if (ans < 4)puts("0");
    else printf("%d\n", ans + 1);

## 后缀数组求可重叠出现k次的最长重复子串

    bool judge(int mid, int len, int k) {
        int cnt = 1;
        for (int i = 1; i <= len; i++) {
            if (height[i] >= mid)cnt++;
            else cnt = 1;
            if (cnt >= k)return true;
        }
        return false;
    }
    //同上二分长度

## 后缀自动机

    const int maxn = 3 * 1e6 + 10;

    char s[maxn];
    int siz[maxn];
    LL ans = 0;

    struct SuffixAutoMaton {
        int last, cnt;
        int trans[maxn << 1][26], fa[maxn << 1], len[maxn << 1];
        SuffixAutoMaton() { last = cnt = 1; }//以1为根，cnt记录节点个数
        void insert(int c) {
            int p = last, np = ++cnt;//p为以c结尾的前缀的倒数第二个节点，np为倒数第一个（新建）
            last = np;//更新last
            len[np] = len[p] + 1;
            siz[np] = 1;//siz[i]表示i节点所代表的endpos的集合元素大小，
            //即所对应的字符串集的longest出现的次数（之后用的时候可能需要一个累加）
            while (p && !trans[p][c]) { trans[p][c] = np; p = fa[p]; }//沿着parent树跳！（边跳边更新）
            //case1：如果跳到最前面的根(S)，把np的parent树上的father置为1（即根）
            if (!p)fa[np] = 1;
            else {
                int q = trans[p][c];//q表示从np一直跳parent树所得到的 第一个 有c儿子的节点 的c儿子
                //case2：如果节点q表示的endpos所对应的字符串集合只有一个字符串，那么把np的parent树上的父亲设置为q
                if (len[p] + 1 == len[q])fa[np] = q;
                else {
                    //case3：复制q点
                    //同时len要更新（注意len[q] != len[p] + 1, 因为加点会使x父亲改变）
                    int nq = ++cnt;//新建复制点nq
                    fa[nq] = fa[q];//复制q点的父亲
                    memcpy(trans[nq], trans[q], sizeof(trans[q]));//复制q点的儿子
                    len[nq] = len[p] + 1;//更新len
                    fa[q] = fa[np] = nq;//把q点和np点的father指向复制点nq
                    while (p&&trans[p][c] == q) { trans[p][c] = nq; p = fa[p]; }//将前面所有连q的点连向nq
                }
            }
        }
    } sam;

    int main() {
        scanf("%s", s + 1);
        int len = strlen(s + 1);
        for (int i = 1; i <= len; i++) sam.insert(s[i] - 'a');
        return 0;
    }

## 后缀自动机求本质不同的子串数目

    LL ans = 0;
    for (int i = 1; i <= sam.cnt; i++)
        ans += sam.len[i] - sam.len[sam.fa[i]];
    cout << ans << endl;

## 回文树

    #include<bits/stdc++.h>
    using namespace std;

    const int N=2e5+5;
    typedef long long ll;

    char s[N];
    int S[N],len[N],fail[N],ch[N][26],tot,n;
    ll cnt[N];

    int new_node(int x)
    {
        len[tot]=x,cnt[tot]=0;
        memset(ch[tot],0,sizeof(ch[tot]));
        return tot++;
    }

    int get_fail(int x)
    {
        while(S[n-len[x]-1]!=S[n]) x=fail[x];
        return x;
    }

    void add(char *s)
    {
        tot=0,new_node(0),new_node(-1);
        fail[0]=1,fail[1]=1,S[0]=-1,n=0;
        int last=0;
        for(int i=0;s[i];i++)
        {
            int c=s[i]-'a';
            S[++n]=c;
            int cur=get_fail(last);
            if(!ch[cur][c])
            {
                int now=new_node(len[cur]+2);
                fail[now]=ch[get_fail(fail[cur])][c];
                ch[cur][c]=now;
            }
            last=ch[cur][c];
            cnt[last]++;
        }
    }

    ll query(char *s)
    {
        S[0]=-1,n=0;
        int last=0;ll res=0;
        for(int i=0;s[i];i++)
        {
            int c=s[i]-'a';
            S[++n]=c;
            int cur=last;
            while(cur!=1&&(!ch[cur][c]||S[n-len[cur]-1]!=S[n]))
                cur=fail[cur];
            last=ch[cur][c];
            res+=cnt[last];
        }
        return res;
    }

    void solve(int cas)
    {
        scanf("%s",s);
        add(s);
        for(int i=tot-1;i>=0;i--) cnt[fail[i]]+=cnt[i];
        cnt[0]=cnt[1]=0;
        for(int i=2;i<=tot-1;i++) cnt[i]+=cnt[fail[i]];
        scanf("%s",s);
        ll ans=query(s);
        printf("Case #%d: %lld\n",cas,ans);
    }

    int main()
    {
        //freopen("G.in","r",stdin);
        int T,cas=0;
        scanf("%d",&T);
        while(T--)
            solve(++cas);
        return 0;
    }

## s中选两个非空子串a,b（可重叠），使它们拼起来是一个回文串，问选择方案数

    const int maxn = 2e5 + 10;
    int n, pos, d[maxn * 2];
    char s[maxn * 2], S[maxn * 2];
    LL ans, sum;

    template <class T>
    void print(T a)
    {
        if (a > 9) print(a / 10);
        putchar(a % 10 + '0');
    }

    struct Palindrome_tree {
        int N, last;
        struct T {
            int Next[30], fail, cnt, num, len;
        } tree[maxn];
        void init()
        {
            for (int i = 0; i <= N; i++) {
                for (int j = 0; j < 26; j++) tree[i].Next[j] = 0;
                tree[i].fail = tree[i].cnt = tree[i].num = tree[i].len = 0;
            }
            N = 1, tree[N].len = -1, tree[N].fail = 1;
            N++, tree[N].len = 0, tree[N].fail = 1;
            last = 2;
        }

        int get_fail(int x)
        {
            while (s[pos - 1 - tree[x].len] != s[pos]) x = tree[x].fail;
            return x;
        }

        void Add(int c)
        {
            int Now = get_fail(last);
            if (!tree[Now].Next[c]) {
                last = ++N;
                tree[N].len = tree[Now].len + 2;
                int f = get_fail(tree[Now].fail);
                tree[N].fail = tree[f].Next[c] ? tree[f].Next[c] : 2;
                tree[N].num = tree[tree[N].fail].num + 1;
                tree[Now].Next[c] = N;
            }
            else
                last = tree[Now].Next[c];
            tree[last].cnt++;
        }
    } palindrome_tree;

    struct SAM {
        int mm, son[maxn * 4][30], pre[maxn * 4], step[maxn * 4], last, total, root,
            p[maxn * 4], q[maxn * 4], h[maxn * 4];
        LL f[maxn * 4], g[maxn * 4];
        void init()
        {
            for (int i = 0; i <= total; i++) {
                for (int j = 0; j < 30; j++) son[i][j] = 0;
                pre[i] = step[i] = f[i] = g[i] = p[i] = q[i] = 0;
            }
            total = last = root = 1;
        }
        struct node {
            int node, Next;
        } G[maxn * 4];
        void Insert(int a, int b)
        {
            mm++;
            G[mm].node = b;
            G[mm].Next = h[a];
            h[a] = mm;
        }
        inline void Push(int v) { step[++total] = v; }
        void Extend(int ch)
        {
            Push(step[last] + 1);
            int p = last, np = total;
            for (; !son[p][ch]; p = pre[p]) son[p][ch] = np;
            if (!p)
                pre[np] = root;
            else {
                int q = son[p][ch];
                if (step[p] + 1 != step[q]) {
                    Push(step[p] + 1);
                    int nq = total;
                    memcpy(son[nq], son[q], sizeof(son[q]));
                    pre[nq] = pre[q];
                    pre[q] = pre[np] = nq;
                    for (; son[p][ch] == q; p = pre[p]) son[p][ch] = nq;
                }
                else
                    pre[np] = q;
            }
            last = np;
        }
        void dfs1(int u, int fa)
        {
            for (int Now = h[u]; Now != -1; Now = G[Now].Next) {
                int v = G[Now].node;
                if (v == fa) continue;
                dfs1(v, u);
                f[u] += f[v];
            }
            //        printf("fff%d %lld\n",u,f[u]);
        }
        void dfs2(int u, int fa)
        {
            g[u] = f[u] * (step[u] - step[fa]);
            g[u] += g[fa];
            for (int Now = h[u]; Now != -1; Now = G[Now].Next) {
                int v = G[Now].node;
                if (v == fa) continue;
                dfs2(v, u);
            }
            if (q[u] >= 1 && q[u] <= n) {
                ans += g[u] * d[q[u] + 1];
                sum += g[u];
                //            printf("aaa%d %lld\n",u,g[u]);
            }
        }
        void Build()
        {
            init();
            for (int i = 1; i <= 2 * n + 1; i++) Extend(s[i] - 'a');
            int tmp = 1;
            for (int i = 1; i <= 2 * n + 1; i++) {
                tmp = son[tmp][s[i] - 'a'];
                p[i] = tmp;
                q[tmp] = i;
                if (i > n + 1) f[tmp] = 1;
            }
            mm = -1;
            for (int i = 0; i <= total; i++) h[i] = -1;
            for (int i = 2; i <= total; i++) Insert(pre[i], i);
            dfs1(root, root);
            dfs2(root, root);
        }
    } sam;

    int main()
    {
        scanf("%d", &n);
        scanf("%s", S + 1);
        s[0] = '#';
        for (int i = 1; i <= n; i++) s[i] = S[n + 1 - i];
        palindrome_tree.init();
        for (int i = 1; i <= n; i++) {
            pos = i, palindrome_tree.Add(s[i] - 'a');
            d[n + 1 - i] = palindrome_tree.tree[palindrome_tree.last].num;
        }
        //    for (int i=1; i<=n; i++) printf("%d ",d[i]); printf("\n");
        for (int i = 1; i <= n; i++) s[i] = S[i];
        s[n + 1] = 'a' + 27;
        for (int i = 1; i <= n; i++) s[i + n + 1] = S[n + 1 - i];
        sam.Build();
        //    printf("%lld %lld\n",ans,sum);

        for (int l = 1, r = n; l < r; l++, r--) swap(S[l], S[r]);
        for (int i = 1; i <= n; i++) s[i] = S[n + 1 - i];
        palindrome_tree.init();
        for (int i = 1; i <= n; i++) {
            pos = i, palindrome_tree.Add(s[i] - 'a');
            d[n + 1 - i] = palindrome_tree.tree[palindrome_tree.last].num;
        }
        //    for (int i=1; i<=n; i++) printf("%d ",d[i]); printf("\n");
        for (int i = 1; i <= n; i++) s[i] = S[i];
        s[n + 1] = 'a' + 27;
        for (int i = 1; i <= n; i++) s[i + n + 1] = S[n + 1 - i];
        sam.Build();
        //    printf("%lld %lld\n",ans,sum);
        ans = ans + sum / 2;
        print(ans);
        putchar(10);
        return 0;
    }

## Trie树

    #define R 52//字符集大小
    class TrieNode {
    public:
        int val, sons_cnt;
        bool exist;
        TrieNode*nxt[R];
        TrieNode() :val(0), sons_cnt(0), exist(false) {
            for (int i = 0; i < R; i++)nxt[i] = nullptr;
        };
    };

    class Trie {
    public:
        TrieNode*root;
        Trie() { root = new TrieNode(); }
        void insert(string&s, int v) {
            insert(s, root, 0, v, nullptr);
        }
        void insert(string&s) {
            insert(s, root, 0, nullptr);
        }
        string find_longest_common_prefix(TrieNode*node) {
            if (node == nullptr || (node != nullptr&&node->sons_cnt > 1))return "";
            int i = 0;
            for (i = 0; i < R; i++) {
                if (node->nxt[i] != nullptr)break;
            }
            if (i == R)return "";
            char c = ((i > 25) ? ('A' + i) : ('a' + i));
            string tmp;
            tmp.insert(tmp.begin(), c);
            if (node->val)return "";
            else return tmp + find_longest_common_prefix(node->nxt[i]);
        }
    private:
        TrieNode* insert(string&s, TrieNode*node, int loc, int v, TrieNode*father) {
            int len = s.length();
            if (len == 0) {
                node->exist = true, node->val = v, node->sons_cnt++;
                return node;
            }
            if (node == nullptr) {
                node = new TrieNode();
                if (father != nullptr)father->sons_cnt++;
            }
            if (len == loc) { node->exist = true, node->val = v; return node; }
            if (s[loc] >= 'a')node->nxt[s[loc] - 'a'] = insert(s, node->nxt[s[loc] - 'a'], loc + 1, v, node);
            else node->nxt[s[loc] - 'A' + 26] = insert(s, node->nxt[s[loc] - 'A' + 26], loc + 1, v, node);
            return node;
        }
        TrieNode* insert(string&s, TrieNode*node, int loc, TrieNode*father) {
            int len = s.length();
            if (len == 0) {
                node->exist = true, node->sons_cnt++;
                return node;
            }
            if (node == nullptr) {
                node = new TrieNode();
                if (father != nullptr)father->sons_cnt++;
            }
            if (len == loc) { node->exist = true; return node; }
            if (s[loc] >= 'a')node->nxt[s[loc] - 'a'] = insert(s, node->nxt[s[loc] - 'a'], loc + 1, node);
            else node->nxt[s[loc] - 'A' + 26] = insert(s, node->nxt[s[loc] - 'A' + 26], loc + 1, node);
            return node;
        }
    };


    int main() {
        Trie a;
        vector<string>strs = { "she","shell","sells","sea","shore","s" };
        for (auto s : strs)a.insert(s);
        cout << a.find_longest_common_prefix(a.root) << endl;
        return 0;
    }
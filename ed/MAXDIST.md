# [MAXDIST](https://www.codechef.com/problems/MAXDIST)

> [Editorial chính thức của Codechef](https://discuss.codechef.com/t/maxdist-editorial/11113) 👈 **highly recommended, đơn giản và dễ hiểu hơn nồi lẩu ở dưới đây 🍲**

## Hmph
- Đầu tiên ta chọn một đỉnh ngẫu nhiên, DFS từ nó, giả sử đỉnh sâu nhất ta tìm được sau khi DFS là <code>v<sub>1</sub></code>
- DFS từ <code>v<sub>1</sub></code>, chọn ra một đỉnh sâu nhất (dĩ nhiên là đỉnh lá) và nó là đỉnh gặp đầu tiên, tạm gọi là đỉnh  <code>v<sub>2</sub></code>
- DFS tiếp từ đỉnh <code>v<sub>2</sub></code>, chọn ra tập đỉnh `S` có sao cho toàn bộ đều là các đỉnh sâu nhất, gọi độ sâu này là `D`, chọn ra một đỉnh  <code>u<sub>1</sub></code> có timein nhỏ nhất và  <code>u<sub>2</sub></code> có timein lớn nhất, tìm LCA của 2 đỉnh, gọi là `d`
- Đến đây ta có thể nối từ `d` đến <code>v<sub>2</sub></code> để giảm độ dài của toàn bộ các đỉnh thuộc tập `S` đến `v` (kiểm tra xem có thể vòng từ <code>u<sub>2</sub></code> sang <code>u<sub>1</sub></code> với độ dài đường đi = `D`, nếu có thì là `Nô`
- Từ đây đi ngược từ `d` lên <code>v<sub>2</sub></code>, kiểm tra xem có một đường đi nào đó từ các đỉnh thuộc `S` vòng qua một đỉnh `x` xong vòng xuống với cùng độ tại đường đi = `D`, nếu `x` là con của `d` thì không thể nối được, còn không thì nối `x` với `d`

## Code
- Trong lúc bấn loạn vì không debug nổi thì code không được chỉn chu cho lắm :( sumimasen <( _ _ )>
```c++
#include <bits/stdc++.h>
using namespace std;

#define pi pair<int, int>
#define inf32 0x3f3f3f3f
#define inf64 0x3f3f3f3f3f3f3f3f
#define all(x) x.begin(), x.end()
#define Unique(v) v.erase(unique(all(v)), v.end());
#define int long long
#define setinf(d) memset(d, inf32, sizeof d)
#define setneg(d) memset(d, -1, sizeof d)
#define set0(d) memset(d, 0, sizeof d)
#define Log2(x) 63 - __builtin_clzll(x)
#define oo 2e18
#define mod 1000000007
#define FILENAME "f"

const int maxn  = 1e4 + 5;

int n;
vector<int> edge[maxn];

int mxnode = 0;
int timein[maxn];
int timeout[maxn];
int timein2[maxn];
int depth[maxn];
int rmq[maxn];
int par[maxn];
int pos[maxn];
int sz[maxn];
int s[maxn][20];
int id;

void dfs1(int root, int parent, int depth){
    if(depth > mxnode) id = root, mxnode = depth;
    for(int &v : edge[root])
        if(v != parent) dfs1(v, root, depth + 1);
}

void dfs2(int root, int parent){
    sz[root] = 1;
    timein2[++timein2[0]] = root;
    pos[root] = timein2[0];
    par[root] = parent;
    timein[root] = ++timein[0];
    depth[root] = depth[parent] + 1;
    for(int &v : edge[root]){
        if( v != parent) dfs2(v, root),sz[root] += sz[v];
    }
    timeout[root] = ++timein[0];
}

void gen(){
    for(int i = 1; i <= n; i++) s[i][0] = par[i];
    for(int j = 1; 1 << j <= n; j++){
        for(int i = 1; i <= n; i++){
            if(s[i][j - 1]) s[i][j] = s[s[i][j - 1]][j - 1];
        }
    }
}

int lca(int u, int v){
    if(depth[u] < depth[v]) swap(u, v);
    int lg = log2(depth[u]);
    for(int i = lg; i >= 0; i--){
        if(depth[u] - (1 << i) >= depth[v]) u = s[u][i];
    }
    if(u == v) return u;
    for(int i = lg; i >= 0; i--){
        if(s[u][i] != s[v][i]) u = s[u][i], v = s[v][i];
    }
    return par[u];
}

void dfsclear(int root, int parent){
    depth[root] = 0; 
    for(int &v : edge[root])
        if(v != parent)
            dfsclear(v, root);
}

struct SparseTable{
    int *a, n, sign;
    vector<vector<int>> st;
    SparseTable(){}
    SparseTable(int N, int b[], int s): n(N) {
        init(n, b, s);
    }

    void init(int N, int b[], int s){
        if(n > maxn) return;
        for(int i = 0; i <= n; i++) st.push_back(vector<int>(15));
        a = b;
        n = N;
        sign = s;
    }

    void build(){
        for(int i = 1; i <= n; i++)
            st[i][0] = a[i];

        for(int j = 1; 1 << j <= n; j++){
            for(int i = 1; i <= n - (1 << j) + 1; i++){
                st[i][j] = sign * min(sign * st[i][j - 1], sign * st[i + (1 << (j - 1))][j - 1]);
            }
        }
    }

    int get(int l, int r){
        int lg = log2(r - l + 1);
        return sign * min(sign * st[l][lg], sign * st[r - (1 << lg) + 1][lg]);
    }
};

void solve(){
    set0(timein); set0(s); mxnode = 0; id = 0;
    cin >> n;
    for(int i = 1; i <= n; i++) edge[i].clear();
    for(int i = 1; i <= n - 1; i++){
        int a, b;
        cin >> a >> b;
        edge[a].push_back(b);
        edge[b].push_back(a);
    }
    
    if(n == 2){
        cout << "NO\n";
        return;
    }

    dfs1(1, 0, 1);
    dfs2(id, 0);
    int dia = *max_element(depth + 1, depth + n + 1);
    
    vector<int> v;
    for(int i = 1; i <= n; i++ ) if(depth[i] == dia) v.push_back(i);
    sort(all(v), [&](const int &a, const int &b){
        return timein[a] < timein[b];
    });
    
    set0(sz); set0(timein2); set0(par); set0(timein);
    dfs2(v.front(), 0);

    v.clear();
    for(int i = 1; i <= n; i++) if(depth[i] == dia) v.push_back(i);
    sort(all(v), [&](const int &a, const int &b){
        return timein[a] < timein[b];
    });
    gen();
    
    if(v.size() == 1){
        cout << "YES\n";
    }else{
        int d = lca(v.front(), v.back());
        int dd = (depth[v.front()] + depth[v.back()])  - 2 * depth[d] + 1;
        
        if(depth[v.front()] == dd){
            cout << "NO\n";
            return;
        }

        int oldD = depth[v.front()];
        dfsclear(d, par[d]);
        for(int i= 1; i <= n; i++) rmq[i] = depth[timein2[i]];
        SparseTable s(n, rmq, -1);  s.build();

        for(int x = par[d]; x != v.front(); x = par[x]){
            int mx = s.get(pos[x], pos[x] + sz[x] - 1);
            int newd = mx + oldD - 2 * depth[x] + 1;
            if(newd == oldD){
               if(depth[x] - depth[d] < -1){
                   cout << "YES\n";
                   return;
                }else{
                   cout << "NO\n";
                   return;
               }
            }
        }
        cout << "YES\n";
    }
}

signed main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);
    int t;
    cin >> t;
    while(t--) solve();
}
```

# SLOW

### Ý tưởng

- Khi kiểm tra một một con bò ăn cỏ tại chuồng `u` nào đó thì chỉ ta cần quan tâm đến những cánh đồng trên đường đi ngắn nhất từ đỉnh ruộng `1` đến `u`
- Khi DFS từ 1 thì những đỉnh ta đã đi qua chính là đường đi này, khi đó cần dùng một cây cối gì đó để biết trên đường đi từ `1` đến `u` có bao nhiêu cánh đồng đã có con bò đến trước
- Khi DFS đến một đỉnh, hãy đánh dấu vị trí `t` là thời gian có con bò đến cánh đồng đó là 1, tổng các vị trí từ `1` đến `t - 1` chính là đáp án cho con bò đó
- Khi kết thúc DFS tại một đỉnh thì hãy xóa đỉnh đó ra khỏi cái DS của ta vì đỉnh đó không còn nằm trong con đường của những con bò khác nữa

### Code
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
#define FILENAME "slow"

const int maxn = 1e5 + 5;

int n;
vector<int> edge[maxn];
int a[maxn], pos[maxn];
int ans[maxn];

struct FenwickTree{
    vector<int> bit;
    int n;
    FenwickTree(int n): n(n), bit(n + 1){}
    void update(int u, int v){
        while(u <= n) bit[u] += v, u += u & -u;
    }
    
    int get(int u){
        int s = 0;
        while(u) s += bit[u], u -= u & -u;
        return s;
    }
    
    int get(int l, int r){
        return get(r) - get(l - 1);
    }
};

void dfs(int root, int parent, FenwickTree &d){
    ans[pos[root]] = d.get(pos[root]);
    d.update(pos[root], 1);
    for(int &v : edge[root]){
        if(v != parent){
            dfs(v, root, d);
        }
    }
    d.update(pos[root], -1);
}

signed main(){
    freopen(FILENAME".inp", "r", stdin);
    freopen(FILENAME".out", "w", stdout);
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    cin >> n;

    FenwickTree d(n);

    for(int i = 1; i <= n - 1; i++){
        int a, b;
        cin >> a >> b;
        edge[a].push_back(b);
        edge[b].push_back(a);
    }
    for(int i = 1; i <= n; i++) cin >> a[i], pos[a[i]] = i;
    dfs(1, 0, d);
    for(int i = 1; i <= n; i++) cout << ans[i] << '\n';
}
```

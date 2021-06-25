# [K-th Ancestor](https://vjudge.net/contest/444351#problem/A)

<p align = "center"> 
  <img src = "https://github.com/LetterC67/public_archieve/blob/main/misc/pic/856993194704109578.png">
</p>

## Nhận xét và một số chú ý ( ͡°( ͡° ͜ʖ( ͡° ͜ʖ ͡°)ʖ ͡°) ͡°)
  - Có thể sử dụng Sqrt Decomposition để leo ngược lên nhưng như vậy sẽ TLE (Độ phức tạp `O(n sqrt n)`)
  - Thay vào đó sử dụng dp với độ phức tạp `O(n log n)`: 
    + `dp[i][j]` lưu lại cha thứ <code>2<sup>j</sup></code> của đỉnh có thứ tự `i`
    + `dp[i][j] = dp[dp[i][j - 1]][j - 1]`. Tại cha thứ <code>2<sup>(j - 1)</sup></code> của cha thứ <code>2<sup>(j - 1)</sup></code> của `i` cũng là cha thứ <code>2<sup>j</sup></code> của `i`
    + Với mỗi truy vấn tìm cha thứ `k` thì leo lên lần lượt một đoạn <code>2<sup>⌊log2(k)⌋</sup></code> đến cha thứ `k'` làm tương tự đến hết
  - Do đỉnh được nhập vào không có thứ tự nhất định nên phải ánh xạ lại từ `1...x`
 
 ## Code 👨‍💻
  - Một số biến:
    + `R` là root
    + `d[]` lưu độ sâu của từng đỉnh
    + `par[]` lưu cha của từng đỉnh
    + `id[]` lưu số thứ tự của từng đỉnh
    + `val[]` gía trị của đỉnh
    
 ```c++
#include <bits/stdc++.h>
using namespace std;

const int maxn = 1e5 + 5;

int n, q, d[maxn], par[maxn], R;

int dp[maxn][18], id[maxn], val[maxn], cnt = 0;

bool app[maxn];

vector<int> edge[maxn];

void dfs(int root, int parent, int depth){
    app[root] = true;
    d[root] = depth;
    par[root] = parent;
    for(int x: edge[root])
        if(x != parent)
            dfs(x, root, depth + 1);
}

void gen(){
    for(int i = 0; i <= n; i++)
        for(int j = 0; 1 << j <= n; j++)
            dp[i][j] = -1;

    for(int i = 0; i <= n; i++)
        dp[i][0] = par[val[i]];

    for(int j = 1; 1 << j <= n; j++)
        for(int i = 0; i <= n; i++)
            if(dp[i][j - 1] != -1)
                dp[i][j] = dp[id[dp[i][j - 1]]][j - 1];
}

void add(){
    int x, y;
    cin >> y >> x;

    app[x] = true;
    d[x] = d[y] + 1;
    par[x] = y;
    if(id[x] == -1) id[x] = cnt++, val[cnt - 1] = x;
    dp[id[x]][0] = par[x];

    for(int j = 1; 1 << j <= n; j++){
        if(dp[id[x]][j - 1] != -1){
            dp[id[x]][j] = dp[id[dp[id[x]][j - 1]]][j - 1];
        }
    }

    if(!y) R = x;
}


void dlt(){
    int x; cin >> x;
    app[x] = false;
}

void reset(){
    cnt = 0;
    d[0] = -1;
    for(int i = 1; i < maxn; i++)
        edge[i].clear();
    for(int i = 0; i < maxn; i++) for(int j = 0;  j < 18; j++) dp[i][j] = 0;
    fill(app, app + maxn, false);
    fill(id, id + maxn, -1);
    fill(val, val + maxn, -1);
}

int query(int x, int k){
    while(k){
        int lg = log2(k);
        k -= 1 << lg;
        x = dp[id[x]][lg];
    }
    return x;
}

void solve(){
    cin >> n;
    reset();
    
    for(int i = 0; i < n; i++){
        int a, b;
        cin >> a >> b;
        edge[a].push_back(b);
        edge[b].push_back(a);
        if(id[a] == -1) id[a] = cnt, val[cnt++] = a;
        if(id[b] == -1) id[b] = cnt, val[cnt++] = b;
        if(!a || !b) R = max(a, b);
    }

    dfs(R, 0, 0);

    gen();

    int q;
    cin >> q;
    while(q--){
        int cmd;
        cin >> cmd;
        if(cmd == 0) add();
        else if(cmd == 1) dlt();
        else{
            int x, k;
            cin >> x >> k;
            if(k > d[x] || !app[x]) cout << "0" << endl;
            else cout << query(x, k) << endl;
        }
    }
}

int main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    int t;
    cin >> t;
    while(t--) solve();
}
```

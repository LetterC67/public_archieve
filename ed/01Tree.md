# [0-1-Tree](https://codeforces.com/problemset/problem/1156/D)

## Ý tưởng
  - Sử dụng quy hoạch động, với mỗi đỉnh `u` thì lưu những giá trị sau:
    + `c0` số lượng đường đi toàn số 0 tính từ cạnh nối cha `u` và `u`
    + `c1` số lượng đường đi toàn số 1 tính từ cạnh nối cha `u` và `u`
    + `c01` số lượng đường đi không giàm số tính từ cạnh nối cha `u` và `u`
    + `c10` số lượng đường đi không tăng số tính từ cạnh nối cha `u` và `u`
    + **Trong công thức thì dãy toàn 0 và toàn 1 cũng tính là không giảm hoặc không tăng**
  - Xét đỉnh `v` và các con `u` của nó, có những trường hợp sau:
    + Cạnh nối `v` và `u` nối thêm vào một đường đi từ `u` đến các con của nó
    + Một đường đi từ <code>u<sub>1</sub></code> đi qua `v` và nối đến một đường đi từ <code>u<sub>2</sub></code>

## Chi tiết
- Gọi `t1`, `t0`, `t01`, `t10` lần lượt là tổng của các `c1`, `c0`, `c01`, `c10` của các con `u` của `v` đang xét
- TH1: Cạnh nối `v` và `u` là màu đen ta có các công thức:
  ```c++
    dp[v].c1 += dp[u].c1;
    dp[v].c10 += dp[u].c10;
    dp[v].c01 += dp[u].c1;
    res += dp[u].c10 + dp[u].c1;
  ```
   + Tại sao lại cập nhật `res` như vậy: `dp[u].c10` một đường từ một đỉnh nhận `u` làm tổ tiên đến `v` có dạng `000...111` hoặc toàn 1 và `dp[u].c1` là đường đi từ đỉnh `v` đến một đỉnh nhận `u` làm tổ tiên và toàn bộ đường đi là 1
  
- TH2: Cạnh nối `u` và `v` màu trắng tương tự:
```
  dp[v].c0 += dp[u].c0;
  dp[v].c01 += dp[u].c01;
  dp[v].c10 += dp[u].c0;
  a += dp[u].c01 + dp[u].c10;
```
- Xử lí đường đi qua đỉnh `v` chung cho cả 2 trường hợp:
  + TH1, từ 1 đường đi có dạng `000...111` đi lên `v` thì sẽ nối với các đường đi toàn 1
  + TH2, từ 1 đường đi toàn 0 thì nối với một đường đi không giảm
  + TH3, từ 1 đường đi toàn 1 thì nối với 1 đường toàn 1
- Công thức:
```c++
res += (dp[u].c10 - (dp[u].c1 + dp[u].c0)) * (t1 - dp[u].c1);
res += (dp[u].c0) * (t01 - (dp[u].c01));
res += (dp[u].c1) * (t1 - (dp[u].c1));
```
- Cập nhật giá trị của đỉnh hiện tại
  + Nếu đường đi từ cha `v` đến `v` là trắng:
  ```c++
  c0 += 1
  c1 = 0
  c01 += 1
  c10 = t0 + 1
  ```
  + Tương tự trường hợp đen
  ```c++
  c0 = 0
  c1 += 1
  c01 = t1 + 0
  c10 += 1
  ```

## Code 👨‍💻
- Code debug mãi mới được nên hơi rác mọi người thông cảm 
<div><img src = "https://user-images.githubusercontent.com/53361407/127339254-12b84540-b2d9-4d37-8bb4-a4d87c2f0490.png"  width = 250></img></div>

```c++
#include <bits/stdc++.h>
using namespace std;

#define int long long
#define pi pair<int, int>

const int maxn = 2e5 + 5;

int res = 0;
vector<pi> edge[maxn];

struct state{
    int c0, c1, c01, c10;
} dp[maxn];

void dfs(int root, int parent, int w){
    int a = 0;
    int t01 = 0, t10 = 0, t0 = 0, t1 = 0;
    
    for(pi v : edge[root]){
        int u = v.first, w = v.second;
        if(u == parent) continue;
        dfs(u, root, w);
        if(w){
            dp[root].c1 += dp[u].c1;
            dp[root].c10 += dp[u].c10;
            dp[root].c01 += dp[u].c1;
            a += dp[u].c10 + dp[u].c1;
        }else{
            dp[root].c0 += dp[u].c0;
            dp[root].c01 += dp[u].c01;
            dp[root].c10 += dp[u].c0;
            a += dp[u].c01 + dp[u].c10;
        }
        t10 += dp[u].c10;
        t01 += dp[u].c01;
        t0 += dp[u].c0 ;
        t1 += dp[u].c1;
    }
    
    for(pi v : edge[root]){
        int u = v.first, w = v.second;
        if(u == parent) continue;
        a += (dp[u].c10 - (dp[u].c1 + dp[u].c0)) * (t1 - dp[u].c1);
        a += (dp[u].c0) * (t01 - (dp[u].c01));
        a += (dp[u].c1) * (t1 - (dp[u].c1));
    }
    
    if(!w) dp[root].c0 += 1;
    else dp[root].c0 = 0;
    
    if(!w) dp[root].c01 += 1;
    else dp[root].c01 = t1 + 1;
    
    if(w) dp[root].c1 += 1;
    else dp[root].c1 = 0;
    
    if(w) dp[root].c10 += w;
    else dp[root].c10 = t0 + 1
    
    res += a;
}

signed main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    int n;
    cin >> n;
    for(int i = 0; i < n - 1; i++){
        int a, b, w;
        cin >> a >> b >> w;
        edge[a].push_back({b, w});
        edge[b].push_back({a, w});
    }
    dfs(1, 0, 0);
    cout << res;
}
```

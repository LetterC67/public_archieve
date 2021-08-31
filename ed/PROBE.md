# PROBE

## Ý tưởng

- Coi mỗi một ô vuông `k x k` không có số 1 là một đỉnh của đồ thị, thì kết quả đơn giản là tính số thành phần liên thông của đồ thị, cái này thì dễ rồi nhá
- Với mỗi ô `k x k` thì thỏa mãn thì kiểm tra ô trên và bên trái nó có thỏa mãn hay không, nếu có thì nối vào
- Sử dụng prefix sum 2D để nhanh chóng kiểm tra một hình vuông có thỏa mãn không trong `O(1)`

## Code
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

const int maxn = 1005;

int n, m, k;
int a[maxn][maxn];
int pre[maxn][maxn];
vector<int> edge[maxn * maxn];

bool visit[maxn * maxn];

inline int get(int x, int y, int k){
    int a = x - k + 1, b = y - k + 1;
    return pre[x][y] - pre[a - 1][y] - pre[x][b - 1] + pre[a - 1][b - 1];
}

inline int id(int x, int y){
    return (x - 1) * m + y;
}

void dfs(int root){
    visit[root] = true; 
    for(int &v : edge[root])
        if(!visit[v])
            dfs(v);
}

signed main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    cin >> n >> m >> k;
    for(int i = 1; i <= n; i++) for(int j = 1; j <= m; j++) cin >> a[i][j], pre[i][j] = pre[i - 1][j] + pre[i][j - 1] - pre[i - 1][j - 1] + a[i][j];

    for(int i = k; i <= n; i++){
        for(int j = k; j <= m; j++){
            if(!get(i, j, k)){
                edge[id(i, j)].push_back(id(i, j));
                if(i > k && !get(i - 1, j, k)){
                    edge[id(i, j)].push_back(id(i - 1, j));
                    edge[id(i - 1, j)].push_back(id(i, j));
                }
                if(j > k && !get(i, j - 1, k)){
                    edge[id(i, j - 1)].push_back(id(i, j));
                    edge[id(i, j)].push_back(id(i, j - 1));
                }
            }
        }
    }

    int cnt = 0;
    for(int i = 1; i <= n * m; i++){
        if(edge[i].size() && !visit[i]){
            cnt++;
            dfs(i);
        }
    }
    cout << cnt;
}
```

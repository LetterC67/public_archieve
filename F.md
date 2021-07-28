# [Respect](https://vjudge.net/contest/449202#problem/F)

## Ý tưởng
  - Coi mỗi ô là một đỉnh trong đồ thị, nếu số của 2 ô khác nhau thì trọng số là 1 và giống nhau thì là 0
  - Để tìm đường đi ngắn nhất thì có thể dùng dijkstra nhưng với 10 test mỗi test có `n` tối đa là 1000 thì độ phức tạp không thể qua được giới hạn thời gian 1 giây
  - Thay vào đó sử dụng [BFS 0-1](https://cp-algorithms.com/graph/01_bfs.html) na ná như vậy nhưng độ phức tạp thấp hơn do không dùng heap

## Code
```c++
#include <bits/stdc++.h>
using namespace std;

const int maxn = 1e3 + 69;

int dir[4][2] = {
    {1, 0},
    {0, 1},
    {-1, 0},
    {0, -1},
};
int n, m;
char c[maxn][maxn];
int d[maxn][maxn];
bool visit[maxn][maxn];

bool valid(int x, int y){
    return x && y && x <= n && y <= m;
}

int elaina(){
    memset(d, 0x3f, sizeof d);
    memset(visit, 0, sizeof visit);
    deque<pair<int, int>> q;
    q.push_back({1, 1});
    d[1][1] = 0;
    while(q.size()){
        int x = q.front().first;
        int y = q.front().second;
        q.pop_front();
        if(x == n && y == m) return d[x][y];
        for(int i = 0; i < 4; i++){
            int a = x + dir[i][0],
                b = y + dir[i][1];
            if(valid(a, b)){
                int w = c[x][y] != c[a][b];
                if(d[a][b] > d[x][y] + w){
                    d[a][b] = d[x][y] + w;
                    if(w) q.push_back({a, b});
                    else q.push_front({a, b});
                }
            }
        }
    }
    return 0;
}

void solve(){
    cin >> n >> m;
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= m; j++)
            cin >> c[i][j];
    cout << elaina() << '\n';   
}

int main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    int t;
    cin >> t;
    while(t--) solve();
}
```

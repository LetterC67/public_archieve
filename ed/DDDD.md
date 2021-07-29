# [D](https://vjudge.net/contest/449433#problem/D)

## Ý tưởng
  - Cần sử dụng DP để tính xem với `n` loại xu ban đầu thì các giá trị `x` tốn ít nhất bao nhiêu đồng
  - Do các đồng xu đề khác nhau nên có thể thử từng đồng xu trong những đồng xu chưa được chọn, với mỗi giá trị `x` thuộc đoạn `[l, r]` và giá trị đồng xu mới là `k` thì thử từ `0` đến `⌊x/k⌋`
 
## Code ˋ( ° ▽、° ) 
```c++
#include <bits/stdc++.h>
using namespace std;

const int maxn = 200005;

int n, a[430];
int dp[maxn];
int l, r;
bool used[maxn];

int f(int p){
    if(!p) return 0;
    if(dp[p]) return dp[p];
    dp[p]= 1e9;
    for(int i = 1; i <= n; i++){
        if(a[i] <= p){
            dp[p] = min(dp[p], f(p - a[i]) + 1);
        }
    }
    return dp[p];
}

int main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    cin >> n >> l >> r;
    
    int o = 0;
    for(int i = 1; i <= n; i++) cin >> a[i], used[a[i]] = true;
    for(int i = l; i <= r; i++) o += f(i);
    
    int New = 0;
    for(int i = 1; i <= 200000; i++){
        if(!used[i]){
            int t = 0;
            for(int k = l; k <= r; k++){
                int mn = 1e9;
                for(int g = 0; g <= k / i; g++) {
                    mn = min(mn, f(k - g * i) + g);
                }
                t += mn;
            }
            if(t < o) o = t, New = i;
        }
    }
    cout << New;
}
```

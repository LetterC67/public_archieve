# [cut day](https://codeforces.com/group/ILNBVucTlt/contest/338158/problem/C)

## Nhận xét
  - Với dãy `a` nếu ta xóa các phần tử cạnh nhau có giá trị bằng nhau thì kết quả bài toán không thay đổi
  - Nén dãy lại để xử lí tiện hơn

## Chi tiết
  - Sử dụng DP với `dp[i]` là số lần `không cần cắt` ít nhất nếu dùng các số từ `1` đến `a[i]` và phần tử vị trí thứ `i` ở cuối dãy
  - Nếu chỉ có 1 giá trị `a[i]` xuất hiện trong dãy
    + `dp[i] = max(dp[i], dp[j])` với toàn bộ giá trị `a[j] == a[i] - 1`
    + Nếu `a[i] = a[i - 1] + 1` thì `dp[i] = max(dp[i], dp[i - 1] + 1)`
  - Nếu có nhiều hơn 1:
    + `dp[i] = max(dp[i], dp[j])` với toàn bộ giá trị `a[j] = a[i] - 1`
    + `dp[i] = max(dp[i], dp[p] + 1)` với `a[p] + 1 == a[i]` và `a[p + 1] == a[i]` và `p + 1 # i`
  - Có thể dùng 2 biến để lưu 2 giá trị lớn nhất hoặc là dùng `set`

## Code
```c++
#include <bits/stdc++.h>
using namespace std;

#define pi pair<int, int>
#define INF 0x3f3f3f3f
#define all(x) x.begin(), x.end()
#define pb push_back

const int maxn = 1e6 + 5;

int n, p, mx;
int t[maxn], a[maxn];
int cnt[maxn];
int dp[maxn];
int mxdp[maxn];
set<pair<int, int>> s;
vector<int> v;
vector<int> pos[maxn];

int main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    cin >> n;
    for(int i = 1; i <= n; i++){
        cin >> t[i];
        if(t[i] != t[i - 1]) a[++p] = t[i], v.pb(a[p]);
    }
    n = p;
    sort(all(v));
    v.erase(unique(all(v)), v.end());
    for(int i = 1; i <= n; i++) a[i] = lower_bound(all(v), a[i]) - v.begin() + 1, cnt[a[i]] += a[i] == a[i - 1] + 1, pos[a[i]].pb(i);
    mx = *max_element(a + 1, a + n + 1);
    
    for(int p : pos[1]) if(a[p + 1] == 2) s.insert({0, p});
    
    for(int i = 2; i <= mx; i++){
        if(pos[i].size() == 1){
            int p = pos[i].front();
            for(int x: pos[i - 1]) dp[p] = max(dp[p], dp[x]);
            if(a[p - 1] + 1 == a[p]) dp[p] = max(dp[p], dp[p - 1] + 1);
        }else{
            int mxdp = 0;
            for(int x: pos[i - 1]) mxdp = max(mxdp, dp[x]);
            for(int p: pos[i]){
                dp[p] = mxdp;
                if(s.size()){
                    auto it = --s.end();
                    if((*it).second + 1 == p){
                        if(s.size() == 1) continue;
                        else --it;
                    }
                    dp[p] = max(dp[p], (*it).first + 1);
                }
            }
        }
        s.clear();
        for(int p: pos[i]) if(a[p] + 1 == a[p + 1]) s.insert({dp[p], p});
    }
    
    int res = 0;
    for(int x : pos[mx]){
        res = max(res, dp[x]);
    }
    cout << n - res - 1;
}
```



# Bài B
- Có thể xét từng số 2. Bạn nào nháp ra là thấy - Anh Hoàng ○|￣|_ =3

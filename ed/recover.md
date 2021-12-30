# Recover

### Ý tưởng:
- Trước tiên cần tìm cách so sánh 2 xâu sao cho nhanh:
	+ Gọi `f[i][j]` là giá trị lớn nhất sao cho 2 xâu con `s[i...i + f[i][j]] = s[j...j + f[i][j]]`, tính mảng `f` trong <code>O(|S|<sup>2</sup>)</code>
	+ Muốn so sánh 2 xâu con bắt đầu từ vị trí `i` và `j` có độ dài `l` thì chỉ cần dùng mảng `f` để tìm vị trí đầu tiên 2 xâu con này khác nhau rồi so sánh là được.

- Gọi `dp[i][j]` là số cách chia kết thúc ở vị trí `i` và xâu cuối cùng có độ dài là `j` (hoặc là xâu con cuối cùng là `s[i - j + 1...i]`. Có thể tính mảng `dp` dễ dàng trong  <code>O(|S|<sup>3</sup>)</code>/. Muốn cải tiến thành  <code>O(|S|<sup>2</sup>)</code> cần sử dụng mảng `g[i][j]` ý nghĩa là tổng toàn bộ `dp[i][k]` với `k < j`

### Code mẫu:
```c++
#include <bits/stdc++.h>
using namespace std;

#define pi pair<int, int>
#define inf32 0x3f3f3f3f
#define inf64 0x3f3f3f3f3f3f3f3f
#define all(x) x.begin(), x.end()
#define Unique(v) v.erase(unique(all(v)), v.end());
#define setinf(d) memset(d, inf32, sizeof d)
#define setneg(d) memset(d, -1, sizeof d)
#define set0(d) memset(d, 0, sizeof d)
#define Log2(x) 63 - __builtin_clzll(x)
#define oo 2e18
#define mod 2017
#define int short
#define FILENAME "f"

const int maxn = 5005;

string s;

int f[maxn][maxn];
int dp[maxn][maxn];
int g[maxn][maxn];

inline void inc(int &a, int b){
    if((a += b) >= mod) a -= mod;
}

bool Greater(int a, int b, int len){
    return f[a][b] < len && s[a + f[a][b]] > s[b + f[a][b]];
}

signed main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    cin >> s; s = '?' + s;
    int n = s.size() - 1;

    for(int i = n; i >= 1; i--)
        for(int j = n; j >= 1; j--)
            if(s[i] == s[j]) f[i][j] = f[i + 1][j + 1] + 1;
            else f[i][j] = 0;


    for(int i = 1; i <= n; i++){
        dp[i][i] = 1;
        for(int j = 1; j <= n; j++){
            inc(g[i][j], g[i][j - 1]);
            inc(g[i][j], dp[i][j - 1]);
            if(j < i){
                if(s[i - j + 1] == '0') continue;
                inc(dp[i][j], g[i - j][j]);
                if(i - j >= j && Greater(i - j + 1, i - j - j + 1, j))
                    inc(dp[i][j], dp[i - j][j]);
            }
        }
    }

    int ans = 0;
    for(int i = 1; i <= n; i++) inc(ans, dp[n][i]);
    cout << ans;
}

```

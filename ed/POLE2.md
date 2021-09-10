# POLE2

## Ý tưởng 
- Dễ thấy cần sử dụng quy hoạch động, công thức quy hoạch động là `dp[i][j]`, mang ý nghĩa đã xây đến cột thứ `i` và độ cao của cột đó là `j` thì cần tối thiểu bao nhiêu tiền.
- Độ phức tạp thời gian là `O(n * max(h[i]) ^ 2)) ~ O(n ^ 3)`

## Cải tiến
- Giả sử đang xét đến cột thứ `i` và đang xét ở độ cao <code>h<sub>i</sub></code>và độ cao ở cột thứ `i - 1` là <code>h<sub>i - 1</sub></code> độ cao ban đầu của cột `i` là <code>H<sub>i</sub></code>, dễ thấy chi phí bỏ ra là:

## <code>|h<sub>i</sub> - h<sub>i - 1</sub>| * cost[i] + dp[i - 1][h<sub>i - 1</sub>] + (h<sub>i</sub> - H<sub>i</sub>)</code>

- Nếu chia ra làm 2 TH (vì có giá trị tuyệt đối) thì có thể viết lại như sau, đây là 1 TH còn lại tương tự:
## <code>(h<sub>i</sub>  * cost[i]  + (h<sub>i</sub> - H<sub>i</sub>)) - (h<sub>i - 1</sub> * cost[i] - dp[i - 1][h<sub>i - 1</sub>])</code>
- Bây giờ với mỗi `i` và <code>h<sub>i</sub></code> chỉ cần tìm <code>h<sub>i - 1</sub></code> nào đó sao cho <code>-(h<sub>i - 1</sub> * cost[i] - dp[i - 1][h<sub>i - 1</sub>])</code> là nhỏ nhất
- Sử dụng deque tìm min trên đoạn tịnh tiến, độ phức tạp `O(n * max(h[i])) ~ O(n ^ 2)`

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

const int maxn = 4001;

int n, d;
int dp[maxn][maxn * 2 + 69];
int cost[maxn];
int h[maxn];
deque<int> q;

signed main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    setinf(dp);

    cin >> n >> d;
    for(int i = 1; i <= n - 1; i++) cin >> cost[i];
    for(int i = 1; i <= n; i++) cin >> h[i];
    
    int mx = maxn * 2;
    
    for(int i = h[1]; i <= mx; i++) dp[1][i] = (i - h[1]) * (i - h[1]);
    
    for(int i = 2; i <= n; i++){
    	q.clear();

        for(int j = max(1ll, h[i] - d); j <= h[i] - 1; j++){
            if(dp[i - 1][j] == inf64) continue;
            while(q.size() && -q.back() * cost[i - 1] + dp[i - 1][q.back()] >= -j * cost[i - 1] + dp[i - 1][j]) q.pop_back();
            q.push_back(j);
        }

        for(int j = h[i]; j <= mx; j++){
            if(dp[i - 1][j] != inf64){
                while(q.size() && -q.back() * cost[i - 1] + dp[i - 1][q.back()] >= -j * cost[i - 1] + dp[i - 1][j]) q.pop_back();
                q.push_back(j);
            }
            if(!q.size()) continue;
            dp[i][j] = min(dp[i][j], (j - q.front()) * cost[i - 1] + dp[i - 1][q.front()] + (j - h[i]) * (j - h[i]));
            if(q.front() + d <= j) q.pop_front();
        }

        q.clear();

        for(int j = mx; j >= h[i]; j--){
            if(dp[i - 1][j] != inf64){
                while(q.size() && q.back() * cost[i - 1] + dp[i - 1][q.back()] >= j * cost[i - 1] + dp[i - 1][j]) q.pop_back();
                q.push_back(j);
            }
            if(!q.size()) continue;
            dp[i][j] = min(dp[i][j], (q.front() - j) * cost[i - 1] + dp[i - 1][q.front()] + (j - h[i]) * (j - h[i]));
            if(q.front() - d >= j) q.pop_front();
        }
    }

    cout << *min_element(dp[n] + 1, dp[n] + mx + 1);
}
```

# [Ehehehe](https://vjudge.net/contest/446610#problem/E)

[<div align = "center"><img src = "https://user-images.githubusercontent.com/53361407/125470312-93838e6a-bc8b-4e3e-b356-6531143df47f.jpg" width = 250></img></div>](https://vnoi.info/wiki/algo/math/modular-inverse.md)

  
## Ý tưởng
 - Sử dụng quy hoạch động với trạng thái là `dp[u][0/1]` với ý nghĩa là:
   + `dp[u][1]` là số cách cắt để cây con có đỉnh `u` tồn tại duy nhất 1 đỉnh màu đen
   + `dp[u][0]` là số cách cắt để cây con có đỉnh `u` không tồn tại đỉnh màu đen (chính xác là phải tính cả trường hợp cắt bỏ cả cạnh nối cây con này nữa)
 - Số lượng cách cắt được đếm đều đảm bảo các cây đã bị cắt ra chỉ có duy nhất 1 đỉnh màu đen

## Chi tiết
  - Với mỗi đỉnh ta luôn khởi tạo `dp[u][0] = 1` và `dp[u][1] = 0` vì luôn có thể cắt đỉnh nối `u` và cha nó tạo thành một cây không có đỉnh đen
  - Chia ra 2 trường hợp:
   ### TH1: Đỉnh `u` đang xét là đỉnh màu đen
   <div align = "center"><img src = "https://user-images.githubusercontent.com/53361407/125459313-0c0559c1-5e57-4c0c-b68d-7f8bc3841ab7.png" width = 300></img></div>
   <div align = "center"><i> Đang xét đỉnh 1 màu đen</i></div>
   
   </br>
   
   - Dễ nhận thấy để có thể thêm đỉnh `u` vào thì các cây con có gốc là con của đỉnh `u` không được phép có đỉnh màu đen hoặc là có thể cắt luôn cạnh nối `i` và con của nó. Dưới đây là một cách cắt thỏa mãn:
    
   </br>
   <div align = "center"><img src = "https://user-images.githubusercontent.com/53361407/125460261-1d0b694a-7944-4359-bb7e-0614e2218c71.png" width = 300></img></div>
   
   - Từ đây rút ra được công thức: 
   
      + `dp[u][1] = ∏dp[v][0]` với `v` là các con của `u`
      + `dp[u][0] = dp[u][1]` tại chỉ có duy nhất cách cắt cạnh nối `u` và cha nó để tạo cây không có màu đen

  ### TH2: Đỉnh `u` đang xét là đỉnh màu trắng
  <div align = "center"><img src = "https://user-images.githubusercontent.com/53361407/125462064-9831db21-35c8-4e29-9cfe-0d9f1aead996.png" width = 300></img></div>
  <div align = "center"><i> Đang xét đỉnh 1 màu trắng</i></div>
  
  </br>
  
  - Tạm gọi `c(u)` là số lượng con của đỉnh `u`. Trường hợp này để có thể có một cây con có đỉnh `u` gồm 1 đỉnh màu đen thì ta sẽ cắt sao cho số lượng cây con có đỉnh là con của `u` không có đinh màu đen là `c(u) - 1`. Cây con còn lại có một đỉnh màu đen kết hợp với đỉnh `u` để tạo ra một cây con có một đỉnh màu đen.
  <div align = "center"><img src = "https://user-images.githubusercontent.com/53361407/125463109-16e87a09-b87a-44b7-9628-05ff656dfd4f.png" width = 300></img></div>
  
  - Ta có công thức:
    + Gọi `F = ∏dp[v][0]`
    + `dp[u][1] =  Σ(F / dp[v][0] * dp[v][1]) + dp[u][1]` với `v` là các con của `u`, mỗi lần loại bỏ cách chọn cây toàn trắng của đỉnh con `v` và thay vào cách chọn có một đỉnh đen
    + `dp[u][0] = F + dp[u][1]` với `v` là các con của `u`. Cần cộng thêm `dp[u][1]` vì ta hoàn toàn có thể cắt đỉnh nối `u` và cha nó và có thêm `dp[u][1]` các cắt thỏa mãn

## Cài đặt
Cách 1: Giống như công thức QHD bên trên
```python
def dfs(root, parent):
    for x in edge[root]:
        if x != parent:
            dfs(x, root)
            dp[root][0] *= dp[x][0]
 
    if color[root]:
        dp[root][1] = dp[root][0]
        return
 
    for x in edge[root]:
        if x != parent:
            dp[root][1] += dp[root][0] // dp[x][0] * dp[x][1]
            
    dp[root][0] += dp[root][1]
```
- Đây là một code cài bằng Python thể hiện luôn công thức, khá rõ ràng. Việc cài bằng C++ như thế này sẽ không khả thi vì như code trên không lấy mod dẫn đến tràn số, vậy để lấy mod cần dùng [Nghịch đảo modulo](https://www.youtube.com/watch?v=dQw4w9WgXcQ)

```c++
#include <bits/stdc++.h>
using namespace std;

#define int long long

const int maxn = 1e5 + 5, mod = 1e9 + 7;

int n;

vector<int> edge[maxn];

int dp[maxn][2];
int color[maxn];

int pw(int a, int b){
    if(b == 1) return a;
    int t = pw(a, b / 2) % mod;
    return t * t % mod * (b % 2 ? a : 1) % mod;
}

void dfs(int root, int parent){
    dp[root][1] = 0, dp[root][0] = 1;

    for(int x: edge[root]){
        if(x != parent){
            dfs(x, root);
            dp[root][0] *= dp[x][0];
            dp[root][0] %= mod;
        }
    }

    if(color[root]){
        dp[root][1] = dp[root][0];
        return;
    }
    
    for(int x: edge[root]){
        if(x != parent){
            dp[root][1] += dp[root][0]  * pw(dp[x][0], mod - 2) % mod * dp[x][1] % mod;
            dp[root][1] %= mod;
        }
    }

    dp[root][0] += !color[root] * dp[root][1];
}

signed main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    cin >> n;
    for(int i = 0; i < n - 1; i++){
        int t;
        cin >> t;
        edge[t].push_back(i + 1);
        edge[i + 1].push_back(t);
    }
    
    for(int i = 0; i < n; i++) cin >> color[i];

    dfs(0, -1);

    cout << dp[0][1];
}
```



Cách 2: Cách này ta lần lượt xử lí từng đỉnh con một nên công thức hơi khác với công thức quy hoạch động được đề cập bên trên, không cần sử dụng nghịch đảo modulo
  + `s` là lưu số lượng cách để cắt sao cho toàn bộ các cây con trước toàn trắng
  + `f` (tương đương `dp[i][1]`), mỗi lần phải nhân thêm với `dp[x][0]` để tính các trường hợp lấy cây con có 1 màu đen đằng trước, cộng thêm lấy cây con đỉnh `v` hiện tại
```c++
void dfs(int root, int parent){
    int f = 0, s = 1;

    for(int v: edge[root]){
        if(v != parent){
            dfs(x, root);
            f = f * dp[v][0] + s * dp[v][1];
            s *= dp[v][0];
            f %= mod, s %= mod;
        }
    }

    dp[root][1] = (color[root] ? s : f);
    dp[root][0] = f * !color[root] + s;
}
```

> Link ở ảnh trên nhá!

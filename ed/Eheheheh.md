# [€](https://atcoder.jp/contests/abc173/tasks/abc173_f?lang=en)

## Nhận xét (✿◠‿◠)
 - Giả sử ta đang xét một số đỉnh và không có cạnh nối giữa chúng
 <div align = "center"><img src = "https://user-images.githubusercontent.com/53361407/126877856-bec55894-5ce4-4368-b191-02ba6d4ba466.png" width = 300></img></div>
 
 - Dễ nhận thấy rằng với mỗi 1 cạnh thêm vào thì sẽ giảm số thành phần liên thông của đồ thị đi 1 vì nó đồ thi ban đầu có dạng cây

 <div align = "center"><img src = "https://user-images.githubusercontent.com/53361407/126877852-2e642158-d242-4857-9675-57495e299ad1.png" width = 300></img></div>
 <div align = "center"><i> Nối 3 cạnh, số thành phần liên thông giảm đi 3</i></div>

## Chi tiết
- Giả sử toàn bộ đồ thị chưa có cạnh, từ đó có `f(l, r) = r - l + 1` với mọi `l, r` thỏa mãn
- Với mỗi cạnh `(u, v)`, ta cần đếm xem cạnh đó giảm số thành phần liên thông của bao nhiêu `g(l, r)` là số đồ thị con tạo bởi đỉnh từ `l` đến `r`
- Với cạnh `(u, v)` thì chỉ có những `g(l, r)` với `l <= u` và `v <= r` mới có thể bao gồm cạnh này nên số thành phần liên thông bớt đi là `u * (n - v + 1)` (giả sử `u <= v`)

## Code
- short
```c++
#include <bits/stdc++.h>
using namespace std;

#define int long long

int n;
int res;

signed main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    cin >> n;
    for(int i = 1; i <= n; i++){
        res += i * (i + 1) / 2;
    }
    for(int i = 0; i < n - 1; i++){
        int a, b;
        cin >> a >> b;
        if(a > b) swap(a, b);
        res -= a * (n - b + 1);
    }
    
    cout << res;
}
```

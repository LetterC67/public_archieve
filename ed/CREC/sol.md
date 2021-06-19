# CREC
![Alt Text](https://github.com/LetterC67/public_archieve/blob/main/misc/pic/767065973072068639.gif)


## Đề bài
- Không có nha tự thân vận động o((⊙﹏⊙))o

## Nhận xét 🤔
- Mỗi hình chữ nhật có 2 đường chéo có độ dài bằng nhau, cắt nhau tại trung điểm. Vậy để đếm số lượng hình chữ nhật được tạo thành thì ta cần quan tâm đến những đoạn thẳng có cùng độ dài và cùng trung điểm

## Lời giải 🧾
- Giả sử có 2 điểm `A` và `B` có tọa độ lần lượt là <code>x<sub>1</sub></code>, <code>y<sub>1</sub></code> và <code>x<sub>2</sub></code>, <code>y<sub>2</sub></code>
  + Độ dài đoạn thẳng `AB` là <code>L = √((x<sub>1</sub> - x<sub>2</sub>)<sup>2</sup> + (y<sub>1</sub> - y<sub>2</sub>)<sup>2</sup>)</code>
  + Trung điểm đoạn thẳng `AB` là điểm <code>M((x<sub>1</sub> + x<sub>2</sub>) / 2, y<sub>1</sub> + y<sub>2</sub>) / 2)</code> 
- Để thuận tiện cho việc xử lí thì cần lưu giá trị <code>L' = (x<sub>1</sub> - x<sub>2</sub>)<sup>2</sup> + (y<sub>1</sub> - y<sub>2</sub>)<sup>2</sup></code> và điểm <code>M'((x<sub>1</sub> + x<sub>2</sub>), y<sub>1</sub> + y<sub>2</sub>))</code> để không cần xử lí số thực
- Đếm đếm thì có thể sắp xếp lại để đếm nhưng có cách nhanh hơn là sử dụng `map` nên chẳng cần sort làm gì cho mệt (⌐■_■)
- Giả sử có `k` đoạn thẳng cùng trung điểm và cùng độ dài thì sẽ tạo ra được `k * (k - 1) / 2` hình chữ nhật
 
## Code 👩‍💻
- Code uy tín đã test 👌

```c++
#include <bits/stdc++.h>
using namespace std;

#define x first
#define y second
#define int long long

const int maxn = 1005;

struct diagonal{
    int length;
    int x;
    int y;
};

bool operator < (const diagonal& a, const diagonal& b){
    if(a.length != b.length) return a.length < b.length;
    return make_pair(a.x, a.y) < make_pair(b.x, b.y);
}

int n, res = 0;

pair<int, int> a[maxn];

map<diagonal, int> mp;

signed main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    cin >> n;

    for(int i = 1; i <= n; i++) cin >> a[i].x >> a[i].y;

    for(int i = 1; i <= n - 1; i++){
        for(int j = i + 1; j <= n; j++){
            int len = (a[j].x - a[i].x) *  (a[j].x - a[i].x) + (a[j].y - a[i].y) * (a[j].y - a[i].y);
            int cenx = a[i].x + a[j].x;
            int ceny = a[j].y + a[i].y;
            mp[diagonal{len, cenx, ceny}]++;
        }
    }

    for(auto x: mp)
        res += x.second * (x.second - 1) / 2;

    cout << res;
}
```

> Có thể sử dụng `map<pair<int, pair<int, int>>` để không phải viết lại operator cho key

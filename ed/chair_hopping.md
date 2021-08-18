# [Chair Hopping](https://vjudge.net/contest/453843#problem/B)

## Ý tưởng
- Người ở vị trí `i` sẽ di chuyển đến vị trí `s[i]` sau 1 lượt, vậy ta xây một đồ thị có hướng có cạnh từ `i` đến `s[i]`
- Đề bài cho vị trí sau 2 lượt nhảy nên ta dựng đồ thị có cạnh từ `i` đến `s[s[i]]`. Sau khi nối toàn bộ lại thì ta sẽ được một số cái ~~bánh vòng~~ thành phần liên thông đa dạng nhiều kích cỡ.
 <div align = "center"><img src = "https://user-images.githubusercontent.com/53361407/129912832-17aee2ed-342f-4e73-80ca-aa95e998ea77.png"></img></div>
 <br>

- Đương nhiên là cái bánh vòng của ta vẫn chưa đủ vì ta đã skip mất 1 bước, thực tế nó phải trông như thế này 🙄
 <br>
<div align = "center"><img src = "https://user-images.githubusercontent.com/53361407/129913862-4b1c1a1c-bfe8-4ebe-96bc-8465e316bdfa.png"></img></div>
 <br>

- Dễ thấy chỉ có thể ghép các cái bánh cùng cỡ, vậy ta cần đếm số lượng cách ghép các cái bánh, đó là kết quả bài toán
<br>
<div align = "center"><img src = "https://user-images.githubusercontent.com/53361407/129940058-891427ba-e10d-47e7-b3ea-52e2cdd1adf2.png"></img></div>

## Chi tiết
### Về đếm số lượng cái bánh và cỡ của nó có thể dùng DSU
### Còn về cách tính số lượng cách ghép thì ta cần chia ra 2 TH:
 - **TH có kích cỡ lẻ**:
 	+ Một cái bánh có thể ghép với chính nó với 1 cách ghép duy nhất, hoặc có thể ghép với 1 cái bánh khác với số lượng cách ghép bằng kích cỡ của nó (vẽ ra là biết ^_~)
 	+ Gọi `s(i)` là số lượng cái bánh cỡ `i`, `f(j)` là số lượng cách có thể ghép với `j` cái bánh
 	+ Đầu tiên có `f(0)` = `f(1)` = `1`
 	+ Nhận thấy khi đang có `j - 1` cái bánh, nếu ta thêm một cái bánh thì lại có các TH
 		+ Cái bánh mới thêm ghép với chính nó
 		+ Cái bánh mới thêm ghép với một trong `j - 1` cái cũ, còn lại `j - 2` ghép tùy thích
 	+ Vậy công thức của ta sẽ là `f(j) = f(j - 1) + i * (j - 1) * f(j - 2)`
- **TH có kích cỡ chẵn**:
	+ Một cái bánh cỡ chẵn thì không thể ghép với chính nó, vẽ ra là thấy nha ...
	+ Vậy nếu số lượng cái bánh của cái bánh cỡ `i` nào đó với `i` chẵn mà là lẻ thì không có cách nào chọn cả, output ra `0` rồi dừng
	+ Gọi `s(i)` là số lượng cái bánh cỡ `i`, `f(j)` là số lượng **cách ghép 2 cái bánh với nhau**, ý chỗ này là chỉ cần biết cái bánh `x` ghép với cái bánh `y` tính là 1 lần, mà không tính nó có thể có số lượng cách ghép bằng kích cỡ của nó
	+ `f(2) = 1`
	+ `f(j) = f(j - 2) + (j - 2) * f(j - 2)` nó cũng tựa tựa trường hợp lẻ
	+ Do có `s(i) / 2` cặp mà mỗi cặp có `i` các chọn nên kết quả cuối cùng là `f(s(i)) * 2 ^ (s(i) / 2))`

## Code 
```c++
#include <bits/stdc++.h>
using namespace std;

#define all(x) x.begin(), x.end()
#define Unique(v) v.erase(unique(all(v)), v.end());
#define int long long
#define mod 1000000007

const int maxn = 1e5 + 5;

int n;
int a[maxn], cnt[maxn], f[maxn];
bool chosen[maxn];

struct DisjointSet{
    vector<int> parent, sz;
    DisjointSet(int n): parent(n + 1), sz(n + 1){
        for(int i = 1; i <= n; i++) parent[i] = i, sz[i] = 1;
    }

    int find(int u){
        if(u != parent[u]) return parent[u] = find(parent[u]);
        return u;
    }

    bool join(int u, int v){
        u = find(u);
        v = find(v);
        if(u == v) return false;
        parent[u] = v;
        sz[v] += sz[u];
        return true;
    }
};

int Power(int a, int b){
    if(!b) return 1;
    int t = Power(a, b / 2);
    return t * t % mod * (b % 2 == 1 ? a : 1) % mod;
}

signed main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    cin >> n;
    DisjointSet d(n);

    for(int i = 1; i <= n; i++) cin >> a[i], d.join(a[i], i);
    for(int i = 1; i <= n; i++){
        int r = d.find(i);
        if(!chosen[r]){
            cnt[d.sz[r]]++;
            chosen[r] = true;
        }
    }

    int ans = 1;

    for(int i = 1; i <= n; i++){
        if(cnt[i]){
            if(i % 2 == 0 && cnt[i] % 2) return cout << 0, 0;
            if(i % 2){
                f[0] = f[1] = 1;
                for(int j = 2; j <= cnt[i]; j++){
                    f[j] = (f[j - 1] + i * (j - 1) * f[j - 2]) % mod;
                }
                ans *= f[cnt[i]];
            }else{
                f[2] = 1;
                for(int j = 4; j <= cnt[i]; j++){
                    f[j] = (f[j - 2] + (j - 2) * f[j - 2]) % mod;
                }
                ans *= f[cnt[i]] * Power(i, cnt[i] >> 1) % mod;
            }
            ans %= mod;
        }
    }

    cout << ans;
}
```

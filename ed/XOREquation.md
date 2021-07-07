# [C - XOR Equation](https://vjudge.net/contest/445668#problem/C)

<div align = "center" ><img src = "https://github.com/LetterC67/public_archieve/blob/main/misc/pic/komi.jpg" width = 500></div>

## Nhận xét
  - `a xor b = c` thì có `a xor c = b` và `b xor c = a` 😲
  - Có tối đa 10 dấu `?` nếu quay lui thử 10 số vào mỗi dấu `?` thì sẽ TLE (<code>10<sup>10</sup></code>), thay vào đó thử 2 số có ít dấu `?` nhất thì tối đa chỉ cần thử 6 dấu `?`

## Code
> Nếu có dấu `?` ở vị trí đầu tiên của mỗi số thì không được thay bằng số `0` NGOẠI TRỪ trường hợp số chính là dấu `?` thì nó có thể là `0`. 3 sub của em .·´¯`(>▂<)´¯`·. 

```c++
#include <bits/stdc++.h>
using namespace std;

#define int long long

string s, a[4];
int cnt[4], sz[4], res, p[4];
int Power[10] = {1, 10, 100, 1000, 10000, 100000, 1000000, 10000000, 100000000};

vector<int> ques[4];

bool check(){
    string t = to_string(p[0] ^ p[1]);
    if(t.size() != a[2].size()) 
        return false;

    for(int i = 0; i < t.size(); i++)
        if(t[i] != a[2][i] && a[2][i] != '?') 
            return false;
            
    return true;
}

int elaina(int lf, int nx){
    if(!lf) return res += check();
    int i = !(lf > cnt[1]);
    int pos = sz[i] - ques[i][nx] - 1;
    for(int k = (ques[i][nx] == 0 && sz[i] != 1); k <= 9; k++){
        p[i] += k * Power[pos];
        elaina(lf - 1, (nx + 1) * (lf - 1 != cnt[1]));
        p[i] -= k * Power[pos];
    }
    return 0;
}

void process(){
    a[0].assign(s.begin(), s.begin() + s.find(' '));
    a[1].assign(s.begin() + s.find(" xor") + 5, s.begin() + s.find(" ="));
    a[2].assign(s.begin() + s.find(" = ") + 3, s.end());

    sort(a, a + 3, [](string a, string b){
        return count(a.begin(), a.end(), '?') < count(b.begin(), b.end(), '?');
    });

    for(int i = 0; i < 3; ++i) cnt[i] = count(a[i].begin(), a[i].end(), '?'), sz[i] = a[i].size();
    for(int i = 0; i < 3; i++){
        for(int j = 0; j < a[i].size(); j++){
            ::p[i] = ::p[i] * 10 + (a[i][j] == '?' ? 0 : a[i][j] - '0');
            if(a[i][j] == '?') ques[i].push_back(j);
        }
    }

    elaina(cnt[0] + cnt[1], 0);
    cout << res;
}

signed main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);
    getline(cin, s);
    process();
}
```



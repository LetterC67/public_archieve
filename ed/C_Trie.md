# [C](https://vjudge.net/contest/448740#problem/C)
> palindrome gọi là pal và `x.pf(i)` là hậu tố bắt đầu từ `i` của xâu `x`

<div align = "center">
  
`__/\\\\\\\\\\\\\\\____/\\\\\\\\\______/\\\\\\\\\\\__/\\\\\\\\\\\\\\\_ `
`__\///////\\\/////___/\\\///////\\\___\/////\\\///__\/\\\///////////__`
`________\/\\\_______\/\\\_____\/\\\_______\/\\\_____\/\\\_____________`
`_________\/\\\_______\/\\\\\\\\\\\/________\/\\\_____\/\\\\\\\\\\\_____`
`__________\/\\\_______\/\\\//////\\\________\/\\\_____\/\\\///////______`
`___________\/\\\_______\/\\\____\//\\\_______\/\\\_____\/\\\_____________`
`____________\/\\\_______\/\\\_____\//\\\______\/\\\_____\/\\\_____________`
`_____________\/\\\_______\/\\\______\//\\\__/\\\\\\\\\\\_\/\\\\\\\\\\\\\\\_`
` _____________\///________\///________\///__\///////////__\///////////////__`          
</div>

### Code hơi rối ＞﹏＜ với cả chẳng may có viết sai ở đâu thì báo lại nhá

## Nhận xét:
- Gọi `X'` là xâu đảo ngược của xâu `X`
- Giả sử ta đang xét 2 xâu `S` và `T`, để `S + T` là một xâu pal thì có 2 TH:
  + `T` có độ dài ngắn hơn `S`: `S` sẽ có dạng `T' + P` với `P` là một xâu pal
  + `T` có độ dài dài hơn `S` : `T'` sẽ có dạng `S + P` với `P` là một xâu pal
- Vậy để kiểm tra xâu `T` có thể ghép cặp với xâu `S` nào đó không, ta kiểm tra 2 TH:
  + `T'` có độ dài ngắn hơn: `T'` phải là tiền tố của một xâu `S` sao cho `S.pf(len(T') + 1)` phải là pal
  + `T'` có độ dài dài hơn:  Một xâu `S` nào đó phải là tiền tố của xâu `T'` sao cho `T.pf(len(S) + 1)` phải là pal

## Chi tiết
- Sử dụng Trie, ngoài đường đi đến các node con, chèn lần lượt các xâu ban đầu vào và mỗi node lưu các giá trị sau:
  + Số lượng xâu kết thúc ở node đó (end)
  + Giả sử độ sâu của node là `i` tính từ 1 thì có bao nhiêu xâu đi qua node đó thỏa mãn `X.pf(i)` là pal, tính cả xâu rỗng ở cuối (pal)
<br>
   <div align = "center"><img src = "https://user-images.githubusercontent.com/53361407/126900855-54121160-7575-41b1-8159-820517baed53.png"></img></div>
   <div align = "center"><p><i>Một cây Trie với 2 xâu <code>baabab</code> và <code> baabbb </i></code></p></div>
<br>

- Khi xét xâu `T` cần đảo ngược lại thành xâu `T'`, khi đang xét trên cây cần xét các TH:
  + Nếu ở vị trí `i` có `T'.pf(i)` là pal, cộng thêm số lượng xâu kết thúc ở node đó `res = res + node[v].end` (TH `T'` dài hơn xâu kia)
  + Ở node kết thúc của `T'`, `res = res + node[v].pal` (TH `T'` ngắn hơn hoặc bằng)
  + **Chú ý nếu `T` là pal thì cần trừ kết quả đi 1 vì kết quả đã tính cả chính T**
- Mỗi xâu `S` và xâu đảo ngược `S'` của nó, cần tính trước những vị trí `i` sao cho `X.pf(i)` là pal, có thể dùng 🔪hash🔪để tính 

## Code
```c++
#include <bits/stdc++.h>
using namespace std;

#define int long long

const int maxn = 1e6 + 5;
const int mod = 1e9 + 7, base = 113;

int n, res, pw[maxn];
string s[maxn], revs[maxn];
vector<int> pal[maxn], palrev[maxn];

void init(){
    pw[0] = 1;
    for(int i = 1; i < maxn; i++)
        pw[i] = (pw[i - 1] * base) % mod;
}

struct node{
    node *child[26] = {NULL};
    int end = 0;
    int pal = 0;
} trie;

struct Hash{
    int h[maxn];
    void gohash(string &s){
        s = '?' + s;
        for(int i = 1; i < s.size(); i++)
            h[i] = (h[i - 1] * base + s[i]) % mod;
    }
    int get(int l, int r){
        return (h[r] - h[l - 1] * pw[r - l + 1] % mod + mod) % mod;
    }
} hs, ht;

void insert(int id, node *root, string s){
    node* it = root;
    int i = 1, p = 0;
    for(char c: s){
        if(!it -> child[c - 'a']) it -> child[c - 'a'] = new node();
        it = it -> child[c - 'a'];
        if(p != pal[id].size() && pal[id][p] == i + 1) it -> pal++, p++;
        i++; 
    }
    it -> end++;
}

int go(int id, node *root, string &s){
    node* it = root;
    int i = 1, ans = 0, p = 0;
    for(char c: s){
        if(!it -> child[c - 'a']) return ans;
        it = it -> child[c - 'a'];
        if(p != palrev[id].size() && palrev[id][p] == i + 1) ans += it -> end, p++;
        i++;
    }
    ans += it -> pal;
    return ans;
}

void process(vector<int> pal[], int x, string s){
    string t = s;
    reverse(t.begin(), t.end());
    hs.gohash(s), ht.gohash(t);
    for(int i = s.size() - 2; i >= 1; i--){
        if(hs.get(t.size() - i, t.size() - 1) == ht.get(1, i)){ 
           pal[x].push_back(s.size() - i);
        }
    }
}

void reset(){
    for(int i = 1; i <= n; i++) pal[i].clear(), palrev[i].clear();
    trie = node();
    res = 0;
}

void solve(){
    cin >> n;
    reset();

    for(int i = 1; i <= n; i+=1){
        cin >> s[i]; revs[i] = s[i];
        reverse(revs[i].begin(), revs[i].end());
        process(pal, i, s[i]);
        process(palrev, i, revs[i]);
        pal[i].push_back(s[i].size() + 1);
    }

    for(int i = 1; i <= n; i++){
        insert(i, &trie, s[i]);
    }

    for(int i = 1; i <= n; i++){
        res += go(i, &trie, revs[i]) - (revs[i] == s[i]);
    }

    cout << res << '\n';
}

signed main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);
    init();
    int t;
    cin >> t;
    while(t--) solve();
}
```

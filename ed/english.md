# [Japanese](https://www.codechef.com/problems/ENGLISH)

## Nhận xét
 - Với 2 xâu bất kì thanh vì tính <code>l<sub>p</sub></code> (tiền tố chung dài nhất) và <code>l<sub>s</sub></code> (hậu tố chung dài nhất) rồi tìm min thì thay vào đó có thể so sánh cùng một lúc kí tự đầu và kí tự cuối nên chỉ cần xét tiền tố. Ví dụ:
  + So sánh `abchhfg` và `abegg` thì ở vị trí đầu tiên và cuối cùng cả 2 xâu đều có `ag` chung, ở vị trí tiếp theo là `bf` và `bg` khác nhau -> đáp án là 1

## Chi tiết
 - Xây cây trie, mỗi node có `26 * 26 = 676` con, lưu cả số lượng xâu đi qua vị trí đó
 - Khớp từ những tiền tố có độ dài dài nhất trước 

## Code
```c++
#include <bits/stdc++.h>
using namespace std;

#define pi pair<int, int>
#define INF 0x3f3f3f3f
#define all(x)  x.begin(), x.end()
#define pb push_back
#define int long long

int n, res;
string s;

struct node{
    node *child[676];
    int end = 0;
    int pass = 0;
} trie;

int id(char a, char b){
    return (a - 'a') * 26 + b - 'a';
}

void insert(node *trie, string &s){
    node *it = trie;
    for(int i = 0; i < s.size(); i++){
        int nx = id(s[i], s[s.size() - i - 1]);
        if(!it -> child[nx]) it -> child[nx] = new node();
        it = it -> child[nx];
        it -> pass++;
    }
}

int go(node *trie, int depth){
    int del = 0;
    for(int i = 0; i < 676; i++){
        if(trie -> child[i]){
            del += go(trie -> child[i], depth + 1);
        }
    }
    trie -> pass -= del;
    res += (trie -> pass / 2) * depth * depth;
    return (trie -> pass / 2) * 2 + del;
}

void solve(){
    cin >> n;
    trie = node();
    for(int i = 0; i < n; i++){
        cin >> s;
        insert(&trie, s);
    }
    go(&trie, 0);
    cout << res << '\n'; res = 0;
}

signed main(){
    ios_base::sync_with_stdio(false); cin.tie(0); cout.tie(0);

    int t;
    cin >> t;
    while(t--) solve();
}
```

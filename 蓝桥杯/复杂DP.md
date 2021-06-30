# 复杂DP

蓝桥杯复杂DP

# 鸣人的影分身 

![image-20210302234159156](https://gitee.com/xddadd/cloud-image/raw/master/master/20210302234159.png)

![image-20210302234228343](https://gitee.com/xddadd/cloud-image/raw/master/master/20210302234228.png)

## 经典问题，m个苹果分m个盘子方案

![image-20210302234818873](https://gitee.com/xddadd/cloud-image/raw/master/master/20210302234818.png)

时间复杂度O(NM)

## 递归AC代码

```cpp
#include <iostream>
using namespace std;

int T, n , m;

int f(int m, int n){
    if (m == 0) return 1;//0个苹果,所有盘子都分0
    if (n == 0) return 0;//0个盘子
    if (n > m){//盘子比苹果多,每个盘子至多分一个,
        return f(m ,m);
    }
    //一般情况
    return f(m, n - 1) + f(m - n, n);
    
}

int main(){
    cin >> T;
    while (T --){
        cin >> m >> n;
        int res = f(m, n);
        cout << res << endl;
    }
    return 0;
}
```

# 糖果

![image-20210303112921367](https://gitee.com/xddadd/cloud-image/raw/master/master/20210303112928.png)

![image-20210303112957611](https://gitee.com/xddadd/cloud-image/raw/master/master/20210303112957.png)

## 01背包变形问题

![image-20210303114718866](https://gitee.com/xddadd/cloud-image/raw/master/master/20210303114719.png)

时间复杂度O(NK)

因为%k，所以不能压缩空间

## AC代码

```cpp
#include <iostream>
#include <cstring>
using namespace std;

const int N = 1e3 + 10;

int f[N][N];
int n, k;

int main(){
    cin >> n >> k;
    
    memset(f, -0x3f, sizeof f);
    f[0][0] = 0;
    for (int i = 1 ; i <= n ; i ++){
        int w;
        cin >> w;
        for (int j = 0 ; j < k ; j ++){
            f[i][j] = f[i - 1][j];
            f[i][j] = max(f[i][j], f[i - 1][(j + k - w % k) % k] + w);
        }
    }
    cout << f[n][0];
    return 0;
}

```

# 密码脱落

![image-20210304175728127](https://gitee.com/xddadd/cloud-image/raw/master/master/20210304175728.png)

## 区间DP(最长回文子序列长度)

如样例，对于每个密码来说

若整个串是回文串，则表示无脱落，否则有脱落，脱落多少个相当于去掉多少个字符，使得整个串是回文串，即可以求最长回文子序列的长度。

![image-20210304182714249](https://gitee.com/xddadd/cloud-image/raw/master/master/20210304182714.png)

时间复杂度O(N^2^)，空间复杂度O(N^2^)

## AC代码

```cpp
#include <iostream>
#include <cstring>
using namespace std;

const int N = 1e3 + 10;

int n;
string s;
int f[N][N];

int main(){
    //读入
    cin >> s;
    n = s.size();
    
    //区间dp
    for (int len = 1 ; len <= n ; len ++){
        for (int l = 0 ; l + len - 1 < n ; l ++){
            int r = l + len - 1;
            if (len == 1) f[l][r] = 1;//特判区间长度为1
            else {
                if (s[l] == s[r]) f[l][r] = f[l + 1][r - 1] + 2;
                f[l][r] = max(f[l][r], f[l][r - 1]);
                f[l][r] = max(f[l][r], f[l + 1][r]);
            }
        }
    }
    
    cout << n - f[0][n - 1];
    return 0;
    
}
```

# 生命之树

![image-20210305152104803](https://gitee.com/xddadd/cloud-image/raw/master/master/20210305152104.png)

![image-20210305152128303](https://gitee.com/xddadd/cloud-image/raw/master/master/20210305152128.png)

## 树形DP

![image-20210305163226407](https://gitee.com/xddadd/cloud-image/raw/master/master/20210305163226.png)

时间复杂度O(N)，空间复杂度O(N)

## AC代码

```cpp
#include <iostream>
#include <cstring>
using namespace std;

typedef long long LL;
const int N = 1e5 + 10;

int n;
LL f[N];
int w[N];
int h[N], e[N * 2], ne[N * 2], idx;

void add(int a,int b){
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx ++;
}

//第二个参数是记录父节点，防止向回走
void dfs(int u,int fa){
    f[u] = w[u];
    
    for (int p = h[u] ; p != -1 ; p = ne[p]){
        int j = e[p];
        if (j != fa){
            dfs(j, u);
            f[u] += max(0ll, f[j]);
        }
    }
}



int main(){
    //读入
    cin >> n;
    for (int i = 1 ; i <= n ; i ++) cin >> w[i];
    memset(h, -1, sizeof h);
    for (int i = 0 ; i < n - 1 ; i ++){
        int a, b;
        cin >> a >> b;
        add(a, b);add(b, a);//无向图
    }
    
    //树形DP
    dfs(1, -1);
    
    LL res = -1e10;
    for (int i = 1 ; i <= n ; i ++) res = max(res, f[i]);
    
    cout << res << endl;
    return 0;
}
```

# 斐波那契前 n 项和

![image-20210305210950345](https://gitee.com/xddadd/cloud-image/raw/master/master/20210305210950.png)

# 包子凑数

![image-20210306013948225](https://gitee.com/xddadd/cloud-image/raw/master/master/20210306013948.png)

![image-20210306014002273](https://gitee.com/xddadd/cloud-image/raw/master/master/20210306014002.png)

## 数论+完全背包

1.如何确定有解无解。

若这n个数是互质的，即最大公约数d=1，则说明一定有有限个数凑不出来，即有解。

若n个数并不是互质的，即最大公约数>1，则说明有无限个数凑不出来，即无解。

具体看y总证明。

2.有解的情况求解

对于n个数来说，若有解，最大不能凑出的数是`N = (100 - 1) * (99 - 1) - 1`，这样能够确定我们背包问题的最大体积。

![image-20210306020157073](https://gitee.com/xddadd/cloud-image/raw/master/master/20210306020157.png)

最终扫描一遍凑不出的体积数量即为答案。

时间复杂度O(NM)，空间O(M)

每个状态只和上层状态有关，因此可以状态压缩到一维

## AC代码

```cpp
#include <iostream>
using namespace std;

const int N = 1e5 + 10;

int n, d;
bool f[N];
int w[N];

int gcd(int a,int b){
    return b ? gcd(b, a % b) : a;
}

int main(){
    //读入
    cin >> n;
    for (int i = 1 ; i <= n ; i ++) {
        cin >> w[i];
        d = gcd(d, w[i]);//最大公约数
    }
    
    //分情况讨论是否有解
    if (d > 1) cout << "INF\n";
    else {
        //有解
        
        f[0] = true;//初始化
        //背包DP
        for (int i = 1 ; i <= n ; i ++){
            for (int j = w[i] ; j < N ; j ++){
                f[j] |= f[j - w[i]];
            }
        }
        
        //统计不可凑出的数
        int res = 0;
        for (int i = 0 ; i < N ; i ++) 
            if (!f[i])
                res ++;
                
        cout << res;
    }
    
    
    return 0;
}
```

# 括号配对

![image-20210306161038916](https://gitee.com/xddadd/cloud-image/raw/master/master/20210306161046.png)

## 区间DP

该题类似于密码脱落和石子合并两题的结合，因为若括号序列是回文串，那么一定是合法的。但是该题新加了一个条件即`()[]`这样并列的括号也是合法的。

![image-20210306180329926](https://gitee.com/xddadd/cloud-image/raw/master/master/20210306180330.png)

时间复杂度O(N^3^)，空间复杂度O(N^2^)

## AC代码

```cpp
#include <iostream>
#include <cstring>
using namespace std;

const int N = 1e3 + 10;

string s;
int n;
int f[N][N];

bool check(char a ,char b){
    if (a == '(' && b == ')') return true;
    if (a == '[' && b == ']') return true;
    return false;
}

int main(){
    //读入
    cin >> s;
    n = s.size();
    
    //区间DP
    for (int len = 1 ; len <= n ; len ++){
        for (int i = 0 ; i + len - 1 < n ; i ++){
            int j = i + len - 1;
            if (len == 1) f[i][j] = 1;//初始化
            else {
                f[i][j] = 1e9;
                if (check(s[i], s[j])) f[i][j] = f[i + 1][j - 1];
                else f[i][j] = f[i + 1][j - 1] + 2;
                f[i][j] = min(f[i][j], min(f[i + 1][j], f[i][j - 1]) + 1);
                //并列的情况，枚举分界点
                for (int k = i ; k < j ; k ++){
                    f[i][j] = min(f[i][j], f[i][k] + f[k + 1][j]);
                }
            }
        }
    }
    
    cout << f[0][n - 1];
    return 0;
}
```


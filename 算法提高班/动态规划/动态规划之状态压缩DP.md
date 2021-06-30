# 动态规划之状态压缩DP

提高课状态压缩DP

# 棋盘式(连通性)状态压缩DP

# 小国王

![image-20210331091050416](https://gitee.com/xddadd/cloud-image/raw/master/image-20210331091050416.png)

## 状态压缩DP

![image-20210331101027518](https://gitee.com/xddadd/cloud-image/raw/master/image-20210331101027518.png)

时间复杂度O(NKM),空间复杂度O(MM)

## AC代码

```cpp
#include <iostream>
#include <vector>
using namespace std;


typedef long long LL;
const int N = 12, M = 1 << N , K = 110;

int n , k;
int cnt[M];//记录状态为i的二进制1的个数
vector<int> valid;//记录a | b 是否不存在有连续的两个1的   所有合法状态
vector<int> st[M];//st[a][b]表示，a能被那些合法状态b转移
LL f[N][K][M];


//计算s的二进制位1的个数
int count(int s){
    int res = 0;
    while (s){
        s -= (s & (-s));
        res ++;
    }
    return res;
}

//判断s状态是否合法，即s二进制是否不存在有连续两个1
bool check(int s){
    for (int i = 0 ; i < n ; i ++){
        if ((s >> i & 1) && (s >> (i + 1) & 1))
            return false;
    }
    return true;
}

int main(){
    cin >> n >> k;
    
    //预处理cnt和valid
    for (int i = 0 ; i < 1 << n ; i ++){
        cnt[i] = count(i);
        if (check(i))
            valid.push_back(i);
    }
    
    //预处理对于a状态来说，哪些b状态能转移到a
    for (int i = 0 ; i < valid.size() ; i ++){
        for (int j = 0 ; j < valid.size() ; j ++){
            int a = valid[i], b = valid[j];
            if ((a & b) == 0 && check(a | b)) {
                st[a].push_back(b);
            }
        }
    }
    
    
    //状态压缩DP
    f[0][0][0] = 1;//初始化
    for (int i = 1; i <= n + 1; i ++ )
        for (int j = 0; j <= k; j ++ )
            //枚举所有合法状态
            for (int u = 0; u < valid.size() ; u ++ )
                //枚举合法状态能被那些状态合法的转移
                for (int v = 0 ; v < st[valid[u]].size() ; v ++){
                    int a = valid[u], b = st[valid[u]][v];
                    int c = cnt[a];
                    if (j >= c)
                        f[i][j][a] += f[i - 1][j - c][b];
                }
               

    cout << f[n + 1][k][0] << endl;

    return 0;

    
}
```

# 玉米田

![image-20210614113233292](https://gitee.com/xddadd/cloud-image/raw/master/image-20210614113233292.png)

## 状态压缩DP

> 该题和之前的状态压缩DP不一样的点在于，部分区域不能放。
>
> 因为我们用二进制表示放置的状态，放为1，不放为0，所以将每层不能放的置为1，表明这里开始就已经种植了玉米，在后面状态转移方程的时候，与每层的状态作`&`运算判断合法性，即当前的状态和初始状态不能重复。即当前状态放置的不能和初始化的已经放置有重复。

![image-20210614133633860](https://gitee.com/xddadd/cloud-image/raw/master/image-20210614133633860.png)

时间复杂度$O(N2^M)$，空间复杂度$O(2^M)$

## AC代码

```cpp
#include <iostream>
#include <cstring>
#include <vector>
using namespace std;

const int N = 14, M = 1 << N, MOD = 1e8;

int n, m;
int g[N]; //g[i]表示每层初始化的状态,把不能种植的土地看成已经种植的土地
vector<int> valid;
vector<int> st[M];
int f[N][M];

//判断s状态是否合法，即s二进制是否不存在有连续两个1
bool check(int s) {
    for (int i = 0 ; i < m ; i ++) {
        if ((s >> i) & 1 && (s >> (i + 1)) & 1)
            return false;
    }
    return true;
}

int main() {
    //读入
    cin >> n >> m;
    for (int i = 0 ; i < n ; i ++) {
        int s = 0;
        for (int j = m - 1 ; j >= 0 ; j --) {
            int t;
            cin >> t;
            if (!t) s |= (1 << j);
        }
        g[i] = s;
    }
    
    //初始化所有合法的状态
    for (int i = 0 ; i < 1 << m ; i ++)
        if (check(i))
            valid.push_back(i);
    
    //初始化a状态，能被哪些b状态转移到
    for (int i = 0 ; i < valid.size() ; i ++) {
        for (int j = 0 ; j < valid.size() ; j ++) {
            int a = valid[i], b = valid[j];
            if ((a & b) == 0)
                st[a].push_back(b);
        }
    }
    
    //状态压缩DP
    f[0][0] = 1;
    for (int i = 1 ; i <= n + 1 ; i ++) {
        int s = g[i - 1];
        for (int j = 0 ; j < valid.size() ; j ++) {
            if (!(s & valid[j])) {
                for (int u = 0 ; u < st[valid[j]].size() ; u ++) {
                    int a = valid[j], b = st[a][u];
                    f[i][a] = (f[i][a] + f[i - 1][b]) % MOD;
                }
            }
            
        }
    }
    
    
    cout << f[n + 1][0];
    return 0;
}
```

# 炮兵阵地

![image-20210614152911519](https://gitee.com/xddadd/cloud-image/raw/master/image-20210614152911519.png)

![image-20210614152919066](https://gitee.com/xddadd/cloud-image/raw/master/image-20210614152919066.png)

## 状态压缩DP

​	该题和之前的题有了一些转变：不仅和上一行有关，还和上上一行有关，而且从求最优方案数变成了最大放置数量。

状态表示：`f[i][j][k]`表示已经放完前`i - 1`行，在放第`i`行，第`i`行的状态是`j`，第`i-1`行的状态是`k`的  摆放的炮兵部队的数量最大值。

> 下图中没列举当前层状态和上一层状态是否会有炮放置在山地，代码中需要判断

![image-20210614180922459](https://gitee.com/xddadd/cloud-image/raw/master/image-20210614180922459.png)

时间复杂度$O(N2^M2^M2^M)$，空间复杂度$O(N2^M2^M)$

## AC代码

```cpp
#include <iostream>
#include <vector>
using namespace std;

const int N = 110, M = 1 << 11;

int g[N];
int n, m;
int f[2][M][M];
int cnt[N];
vector<int> valid;


bool check(int s) {
    for (int i = 0 ; i < m ; i ++) {
        int a = s >> i & 1, b = s >> i + 1 & 1, c = s >> i + 2 & 1;
        if (a == 1 && (b || c))
            return false;
    }
    return true;
}

int count(int s) {
    int res = 0;
    for (int i = 0; i < m; i ++ )
        if (s >> i & 1)
            res ++ ;
    return res;
}

int main() {
    //读入
    cin >> n >> m;
    for (int i = 1 ; i <= n ; i ++) {
        int s = 0;
        for (int j = m - 1 ; j >= 0 ; j --) {
            char c;
            cin >> c;
            if (c == 'H') s |= (1 << j);
        }
        g[i] = s;
    }
    
    //初始化所有合法状态
    for (int i = 0 ; i < 1 << m ; i ++) 
        if (check(i)) {
            valid.push_back(i);
            cnt[i] = count(i);
        }
            
            
            
    
    //状态压缩DP
    for (int i = 1 ; i <= n + 2 ; i ++) {
        for (int j = 0 ; j < valid.size() ; j ++) {
            for (int k = 0 ; k < valid.size() ; k ++) {
                for (int u = 0 ; u < valid.size() ; u ++) {
                    int b = valid[k], a = valid[u], c =valid[j];
                    if (c & g[i] | g[i - 1] & b) continue; //上一层和该层不能在山地放置炮
                    if ((a & b) || (b & c) || (a & c)) continue;  //相邻2行/1行不能同时放炮
                    f[i & 1][c][b] = max(f[i & 1][c][b], f[i - 1 & 1][b][a] + cnt[c]);
                }
            }
        }
    }
    
    cout << f[n + 2 & 1][0][0];
    
    return 0; 
}

```


# DP

# AcWing 1454. 异或和是质数的子集数

![image-20210627210909564](https://gitee.com/xddadd/cloud-image/raw/master/image-20210627210909564.png)

## 背包DP

题意：给定一个数组，求出存在所有子集的异或和是质数。

> 由题意分析，所有数的范围都在$1-5000$，则可以推出所有子集的异或和的范围在$1-8191$之间。
>
> 我们可以转换成求得从所有数中选，有多少个方案能凑出异或和为$1-8191$，再枚举一遍$1-8191$中为质数的方案累加即可。

1. 背包DP，求解从所有数中选，能凑出异或和为$1-8191$的方案分别有多少。
2. 枚举异或和为$1-8181$的方案，若当前异或和为质数，则累加到答案中。

- 则问题转换成01背包异或方案DP

> 状态表示：`f[i][j]`表示从前`i`个数中选，异或和为`j` 的方案数
>
> ![image-20210627212117104](https://gitee.com/xddadd/cloud-image/raw/master/image-20210627212117104.png)
>
> 又基于状态数组一维是$5000$，二维是$8000+$，空间会开不下，而且每一层的状态只依赖于上一层状态，则我们可以用滚动数组来优化，优化到一维是2。
>
> 滚动数组使用主要是数组的第一维不同而已：`f[i % 2]`

时间复杂度$O(NM)$，N为N个数，M为异或和的范围

## AC代码

```cpp
#include <iostream>
#include <algorithm>
#include <cmath>
using namespace std;

const int N = 1e4 + 10, M = 8192, MOD = 1e9 + 7;

int f[2][M];
int n;
int a[N];

//判断x是否是质数
bool check(int x) {
    int t = sqrt(x);
    for (int i = 2 ; i <= t ; i ++) {
        if (x % i == 0)
            return false;
    }
    return true;
} 

int main() {
    //读入
    cin >> n;
    for (int i = 1 ; i <= n ; i ++) cin >> a[i];
    
    //背包DP
    f[0][0] = 1;//方案初始化
    for (int i = 1 ; i <= n ; i ++) {
        for (int j = 0 ; j < M ; j ++) {
            f[i & 1][j] = f[i - 1 & 1][j];
            if ((j ^ a[i]) < M)
                f[i & 1][j] = (f[i & 1][j] + f[i - 1 & 1][j ^ a[i]]) % MOD; 
        }
    }
    
    //枚举所有异或和的可能性
    int res = 0;
    for (int i = 2 ; i < M ; i ++) {
        if (check(i))
            res = (res + f[n & 1][i]) % MOD;
    }
    
    cout << res << endl;

    return 0;
    
}
```

# AcWing 1612. 最大正方形

![image-20210729210119347](https://gitee.com/xddadd/cloud-image/raw/master/master/20210729210119.png)

## DP

比较经典的一个DP了吧

状态表示：`f[i][j]`表示以`g[i][j]`为右下角的组成正方形的  最大边长

状态转移划分：

向左上方衍生边长

![image-20210729211736673](https://gitee.com/xddadd/cloud-image/raw/master/master/20210729211736.png)

图片来自于：[我要乱说出去](https://www.acwing.com/solution/content/34820/)大佬

状态方程：左，上，左上三个取min再+1

初始化：$i == 0 || j == 0 || i == 0 || j == 0$时候，这时候，一定有一边扩展不出去，则一定为0，所以矩阵状态可以从1开始存，0行0列的也初始化为0即可。

时间复杂度$O(N^2)$，空间复杂度$O(N^2)$

## AC代码

```cpp
#include <iostream>
using namespace std;

const int N = 1e3 + 10;

int n, m;
int g[N][N];
int f[N][N];

int main(){
    //读入
    cin >> n >> m;
    for (int i = 1 ; i <= n ; i ++) {
        for (int j = 1 ; j <= m ; j ++) {
            cin >> g[i][j];
        }
    }
    
    //DP
    int res = 0;
    for (int i = 1 ; i <= n ; i ++) {
        for (int j = 1 ; j <= m ; j ++) {
            if (g[i][j]) {
                f[i][j] = min(f[i - 1][j], min(f[i][j - 1], f[i - 1][j - 1])) + 1;
                res = max(res, f[i][j]);
            }
        }
    }
    cout << res * res << endl;
    
    return 0;
}
```

# AcWing 620. 安全区

![image-20210730124953963](https://gitee.com/xddadd/cloud-image/raw/master/master/20210730125001.png)

![image-20210730125010949](https://gitee.com/xddadd/cloud-image/raw/master/master/20210730125011.png)

## 最大正方形DP

> 考虑DP
>
> `f[i][j]`表示以`g[i][j]`为右下角的不包含怪兽的最大正方形的  个数。个数等价于边长，则该问题**转换成了最大正方形**的问题。[最大正方形题解](https://www.acwing.com/solution/content/59412/)

状态表示：`f[i][j]`表示以`g[i][j]`为右下角的不包含怪兽的最大正方形的  边长

状态转移方程：`f[i][j - 1], f[i - 1][j], f[i - 1][j - 1]`三个取min后加1

时间复杂度$O(N^2)$，空间复杂度$O(N^2)$

## AC代码

```cpp
#include <iostream>
#include <cstring>
using namespace std;

const int N = 3e3 + 10;

int g[N][N];
int f[N][N];
int n, m, k;
int T;

int main(){
    cin >> T;
    for (int t = 1 ; t <= T ; t ++) {
        memset(g, 0, sizeof g); //初始化
        memset(f, 0, sizeof g); //初始化
        //读入
        cin >> n >> m >> k;
        for (int i = 0 ; i < k ; i ++) {
            int a, b;
            cin >> a >> b;
            g[a + 1][b + 1] = 1;
        }
        
        //DP
        long long res = 0;
        for (int i = 1 ; i <= n ; i ++) {
            for (int j = 1 ; j <= m ; j ++) {
                if (!g[i][j]) {
                    f[i][j]= min(f[i - 1][j], min(f[i][j - 1], f[i - 1][j - 1])) + 1;
                }
                res += f[i][j];
            }
        }
        //输出
        printf("Case #%d: %lld\n", t, res);
    }
    return 0;
}
```

# AcWing 1051. 最大的和

![image-20210730175855433](https://gitee.com/xddadd/cloud-image/raw/master/master/20210730175855.png)

## DP+前后缀枚举

题意：给定一个数组，求连续两段子数组的和的最大值，可以相邻，也可以不相邻。

> ![](https://gitee.com/xddadd/cloud-image/raw/master/master/20210730181300.png)
>
> 答案大致如图，如何求数组中连续一段子数组的和的最大值呢？DP，[连续子数组的最大和](https://www.acwing.com/problem/content/50/)。
>
> 如果要求两段呢？利用枚举两端的中点，前后缀分解下来做。
>
> `f[i]`表示`a[1-i]`中最大的连续一段子数组的和。
>
> `g[i]`表示`a[i-(n-1)]`中最大连续一段子数组的和。
>
> 这样我们可以枚举两段的中点取max即可求出。**类似的题是接雨水，也是前后缀分解后枚举**

1. 求`f`
2. 求`g`
3. 枚举两段的分界点

时间复杂度$O(N)$，空间复杂度$O(N)$

## AC代码

```cpp
#include <iostream>
#include <cstring>
using namespace std;

const int N = 5e4 + 10;

int T;
int f[N], g[N];
int n;
int a[N];


int main() {
    cin >> T;
    while (T --) {
        //初始化
        memset(f, -0x3f, sizeof f);
        memset(g, -0x3f, sizeof f);
        cin >> n;
        for (int i = 0 ; i < n ; i ++) cin >> a[i];
        //从左往右求
        int s = a[0];
        f[0] = a[0];
        for (int i = 1 ; i < n ; i ++) {
            s = max(s, 0) + a[i];
            f[i] = max(f[i - 1], s);
        }
        //从右往左求
        s = a[n - 1];
        g[n - 1] = a[n - 1];
        for (int i = n - 2 ; i >= 0 ; i --) {
            s = max(s, 0) + a[i];
            g[i] = max(g[i + 1], s);
        }
        //枚举左边和右边
        int res = -1e9;
        for (int i = 0 ; i < n - 1 ; i ++) {
            res = max(res, f[i] + g[i + 1]);
        }
        cout << res << endl;
        
    }
    return 0;
}
```


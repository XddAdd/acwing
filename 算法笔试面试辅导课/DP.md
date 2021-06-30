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


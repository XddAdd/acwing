# 一般DP

# 3499.序列最大收益

![image-20210525233827981](https://gitee.com/xddadd/cloud-image/raw/master/image-20210525233827981.png)

![image-20210525233836635](https://gitee.com/xddadd/cloud-image/raw/master/image-20210525233836635.png)

## 动态规划

![image-20210526001004647](https://gitee.com/xddadd/cloud-image/raw/master/image-20210526001004647.png)

时间复杂度$O(N^3)$，空间复杂度$O(N^2)$

## AC代码

```cpp
#include <iostream>
#include <cstring>
using namespace std;

const int N = 210;

int n, m, k;
int w[N][N];
int a[N];
int f[N][N];

int main(){
    //读入
    cin >> m >> k >> n;
    for (int i = 1 ; i <= n ; i ++) cin >> a[i];
    for (int i = 1 ; i <= m ; i ++) 
        for (int j = 1 ; j <= m ; j ++) 
            cin >> w[i][j];
    
    //DP
    memset(f, -0x3f, sizeof f);
    f[1][0] = 0;
    for (int i = 1 ; i <= n ; i ++) {
        for (int j = 0 ; j <= k ; j ++) {
            for (int u = 0 ; u < i; u ++ ){
                if (j - (i - u - 1) >= 0)
                    f[i][j] = max(f[i][j], f[u][j - (i - u - 1)] + w[a[u]][a[i]]);
            }
        }
    }
    
    //求答案
    int res = 0;
    for (int i = 0 ; i <= k ; i ++)
        res = max(res, f[n][i]);
    
    cout << res << endl;
    
    return 0;
    
}
```


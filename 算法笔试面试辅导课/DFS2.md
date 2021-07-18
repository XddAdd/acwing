# DFS2

# AcWing 1491. 圆桌座位

![image-20210703144533278](https://gitee.com/xddadd/cloud-image/raw/master/image-20210703144533278.png)

![image-20210703144551490](https://gitee.com/xddadd/cloud-image/raw/master/image-20210703144551490.png)

## DFS搜索

题意：给定n个人，m对关系，n个人围成一圈坐，不能将两个是朋友关系的相邻。输出有多少种坐的方案。若通过旋转两个方案相同，则认为是同种方案。

因为数据量比较小，所以可以使用DFS搜索来枚举所有方案。我们可以枚举圆桌的每一位放谁

，要满足这次枚举的人不能和上一个枚举过的人是朋友关系。

> 如何枚举使得方案不重复枚举呢？
>
> 我们将桌子的任一位置固定放上一个人，这样就能达到方案去重的效果了。

1. 固定1号位子放1
2. dfs枚举n个位置每一个位置放谁
3. 枚举每一个位置放谁要满足：没被放过，放的人不能和上一个人是朋友关系

时间复杂度指数级别。

## AC代码

```cpp
#include <iostream>
using namespace std;

const int N = 13;

int n, m;
int path[N];
bool g[N][N];
bool st[N];
int res;

//枚举当前位置放什么
void dfs(int u) {
    if (u > n) {
        //判断头尾是否是朋友
        int a = 1, b = path[n];
        if (!g[a][b]) {
            res ++;
        }
        return ;
    }
    
    //从2开始枚举放什么
    for (int i = 2 ; i <= n ; i ++) {
        //没放过,并且和上一个不是朋友
        if (st[i]) continue;
        int l = path[u - 1];
        if (!g[l][i]) {
            path[u] = i;
            st[i] = true;
            dfs(u + 1);
            st[i] = false;
        }
    }
}

int main() {
    //读入
    cin >> n >> m;
    for (int i = 0 ; i < m ; i ++) {
        int a, b;
        cin >> a >> b;
        g[a][b] = true, g[b][a] = true;
    }
    
    //DFS
    path[1] = 1;//固定1号位置放1
    st[1] = true;
    dfs(2);
    
    cout << res << endl;
    
    return 0;
}
```


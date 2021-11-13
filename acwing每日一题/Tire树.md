# Tire树

# 3485.最大异或和

![image-20210525221428901](https://gitee.com/xddadd/cloud-image/raw/master/image-20210525221428901.png)

## tire树+前缀异或和+滑动窗口

> 最重要是先求得前缀异或和。
>
> 长度不超过M的连续子数组中，求得异或和最大值。
>
> 求[l, r]之间的异或和：s[r] ^ s[l - 1]
>
> ![image-20210525223556098](https://gitee.com/xddadd/cloud-image/raw/master/image-20210525223556098.png)
>
> 限制了长度不超过M，则图中等式的右边的元素个数不超过M，即等式左边r - l也不能能超过M。
>
> 即维护连续的M个数区间，借助tire树，求得M个数之间最大的两个数的最大异或和。



1. 先求前缀异或和
2. 滑动窗口，对于前缀异或和数组，保持[l,r]之间的数有M个，求得不超过M个数的两个数的最大异或和，更新答案。

时间复杂度$O(NlogN)$，空间复杂度$O(N)$

## AC代码

```cpp
#include <iostream>
using namespace std;

const int N = 1e5 + 10, M = N * 31;

int n, m, idx;
int son[M][32];
int s[N];
int cnt[M];


void insert(int x, int sta) {
    int p = 0;
    for (int i = 30 ; i >= 0 ; i --) {
        int u = x >> i & 1;
        if (!son[p][u]) son[p][u] = ++ idx;
        p = son[p][u];
        cnt[p] += sta;
    }
}

int query(int x) {
    int p = 0;
    int res = 0;
    for (int i = 30 ; i >= 0 ; i --) {
        int u = x >> i & 1;
        if (cnt[son[p][!u]]) p = son[p][!u], res = res * 2 + 1;
        else p = son[p][u], res = res * 2;
    }
    return res;
}



int main(){
    //读入
    cin >> n >> m;
    //初始化异或前缀和
    for (int i = 1 ; i <= n ; i ++) {
        int x;
        cin >> x;
        s[i] = s[i - 1] ^ x;
    }
    
    //滑动窗口求异或最大值
    int res = 0;
    m ++;//因为[l, r]之间异或和是s[r] ^ s[l - 1]
    for (int i = 1 ; i <= n ; i ++) {
        if (i > m ) insert(s[i - m], -1);
        insert(s[i], 1);
        res = max(res, query(s[i]));
    } 
    
    cout << res << endl;
    
    return 0;
    
}
```


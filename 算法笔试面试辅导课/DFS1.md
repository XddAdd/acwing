# 算法笔试面试辅导之DFS搜索1


# AcWing 92. 递归实现指数型枚举

## 算法1.二进制枚举

用一个int来表示这n个数每个数的状态，每个数只有选和不选两个状态，则一共有$2^n$种状态，利用二进制枚举来枚举所有状态。
时间复杂度$O(2^N)$

## AC代码
``` cpp
#include <iostream>
using namespace std;

const int N = 20;

int n;

int main(){
    cin >> n;
    //枚举所有状态
    for (int i = 0 ; i < 1 << n ; i ++) {
        for (int j = 0 ; j < n ; j ++) {
            if (i >> j & 1) {
                cout << j + 1 << " ";
            }
        }
        cout << endl;
    }
    
    return 0;
}
```

## 算法2.递归枚举
我们也可以利用递归来枚举，枚举每一位选与不选的两个状态。这需要用一个path来记录递归的路径,并且需要用一个状态数组来表示每个数的状态是选/不选。
时间复杂度$O(2^N)$
``` cpp
#include <iostream>
using namespace std;

const int N = 20;

int path[N];
bool st[N];//判断第i个数选/不选两种状态
int n;

//u表示当前在考虑第u个数选/不选,n表示一共要考虑n个数
void dfs(int u, int n) {
    if (u > n) {
        for (int i = 1 ; i <= n ; i ++) {
            if (st[i])
                cout << path[i] << " ";
        }
        cout << endl;
        return ;
    }
    
    //不选
    st[u] = false;
    dfs(u + 1, n);
    
    //选
    st[u] = true;
    path[u] = u;
    dfs(u + 1, n);
    
}

int main(){
    cin >> n;
    
    dfs(1, n);
    
    return 0;
}
```

# AcWing 94. 递归实现排列型枚举
## 经典全排列
DFS搜索，对于每个位置，枚举能填哪些数。
因为这些数不能重复，所以当前位置能填的数只能是没被枚举过的。
$O(N!)$
## AC代码
``` cpp
#include <iostream>
using namespace std;

const int N = 12;

int n;
int st[N];
int path[N];

void dfs(int u, int n){
    if (u > n) {
        for (int i = 1 ; i <= n ; i ++) {
            cout << path[i] << " ";
        }
        cout << endl;
        return ;
    }
    //枚举每个位置能填哪些数
    for (int i = 1 ; i <= n ; i ++) {
        if (!st[i]) {
            st[i] = true;
            path[u] = i;
            dfs(u + 1, n);
            st[i] = false;
        }
    }
}

int main(){
    cin >> n;
    dfs(1, n);
    return 0;
}
```
# AcWing 1537. 递归实现排列类型枚举 II 

## 全排列重复元素
> 什么情况下会出现重复的排列呢？
> ![dfs1.png](https://cdn.acwing.com/media/article/image/2021/06/30/50620_a3f00d3ad9-dfs1.png) 
> 如上图：上面两行蓝色表示重复元素，红色表示枚举的先后顺序，下面一行是枚举时候的路径。
> 因为蓝色都是重复元素，若枚举顺序如上面图的时候，下面的路径就会重复。

综上：我们枚举的时候要保证重复元素的枚举的相对顺序，如果枚举当前元素，一定要保证前一个重复的元素一定被枚举过了，则当前元素才能被枚举。

时间复杂度$O(N!)$

## AC代码
``` cpp
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 12;

int st[N];
int path[N];
int a[N];
int n;

void dfs(int u,int n){
    if (u == n){
        for (int i = 0 ; i < n ; i ++) {
            cout << path[i] << " ";
        }
        cout << endl;
        return ;
    }
    
    for (int i = 0 ; i < n ; i ++) {
        //若相同元素，要保证前一个元素选过了，当前元素才能选
        if (i && a[i - 1] == a[i] && !st[i - 1]) continue;
        if (!st[i]) {
            path[u] = a[i];
            st[i] = true;
            dfs(u + 1, n);
            st[i] = false;
        }
    }
}

int main(){
    cin >> n;
    for (int i = 0 ; i < n ; i ++) cin >> a[i];
    
    //排序
    sort(a, a + n);
    
    dfs(0, n);
    return 0;
}
```

# AcWing 1572. 递归实现指数型枚举 II  

## 指数型枚举重复元素
没有重复元素的指数型枚举是枚举每个数选/不选，有了重复元素，我们要对于相同的元素枚举选0个,选1个....等。
1. 排序
2. 从0开始枚举每个位置选什么
3. 对于每个选不选，我们枚举相同元素选0个,选1个.....
## AC代码

``` cpp
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 16;

int a[N];
int n;
int path[N];
bool st[N];

void dfs(int u, int n){
    if (u == n) {
        for (int i = 0 ; i < n ; i ++){
            if (st[i])
                cout << a[i] << " ";
        }
        cout << endl;
        return ;
    }
    
    //枚举当前相同的元素选几个
    int k = u;
    while (k < n && a[k] == a[u]) k ++;

    //选0个,1个...k - u个
    for (int i = u ; i < k + 1 ; i ++){
        dfs(k, n);
        st[i] = true;
    }
    
    //回溯
    for (int i = u ; i < k + 1 ; i ++){
        st[i] = false;
    }
}

int main(){
    //读入
    cin >> n;
    for (int i = 0 ; i < n ; i ++) cin >> a[i];
    //排序
    sort(a, a + n);
    
    dfs(0, n);
    
    return 0;
}
```
# AcWing 93. 递归实现组合型枚举

## 组合型枚举

组合型枚举是n个数中选m个数。
因为1 2和2 1这种算是重复的，所以我们枚举的时候传入start，从start开始搜索，保证相对顺序。
1. 枚举这m个位置每个位置选哪个数
2. 对于每个位置，枚举选哪个数，传入参数，从start开始搜索
3. 递归结束的条件是选了m个
4. 可行性剪枝：若当前选了u个数，后面能选的数(n - start + 1)不足m个，则这个分支肯定选不够m个

## AC代码
``` cpp
#include <iostream>
using namespace std;

const int N = 25;

int n, m;
int path[N];

void dfs(int u, int start){
    //可行性剪枝
    if (u + (n - start + 1) < m) return;
    if (u > m) {
        for (int i = 1 ; i <= m ; i ++)
            cout << path[i] << " ";
        cout << endl;
        return ;
    }
    
    //从start开始枚举选哪些数
    for (int i = start ; i <= n ; i ++){
        path[u] = i;
        dfs(u + 1, i + 1);
    }
}

int main(){
    cin >> n >> m;
    dfs(1, 1);
    return 0;
}
```

# AcWing 1573. 递归实现组合型枚举 II

## 组合型枚举重复元素

整体还是按照组合型没有重复元素来枚举。
注意：我们枚举的时候要保证重复元素的枚举的相对顺序，如果枚举当前元素，一定要保证前一个重复的元素一定被枚举过了，则当前元素才能被枚举。这样就不会出现重复的方案。


## AC代码
``` cpp
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 25;

int a[N];
int n, m;
bool st[N];
int path[N];

//u表示当前枚举第u个位置填什么数, start表示从哪个数开始填
void dfs(int u, int start) {
    //已经填了m个数
    if (u == m) {
        for (int i = 0 ; i < m ; i ++) {
            cout << path[i] << " ";
        }
        cout << endl;
        return ;
    }
    
    //从start开始枚举
    for (int i = start ; i < n ; i ++){
        //相同元素，搜索要保证相对搜索顺序
        if (i && a[i] == a[i - 1] && !st[i - 1]) continue;
        path[u] = a[i];
        st[i] = true;
        dfs(u + 1, i + 1);
        st[i] = false;
    }
    
}

int main(){
    //读入
    cin >> n >> m;
    for (int i = 0 ; i < n ; i ++) cin >> a[i];
    sort(a, a + n);
    
    dfs(0, 0);
    return 0;
}
```
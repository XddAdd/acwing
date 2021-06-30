# 数学与简单DP

# 买不到的数目

![image-20210207155553833](https://gitee.com/xddadd/cloud-image/raw/master/master/20210207155554.png)

## 数学(经典题)

定理：如果a,b互质，不能由a和b凑出来的最大的数是`(a - 1)(b - 1) - 1`

时间复杂度O(1)

## AC代码

```cpp
#include <iostream>
using namespace std;

int a , b , res;

int main(){
    cin >> a >> b;
    cout << (a - 1) * (b - 1) - 1;
    return 0;
}
```

# 蚂蚁感冒

![image-20210207182344111](https://gitee.com/xddadd/cloud-image/raw/master/master/20210207182344.png)

![image-20210207182352391](https://gitee.com/xddadd/cloud-image/raw/master/master/20210207182352.png)

## 脑筋急转弯

![image-20210207184106756](https://gitee.com/xddadd/cloud-image/raw/master/master/20210207184106.png)

时间复杂度O(N)

## AC代码

```cpp
#include <iostream>
using namespace std;

const int N = 55;

int x[N];
int n;

int main(){
    //读入
    cin >> n;
    for (int i = 0 ; i < n ; i++) cin >> x[i];
    
    //计算左边向右的和右边向左的
    int l = 0, r = 0;
    for (int i = 1 ; i < n ; i ++){
        if (abs(x[i]) < abs(x[0]) && x[i] > 0) l ++;
        else if (abs(x[i]) > abs(x[0]) && x[i] < 0) r ++;
    }
    
    //判断感冒蚂蚁的方向讨论
    int res = 0;
    if (x[0] > 0){
        if (r) res = r + l + 1;
        else res = 1;
    } else {
        if (l) res = r + l + 1;
        else res = 1;
    }
    
    cout << res;
    return 0;
}
```

# 饮料换购

![image-20210207191059490](https://gitee.com/xddadd/cloud-image/raw/master/master/20210207191059.png)

## 模拟+找规律

模拟找规律

## AC代码

```cpp
#include <iostream>
using namespace std;

int n;

int main(){
    cin >> n;
    
    int res = n;
    while (n >= 3){
        res += n / 3;
        n = n / 3 + n % 3;
    }

    cout << res;
    return 0;
}
```

# 地宫取宝

![image-20210209144405717](https://gitee.com/xddadd/cloud-image/raw/master/master/20210209144412.png)

![](https://gitee.com/xddadd/cloud-image/raw/master/master/20210209144412.png)

## 最长上升子序列+数字三角形

![image-20210209155043862](https://gitee.com/xddadd/cloud-image/raw/master/master/20210209155043.png)

时间复杂度O(NMKCC),空间复杂度O(NMKC)。

## AC代码

```cpp
#include <iostream>
using namespace std;

const int N = 55, K = 15, C = 15, P = 1000000007;

int n, m , k;
int w[N][N], f[N][N][K][C];


int main(){
    //读入
    cin >> n >> m >> k;
    for (int i = 1 ; i <= n ; i ++)
        for (int j = 1 ; j <= m ; j ++){
            cin >> w[i][j];
            w[i][j] ++;
        }


    //初始状态
    f[1][1][1][w[1][1]] = 1;
    f[1][1][0][0] = 1;
    
    //DP
    for (int i = 1 ; i <= n ; i ++){
        for (int j = 1 ; j <= m ; j ++){
            if (i == 1 && j == 1) continue;
            for (int u = 0 ; u <= k ; u ++){
                for (int c = 0 ; c <= 13 ; c ++){
                    int &t = f[i][j][u][c];
                    //不取
                    t = (t + f[i - 1][j][u][c]) % P;
                    t = (t + f[i][j - 1][u][c]) % P;
                    if (u > 0 && c == w[i][j]){
                        //取,枚举接到比自己小的后面
                        for (int v = 0 ; v < c ; v ++){
                            t = (t + f[i - 1][j][u - 1][v]) % P;
                            t = (t + f[i][j - 1][u - 1][v]) % P;
                        }
                        
                    }
                }
            }
        }
    }
    
    //输出答案
    int res = 0;
    for (int i = 0; i <= 13; i ++ ) res = (res + f[n][m][k][i]) % P;
    
    cout << res;
    
    return 0;
            
}
```

# 枚举、模拟和排序

# 连号区间数

![image-20210210160201284](https://gitee.com/xddadd/cloud-image/raw/master/master/20210210160208.png)

![image-20210210160228054](https://gitee.com/xddadd/cloud-image/raw/master/master/20210210160228.png)

## 枚举

我们枚举每个区间的左端点和右端点，对于一段区间来说，若区间的max - min == j - i，则说明该区间是连号区间

时间复杂度O(N^2^)

## AC代码

```cpp
#include <iostream>
using namespace std;

const int N = 1e4 + 10;

int n;
int a[N];

int main(){
    cin >> n;
    for (int i = 0 ; i < n ; i ++) cin >> a[i];
    
    int res = 0;
    for (int i = 0 ; i < n ; i ++){
        int mi = N + 1;
        int ma = 0;
        for (int j = i ; j < n ; j ++){
            mi = min(mi, a[j]);
            ma = max(ma, a[j]);
            if (ma - mi == j - i){
                res ++;
            }
        }
    }
    
    cout << res;
    return 0;
}
```

# 递增三元组

![image-20210210160412304](https://gitee.com/xddadd/cloud-image/raw/master/master/20210210160412.png)

![image-20210210160434232](https://gitee.com/xddadd/cloud-image/raw/master/master/20210210160434.png)

## 枚举 +二分/排序/前缀和

我们需要找到`Ai < Bj < Ck`，满足这样所有组合数。由N = 1e5,我们的算法时间复杂度肯定是O(N)/O(NlogN)。

如何枚举呢？

我们如果枚举Bj的话，我们只需要在A数组中找到满足<Bj有多少个数，C数组中有多少个>Bj的数，由乘法原理，可得Bj时方案数是这两个数相乘。

我们根据如何枚举可以发现需要查找，这样很快能想到排序+二分。

二分思路：A、C数组排序,枚举B数组的每个数，二分查找A数组和C数组即可。

二分时间复杂度O(NlogN),空间O(logN)，空间可以看成排序的空间

## 二分AC代码

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

typedef long long LL;
const int N = 1e5 + 10, M = 1e5;

int n;
int a[N], b[N], c[N];

//A数组中找到第一个>=x的数
int findA(int x){
    if (x > a[n]) return n;//特判所有数都比x小
    int l = 1, r = n;
    while (l < r){
        int mid = l + r >> 1;
        if (a[mid] >= x) r = mid;
        else l = mid + 1;
    }
    return r - 1;
}

//C数组中找到第一个<=x的数
int findC(int x){
    if (x < c[1]) return n;//特判所有数都比x大
    int l = 1, r = n;
    while (l < r){
        int mid = l + r + 1 >> 1;
        if (c[mid] <= x) l = mid;
        else r = mid - 1;
    }
    return n - r;
}


int main(){
    //读入
    cin >> n;
    for (int i = 1 ; i <= n ; i ++) cin >> a[i];
    for (int i = 1 ; i <= n ; i ++) cin >> b[i];
    for (int i = 1 ; i <= n ; i ++) cin >> c[i];
    
    //排序
    sort(a + 1, a + n + 1);
    sort(c + 1, c + n + 1);
    
    //枚举b[j]
    LL res = 0;
    for (int j = 1 ; j <= n ; j ++){
        int cntA = findA(b[j]);
        int cntC = findC(b[j]);
        res += (LL) cntA * cntC;
    }
    
    cout << res;
    return 0;
}
```

## 前缀和

由于我们已经只需要枚举Bj了，只需要在A数组中找到满足<Bj有多少个数，C数组中有多少个>Bj的数。我们利用前缀和思想：

cntA[i]表示A数组中<=i的个数，cntC[i]表示C数组中>=i的个数。

我们需要事先统计A数组和C数组中每个数出现的次数后再进行前缀和即可。

前缀和：时间复杂度O(N)，空间复杂度O(N)

```cpp
#include <iostream>
using namespace std;

typedef long long LL;
const int N = 1e5 + 10, M = 1e5;

int n;
int a[N], b[N], c[N];
int cntA[N], cntC[N];


int main(){
    //读入,并统计出现次数
    cin >> n;
    for (int i = 1 ; i <= n ; i ++) cin >> a[i], cntA[a[i]] ++;
    for (int i = 1 ; i <= n ; i ++) cin >> b[i];
    for (int i = 1 ; i <= n ; i ++) cin >> c[i], cntC[c[i]] ++;
    
    //cntA[i]表示A数组中比i小于等于的个数
    for (int i = 1 ; i <= M ; i ++) cntA[i] += cntA[i - 1];
    //cntC[i]表示C数组中比i大于等于的个数
    for (int i = M ; i >= 0 ; i --) cntC[i] += cntC[i + 1];
    
    //枚举b[j]
    LL res = 0;
    for (int j = 1 ; j <= n ; j ++){
        if (b[j] == 0 || b[j] == M) continue;
        res += (LL) cntC[b[j] + 1] *  cntA[b[j] - 1];

    }
    cout << res;
    return 0;
}
```

# 回文日期

![image-20210212113108224](https://gitee.com/xddadd/cloud-image/raw/master/master/20210212113115.png)

![image-20210212113126242](https://gitee.com/xddadd/cloud-image/raw/master/master/20210212113126.png)

## 枚举年份

第一时间想肯定是从起始日期枚举到终点日期，判断每一个日期是否为回文串，但是这样时间复杂度有点高，并且也不好枚举。

我们观察发现，给定的日期都是8位数，如果想要构成回文串，一定是前四位和后四位对称的，即给定一个日期，我们根据年份，可以确定该年的回文日期最多有一个，这样我们只需要枚举所有年份即可。

时间复杂度O(YEAR)

## AC代码

```cpp
#include <iostream>
using namespace std;

int st, ed;
int mon[] = {0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};


//根据日期获取年份
int get_year(int date){
    int y = 0;
    for (int i = 0 ; i < 4 ; i ++) date /= 10;
    y = date % 10000;
    return y;
}

//根据年份判断是否存在回文串
int get_pal(int y){
    //获取月和日
    int m = 0, d = 0, t = y;
    for (int i = 0 ; i < 2 ; i ++){
        m = m * 10 + y % 10;
        y /= 10;
    }
    for (int i = 0 ; i < 2 ; i ++){
        d = d * 10 + y % 10;
        y /= 10;
    }
    //验证月
    if (m < 1 || m > 12) return 0;
    //验证日
    if (m != 2){
        if (d == 0 || d > mon[m])
            return 0;
    }else {
        if ((t % 4 == 0 && t % 100 != 0) || t  % 400 == 0) {
            if (d == 0 || d > 29) return 0;
        }
        else {
            if (d == 0 || d > 28) return 0;
        }
    }
    int res = t * 10000 + m * 100 + d;
    return res;
}

int main(){
    cin >> st >> ed;
    
    //枚举起始年到终止年
    int res = 0;
    for (int i = get_year(st) ; i <= get_year(ed) ; i ++){
        int t = get_pal(i);
        if (t && t >= st && t <= ed)
            res ++;
    }
    
    cout << res;
    return 0;
}
```

# 移动距离

![image-20210212145310906](https://gitee.com/xddadd/cloud-image/raw/master/master/20210212145311.png)

## 找规律模拟

首先明确我们要求的是曼哈顿距离：`|x1 - x2| + |y1 - y2|`

其次我们发现，如果我们能够确定对应楼号在矩阵的坐标，我们就可以求得两点间的距离了。所以这个问题的核心在于根据给定的编号求得在矩阵中对应的下标。

![image-20210212145823743](https://gitee.com/xddadd/cloud-image/raw/master/master/20210212145823.png)

例如w = 3，n = 8, m = 2。

1.我们将楼号从0开始存，则对应n和m都减1，矩阵从00开始

2.求得n和m对应的存的位置。

3.计算曼哈顿距离

对于给定的楼号，映射后，计算的下标：

`x = n / w`, `y = n % w`，注意y需要特判在奇数行或者偶数行

时间复杂度O(1)

## AC代码

```cpp
#include <iostream>
using namespace std;

int w, n , m;

int main(){
    cin >> w >> m >> n;
    m -- , n --;
    
    //映射矩阵距离 
    int x1, x2, y1, y2;
    x1 = n / w, x2 = m / w;
    if (x1 & 1) y1 = w - 1 - n % w;
    else y1 = n % w;
    if (x2 & 1) y2 = w - 1 - m % w;
    else y2 = m % w;
    
    //计算曼哈顿距离
    int res = abs(x1 - x2) + abs(y1 - y2);

    cout << res << endl;
    return 0;
}
```

# 日期问题

![image-20210212154645016](https://gitee.com/xddadd/cloud-image/raw/master/master/20210212154645.png)

## 枚举

我们用数字表示日期，我们只需要枚举起点到终点每一天。并且校验是否合法，合法的话，还需要判断是否可能是a,b,c组合的格式。

1.枚举起点到终点每一天

2.校验是否合法

- 合法的话，继续判断是否是abc组合的日期格式

时间复杂度O(1e7)

## AC

代码

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

const int st = 19600101, ed = 20591231;
int mon[] = {0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
int a , b, c;


//校验日期是否合法
bool valied(int y, int m, int d){
    //校验月
    if (m < 1 || m > 12) return false;
    //校验日
    int t = mon[m];
    if (m == 2){
        if ((y % 4 == 0 && y % 100 != 0) || y % 400 == 0)
            t ++;
    }
    if (d < 1 || d > t) return false;
    return true;
}



int main(){
    scanf("%d/%d/%d", &a, &b, &c);

    //起始日期枚举到终止日期
    for (int i = st ; i <= ed ; i ++){
        int y = i / 10000, m = i  / 100 % 100, d = i % 100;
        if (valied(y, m, d)){//合法
            if (y % 100 == a && m == b && d == c ||  //年月日
                m == a && d == b && y % 100 == c ||  //月日年
                d == a && m == b && y % 100 == c ){  //日月年
                //输出
                printf("%d-%02d-%02d\n", y, m, d);
            }
        }
    }
    
    return 0;
    
}

```


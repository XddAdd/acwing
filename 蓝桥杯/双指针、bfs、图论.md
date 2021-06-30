# 双指针、bfs、图论

# 日志统计

![image-20210225003149822](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225003157.png)

![image-20210225003244947](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225003245.png)

## 滑动窗口

![image-20210225003634347](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225003634.png)

时间复杂度O(NlogN),空间O(N)

## AC代码

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

typedef pair<int,int> PAIR;
const int N = 1e5 + 10;

int n, k, d;
PAIR logs[N];
int cnt[N];
bool st[N];


int main(){
    //读入
    cin >> n >> d >> k;
    for (int i = 0 ; i < n ; i ++)
        cin >> logs[i].first >> logs[i].second;
    
    //排序
    sort(logs, logs + n);
    
    //滑动窗口
    for (int i = 0, j = 0 ; i < n ; i ++){
        int ts = logs[i].first, id = logs[i].second;
        
        cnt[id] ++;
        //[j,i]之间<d
        while (logs[i].first - logs[j].first >= d){
            cnt[logs[j].second] --;
            j ++;
        }
        
        //判断是否热搜
        if (cnt[id] >= k) st[id] = true;
    }
    
    //输出
    for (int i = 0 ; i < N - 5 ; i ++)
        if (st[i])
            cout << i << endl;
            
    return 0;
}
```

# 献给阿尔吉侬的花束

![image-20210225003916367](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225003916.png)

![image-20210225003925164](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225003925.png)

## bfs求最短路

使用bfs，计算起点到终点的距离即可。

时间复杂度O(N^2^)，空间复杂度O(N^2^)

## AC代码

```cpp
#include <iostream>
#include <cstring>
#include <queue>
using namespace std;

typedef pair<int,int> PAIR;
const int N = 210;

int t, n, m;
char g[N][N];
int st[N][N];

int bfs(PAIR beg, PAIR ed){
    memset(st, 0 , sizeof st);
    queue<PAIR> q;
    q.push(beg);
    
    while (q.size()){
        auto t = q.front();
        q.pop();
        int x = t.first, y = t.second;
        //上下左右四个方向
        int dx[] = {-1, 1, 0 , 0}, dy[] = {0, 0, 1, -1};
        for (int i = 0 ; i < 4 ; i ++){
            auto a = x + dx[i], b = y + dy[i];
            //越界,不能走,遍历过
            if (a < 0 || a >= n || b < 0 || b >= m || st[a][b] || g[a][b] == '#')
                continue;
            st[a][b] = st[x][y] + 1;
            if (a == ed.first && b == ed.second) return st[a][b];
            q.push({a, b});
        }
    }
    return -1;
}

int main(){
    cin >> t;
    while (t --){
        //读入
        PAIR beg, ed;//起点和终点
        cin >> n >> m;
        for (int i = 0 ; i < n ; i ++)
            for (int j = 0 ; j < m ; j ++){
                cin >> g[i][j];
                if (g[i][j] == 'S') beg = {i ,j};
                else if (g[i][j] == 'E') ed = {i, j};
            }
        
        //bfs
        int step = bfs(beg, ed);
        if (step != -1) cout << step << endl;
        else cout << "oop!\n";
    }
    
    return 0;
}
```

# 红与黑

![image-20210225005640315](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225005640.png)

![image-20210225005651207](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225005651.png)

## bfs求连通块的数量

从起点遍历，能遍历到的块，则加1

时间复杂度O(N^2^)

## AC代码

```cpp
#include <iostream>
#include <queue>
#include <cstring>
using namespace std;

typedef pair<int,int> PAIR;
const int N = 50;

int dx[4] = {0, 0, -1, 1}, dy[4] = {1, -1, 0, 0};
int n, m;
char g[N][N];
bool st[N][N];
PAIR star;

int bfs(){
    //初始化
    int res = 1;
    queue<PAIR> q;
    q.push(star);
    
    while (q.size()){
        auto t = q.front();
        q.pop();
        int x = t.first, y = t.second;
        //遍历上下左右
        for (int i = 0 ; i < 4 ; i ++){
            int a = x + dx[i], b = y + dy[i];
            //越界，遍历过，非黑色瓷砖
            if (a < 0 || a >= n || b < 0 || b >= m) continue;
            if (st[a][b] || g[a][b] != '.') continue;
            res ++;
            st[a][b] = true;
            q.push({a, b});
        }
    }
    return res;
}

int main(){
    while (cin >> m >> n, m && n){
        memset(st, false, sizeof st);
        //读入
        for (int i = 0 ; i < n ; i ++){
            for (int j = 0 ; j < m ; j ++){
                cin >> g[i][j];
                if (g[i][j] == '@') star = {i, j}, st[i][j] = true;
            }
        }
        
        //bfs求连通块
        
        int res = bfs();
        cout << res << endl;
    }
    
    return 0;
}
```

# 交换瓶子

![image-20210225013939889](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225013939.png)

![image-20210225013946906](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225013946.png)

## 经典题(连通块数量)

1.建图，对于i来说，i位置上的数为j，则i指向j，样例建图如下：

![image-20210225014529040](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225014529.png)

时间复杂度O(N),空间复杂度O(N)

## AC代码

```cpp
#include <iostream>
using namespace std;

const int N = 1e4 + 10;

int bot[N];
int n;
bool st[N];

//标记连通块
void mark(int s){
    for (int t = s ; !st[t] ; t = bot[t]){
        st[t] = true;
    }
}

int main(){
    //读入
    cin >> n;
    for (int i = 1 ; i <= n ; i ++) cin >> bot[i];
    
    //计算连通块的数量
    int res = 0;
    for (int i = 1 ; i <= n ; i ++){
        if (st[i] == false){
            mark(i);
            res ++;
        }
    }
    
    //输出
    cout << n - res;
    return 0;
}
```

# 完全二叉树的权值

![image-20210225154550388](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225154557.png)

![image-20210225154647412](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225154647.png)

## 枚举

![image-20210225155140067](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225155140.png)

时间复杂度O(N)

## AC代码

```cpp
#include <iostream>
using namespace std;

typedef long long LL;
const int N= 1e5 + 10;

int tree[N];
int n;

int main(){
    //读入
    cin >> n;
    for (int i = 1 ; i <= n ; i ++) cin >> tree[i];
    
    //枚举每一层
    LL max_sum = -1e10;
    int res = 0;
    //枚举每一层左端点
    for (int level = 1 , left = 1 ; left <= n ; level ++, left *= 2 ){
        LL sum = 0;
        //计算左端点到该层结尾的和
        for (int i = left ; i <= n && i < left * 2 ; i ++){
            sum += tree[i];
        }
        //更新答案
        if (sum > max_sum){
            max_sum = sum;
            res = level;
        }
    }
    
    cout << res << endl;
    return 0;
}
```

# 地牢大师

![image-20210225172222958](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225172223.png)

![image-20210225172233368](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225172233.png)

## 三维BFS

三维板的bfs，套路一样

## AC代码

```cpp
#include <iostream>
#include <queue>
#include <cstring>
using namespace std;

typedef pair<int,int> PAIR;
const int N = 1e3 + 10;

int n;
char g[N][N];
bool st[N][N];
int dx[4] = {-1, 0, 1, 0};
int dy[4] = {0, 1, 0, -1};

bool bfs(PAIR star){
    queue<PAIR> q;
    q.push(star);
    st[star.first][star.second] = true;
    int total = 0, cnt = 0;
    
    while (q.size()) {
        auto t = q.front();
        q.pop();
        total ++;
        int x = t.first, y = t.second;
        bool flag = false;
        for (int i = 0 ; i < 4 ; i ++){
            int a = x + dx[i], b = y + dy[i];
            if (a < 0 || a >= n || b < 0 || b >= n) continue;
            if (st[a][b]) continue;
            if (g[a][b] == '.') {
                flag = true;
                continue;
            }
            st[a][b] = true;
            q.push({a, b});
        }
        if (flag) cnt ++;
    }
    
    if (total == cnt) return true;
    return false;
}

int main(){
    //读入
    cin >> n;
    for (int i = 0 ; i < n ; i ++)
        for (int j = 0 ; j < n ; j ++)
            cin >> g[i][j];
            
    //bfs遍历陆地，并且判断是否完全被淹没
    int res = 0;
    for (int i = 0 ; i < n ; i ++){
        for (int j = 0 ; j < n ; j ++){
            if (!st[i][j] && g[i][j] == '#'){
                if (bfs({i, j})) res ++;
            }
        }
    }

    
    //输出
    cout << res <<endl;
    return 0;
}
```

# 大臣的旅费

![image-20210225220235939](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225220236.png)

![image-20210225220306827](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225220306.png)

## 树的直径

![image-20210225220451810](https://gitee.com/xddadd/cloud-image/raw/master/master/20210225220451.png)

求最短路的时间复杂度是O(N)，可以用bfs，也可以dfs

时间复杂度O(N)

## AC代码

```cpp
#include <iostream>
#include <vector>
using namespace std;

typedef long long LL;
const int N = 1e5 + 10;

struct Edge{
    int id, w;
};

int n;
vector<Edge> h[N];
int dis[N];

void dfs(int u, int par ,int distance){
    dis[u] = distance;
    
    for (int i = 0 ; i < h[u].size() ; i ++){
        auto id = h[u][i].id, w = h[u][i].w;
        if (id != par)
            dfs(id, u, distance + w);
    }
}


int main(){
    //读入
    cin >> n;
    for (int i = 0 ; i < n - 1 ; i ++){
        int a , b , w;
        cin >> a >> b >> w;
        h[a].push_back({b, w});
        h[b].push_back({a, w});
    }
    
    //任选一点，求到其他点的距离
    int u = 1;
    dfs(1, -1, 0);
    
    //求该点到其他点最远的点
    for (int i = 1 ; i <= n ; i ++){
        if (dis[u] < dis[i])
            u = i;
    }
    
    //二次求到其他点距离
    dfs(u, -1, 0);
    
    //找出树的直径
    for (int i = 1 ; i <= n ; i ++){
        if (dis[u] < dis[i])
            u = i;
    }
    
    int s = dis[u];
    LL res = 10 * s + (s + 1ll) * s / 2;
    
    cout << res <<endl;
    return 0;
}

```


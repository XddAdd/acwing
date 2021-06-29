# 图的bfs和dfs(flood fill)

lc图的遍历题，类似flood fill算法

# 130. 被围绕的区域

![image-20210318144629865](https://gitee.com/xddadd/cloud-image/raw/master/image-20210318144629865.png)

![image-20210318144648757](https://gitee.com/xddadd/cloud-image/raw/master/image-20210318144648757.png)

## 图的遍历

首先我们能确定四周的O一定不会变成X，所以我们从四周所有的O开始遍历连通块，最终没被遍历过并且是O的点一定是被围绕的，则将这些O变为X

1.第一行和最后一行所有O遍历连通块

2.第一列和最后一列所有O遍历连通块

3.最终图中没被遍历过的O变为X

可以用dfs，也可以bfs

时间复杂度O(NM)

注意：细节多..特别是遍历四周的点O时候，N和M

## AC代码

```cpp
class Solution {
public:
    vector<vector<bool>> st;
    int n, m;
    int dx[4] = {-1, 1, 0, 0}, dy[4] = {0, 0, -1, 1};

    void dfs(vector<vector<char>>& g, int x ,int y){
        st[x][y] = true;
        //上下四个点遍历
        for (int i = 0 ; i < 4 ; i ++){
            int a = x + dx[i], b = y + dy[i];
            //越界
            if (a < 0 || a >= n || b < 0 || b >= m) continue;
            if (g[a][b] == 'X' || st[a][b]) continue;//遍历过/非O
            dfs(g, a, b);
        }
    }

    void solve(vector<vector<char>>& g) {
        if (g.empty() || g[0].empty()) return ; 
        n = g.size(), m = g[0].size();
        st = vector<vector<bool>> (n, vector<bool>(m , false));
        
        //第一列和最后一列
        for (int i = 0 ; i < n ; i ++){
            if (g[i][0] == 'O' && st[i][0] == false)
                dfs(g, i, 0);
            if (g[i][m - 1] == 'O' && st[i][m - 1] == false){
                dfs(g, i, m - 1);
            }
        }
        //第一行和最后一行
        for (int i = 0 ; i < m ; i ++){
            if (g[0][i] == 'O' && st[0][i] == false)
                dfs(g, 0, i);
            if (g[n - 1][i] == 'O' && st[n - 1][i] == false){
                dfs(g, n - 1, i);
            }
        }

        //所有没遍历过的0变为x
        for (int i = 1 ; i < n - 1 ; i ++){
            for (int j = 1 ; j < m - 1 ; j ++){
                if (g[i][j] == 'O' && st[i][j] == false)
                    g[i][j] = 'X';
            }
        }

    }
};
```

# 133.克隆图

![image-20210324155244571](https://gitee.com/xddadd/cloud-image/raw/master/image-20210324155244571.png)

![image-20210324155300313](https://gitee.com/xddadd/cloud-image/raw/master/image-20210324155300313.png)

## 图的遍历

该题类似于138题。

主要思想：给每个点创建一个小弟，即将当前点和复制的点建立关系，然后根据当前点和其他点的关系，给复制的点也建立起关系。

![image-20210324161956708](https://gitee.com/xddadd/cloud-image/raw/master/image-20210324161956708.png)

> 如何将原点和复制的点建立关系呢？
>
> 通过哈希映射

1.复制所有的点(dfs)

2.复制所有的边

时间复杂度O(N + M)

## AC代码

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> neighbors;
    Node() {
        val = 0;
        neighbors = vector<Node*>();
    }
    Node(int _val) {
        val = _val;
        neighbors = vector<Node*>();
    }
    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
*/

class Solution {
public:
    unordered_map<Node*,Node*> hash;

    Node* cloneGraph(Node* node) {
        if (!node) return node;
        dfs(node);//复制所有点

        //复制所有边
        for (auto [old, now] : hash) {
            for (auto ver : old->neighbors){
                now->neighbors.push_back(hash[ver]);
            }
        }
        return hash[node];
    }

    void dfs(Node* node){
        //对于该点，复制一个新点
        hash[node] = new Node(node->val);

        //遍历该点的邻点
        for (auto ver : node->neighbors) {
            if (!hash.count(ver))
                dfs(ver);
        }
    }
};
```

# 200.岛屿数量

![image-20210531210629656](https://gitee.com/xddadd/cloud-image/raw/master/image-20210531210629656.png)

## bfs求连通块数量

> 老裸题了，也可以看成flood fill

时间复杂度$O(N^2)$，空间复杂度$O(N^2)$

## AC代码

```cpp
class Solution {
public:
    typedef pair<int,int> PAIR;
    int n , m;
    vector<vector<bool>> st;
    int dx[4] = {0 ,0 ,-1, 1}, dy[4] = {1, -1, 0, 0};

    void bfs(int x, int y, vector<vector<char>>& g){
        queue<PAIR> q;
        q.push({x, y});
        st[x][y] = true;

        while (q.size()){
            auto t = q.front();
            q.pop();
            for (int i = 0 ; i < 4 ; i ++){
                int a = t.first + dx[i], b = t.second + dy[i];
                if (a < 0 || a >= n || b < 0 || b >= m || g[a][b] == '0' || st[a][b]) continue;
                st[a][b] = true;
                q.push({a, b});
            }
        }
    }

    int numIslands(vector<vector<char>>& g) {
        n = g.size(), m = g[0].size();
        st = vector<vector<bool>> (n , vector<bool> (m + 1, false));
        int res = 0;
        for (int i = 0 ; i < n ; i ++){
            for (int j = 0 ; j < m ; j ++){
                if (g[i][j] == '1' && st[i][j] == false){
                    res ++;
                    bfs(i , j, g);
                }
            }
        }
        return res;
    }
};
```

# 733.图像渲染

![image-20210602092833156](https://gitee.com/xddadd/cloud-image/raw/master/image-20210602092833156.png)

## flood fill

> 从起点出发，把跟起点相邻且一样的点的值变成新值即可。上下左右四个方向

时间复杂度$O(N^2)$，空间复杂度$O(N^2)$

## AC代码

```cpp
class Solution {
public:
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};

    void dfs(vector<vector<int>>& g, int x, int y, int old, int newColor) {
        g[x][y] = -1;
        for (int i = 0 ; i < 4 ; i ++) {
            int a = x + dx[i], b = y + dy[i];
            if (a < 0 || a >= g.size() || b < 0 || b >= g[0].size()) continue;
            if (g[a][b] == old)
                dfs(g, a, b, old, newColor);
        }
        g[x][y] = newColor;
    }

    vector<vector<int>> floodFill(vector<vector<int>>& g, int x, int y, int newColor) {
        int n = g.size(), m = g[0].size();
        int old = g[x][y];
        dfs(g, x, y, old, newColor);
        return g;
    }
};
```

# 463.岛屿的周长

![image-20210616164109362](C:\Users\jason\AppData\Roaming\Typora\typora-user-images\image-20210616164109362.png)

![image-20210616164124956](https://gitee.com/xddadd/cloud-image/raw/master/image-20210616164124956.png)

## BFS

​	这种题一眼看就知道一般是BFS，如何在BFS统计出每个方块的边的数目呢？因为每个方块最多有4条边，所以要考虑最终哪些边能够成为最终连通块的边呢？

> 换一个角度，能成为最终连通块的边 = 4 - 不能成为最终连通块的边。
>
> 不能成为连通块的边的特点：
>
> - 该边不能扩展到响应的邻方块

## AC代码

```cpp
class Solution {
public:
    typedef pair<int,int> PAIR;
    int dx[4] = {-1, 1, 0, 0}, dy[4] = {0, 0, -1, 1};
    int n, m;
    vector<vector<bool>> st;


    int bfs(vector<vector<int>>& g, int x, int y) {
        queue<PAIR> q;
        q.push({x, y});
        st[x][y] = true;
        int res = 0;
        while (q.size()) {
            auto t = q.front(); q.pop();
            int x = t.first, y = t.second;
            int c = 0;
            for (int i = 0 ; i < 4 ; i ++) {
                int a = x + dx[i], b = y + dy[i];
                if (a < 0 || a >= n || b < 0 || b >= m) continue;
                if (!g[a][b]) continue;
                c ++;
                if (st[a][b]) continue;
                q.push({a, b});
                st[a][b] = true;
            }
            res += 4 - c;
        }
        return res;
    }

    int islandPerimeter(vector<vector<int>>& g) {
        n = g.size(), m = g[0].size();
        st = vector<vector<bool>>(n, vector<bool>(m, false));
        int res = 0;
        for (int i = 0 ; i < n ; i ++) {
            for (int j = 0 ; j < m ; j ++) {
                if (g[i][j] && !st[i][j]) {
                    int t = bfs(g, i, j);
                    res = max(res, t);
                }
            }
        }
        return res;
    }
};
```

# 542.01矩阵

![image-20210616214048253](https://gitee.com/xddadd/cloud-image/raw/master/image-20210616214048253.png)

## 多源bfs

1. 将所有源点距离初始化0，并且加入队列
2. 普通的bfs最短路的过程

时间复杂度$O(N^2)$

## AC代码

```cpp
class Solution {
public:
    typedef pair<int,int> PAIR;
    int dx[4] = {-1, 1, 0, 0}, dy[4] = {0, 0, -1, 1};

    vector<vector<int>> updateMatrix(vector<vector<int>>& g) {
        int n = g.size(), m = g[0].size();
        vector<vector<int>> res(n, vector<int>(m, -1));
        queue<PAIR> q;
        //多源bfs,多源点距离初始0并加入队列
        for (int i = 0 ; i < n ; i ++) {
            for (int j = 0 ; j < m ; j ++) {
                if (!g[i][j]) res[i][j] = 0, q.push({i, j});
            }
        }

        //bfs
        while (q.size()) {
            auto t = q.front();
            q.pop();
            int x = t.first, y = t.second;
            for (int i = 0 ; i < 4 ; i ++) {
                int a = x + dx[i], b = y + dy[i];
                if (a < 0 || a >= n || b < 0 || b >= m) continue;
                if (!g[a][b]) continue;
                if (res[a][b] != -1) continue;
                res[a][b] = res[x][y] + 1;
                q.push({a, b});
            }
        }

        return res;


    }
};
```

# 695.岛屿的最大面积

![image-20210618184829754](https://gitee.com/xddadd/cloud-image/raw/master/image-20210618184829754.png)

## bfs连通块的数量

bfs经典应用了，遍历每个连通块的时候，维护一个连通块中方块的数量即可。

时间复杂度$O(N^2)$，空间复杂度$O(N^2)$

## AC代码

```cpp
class Solution {
public:
    typedef pair<int,int> PAIR;
    int n , m;
    vector<vector<bool>> st;
    int dx[4] = {-1, 1, 0, 0}, dy[4] = {0, 0, -1, 1};

    int bfs(vector<vector<int>>& g, int x, int y) {
        int res = 1;
        queue<PAIR> q;
        q.push({x, y});
        st[x][y] = true;
        while (q.size()) {
            auto t = q.front();
            q.pop();
            int x = t.first, y = t.second;
            for (int i = 0 ; i < 4 ; i ++) {
                int a = x + dx[i], b = y + dy[i];
                if (a < 0 || a >= n || b < 0 || b >= m) continue;
                if (!g[a][b] || st[a][b]) continue;
                q.push({a, b});
                res ++;
                st[a][b] = true;
            }
        }
        return res;
    }


    int maxAreaOfIsland(vector<vector<int>>& g) {
        n = g.size(), m = g[0].size();
        st = vector<vector<bool>>(n, vector<bool>(m, false));
        int res = 0;
        for (int i = 0 ; i < n ; i ++) {
            for (int j = 0 ; j < m ; j ++) {
                if (g[i][j] && !st[i][j]){
                    res = max(res, bfs(g, i, j));
                }
            }
        }
        return res;
    }
};
```


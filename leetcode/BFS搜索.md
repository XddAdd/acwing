# BFS搜索

# 752.打开转盘锁

![image-20210625145155228](https://gitee.com/xddadd/cloud-image/raw/master/image-20210625145155228.png)

![image-20210625145213041](https://gitee.com/xddadd/cloud-image/raw/master/image-20210625145213041.png)

## BFS最短路模型

状态之间转换的最短路模型。

> 把每个状态看成一个点，看成从某个点开始BFS搜索其他状态。
>
> 最终目标状态被转移到就是最短的操作次数。

1. 将初始状态入队
2. BFS过程

- 对于该状态来说，枚举该状态能转移到的状态
- 若转移到的状态没在黑名单，并且没被搜索到过，则转移到的状态步数=该状态步数+1，并且入队

## AC代码

```cpp
class Solution {
public:
    unordered_set<string> deads;
    unordered_map<string,int> dis;

    //s[i] + 1
    string add(string s, int i) {
        s[i] ++;
        if (s[i] > '9') s[i] = '0';
        return s;
    }    

    //s[i] - 1
    string sub(string s, int i) {
        s[i] --;
        if (s[i] < '0') s[i] = '9';
        return s;
    }

    int bfs(string start, string& end){
        queue<string> q;
        q.push(start);
        dis[start] = 0;
        //特判初始状态在黑名单
        if (deads.count(start)) 
            return -1;
        while (q.size()) {
            auto t = q.front();
            q.pop();
            if (t == end) return dis[end];
            //枚举所有能转移的状态
            for (int i = 0 ; i < 4 ; i ++) {
                string s1 = add(t, i);
                string s2 = sub(t, i);
                if (!deads.count(s1) && !dis.count(s1)){
                    q.push(s1);
                    dis[s1] = dis[t] + 1;
                }
                if (!deads.count(s2) && !dis.count(s2)){
                    q.push(s2);
                    dis[s2] = dis[t] + 1;
                }
            }
        }
        return -1;
    }

    int openLock(vector<string>& deadends, string target) {
        //黑名单插入哈希表
        for (auto& s : deadends) 
            deads.insert(s);
        //BFS
        int res = bfs("0000", target);
        return res;
    }
};
```

# 773.滑动谜题

![image-20210626004542236](https://gitee.com/xddadd/cloud-image/raw/master/image-20210626004542236.png)

![image-20210626004553592](https://gitee.com/xddadd/cloud-image/raw/master/image-20210626004553592.png)

## BFS状态最短路模型

读完题，经典的BFS状态最短路模型(基础课、提高课)。

> 把每个转移的状态看成是一个点，从某个状态开始向其他状态开始BFS最短路搜索。
>
> 初始状态距离为0，其他状态是转移到该状态的距离+1

一般这种因为使用哈希表来存储距离，而且考虑到使用队列的情况，所以一般都会将矩阵转换成字符串来进行状态的转移搜索。

## AC代码

```cpp
class Solution {
public:
    //矩阵转字符串
    string change(vector<vector<int>>& g) {
        string res;
        for (int i = 0 ; i < 2 ; i ++)
            for (int j = 0 ; j < 3 ; j ++)
                res += to_string(g[i][j]);
        return res;
    }

    //获取s能转移的所有状态
    vector<string> get(string& s) {
        //找到0
        int i = 0;
        while (i < s.size() && s[i] != '0') i ++;
        vector<string> res;
        //上
        if (i - 3 >= 0) {
            string t = s;
            swap(t[i], t[i - 3]);
            res.push_back(t);
        }
        //下
        if (i + 3 <= 5) {
            string t = s;
            swap(t[i], t[i + 3]);
            res.push_back(t);
        }
        //左
        if (i % 3 > 0) {
            string t = s;
            swap(t[i], t[i - 1]);
            res.push_back(t);
        }
        //右
        if (i != 2 && i != 5) {
            string t = s;
            swap(t[i], t[i + 1]);
            res.push_back(t);
        }
        return res;
    }

    int bfs(string start, string end) {
        queue<string> q;
        unordered_map<string, int> dis;
        q.push(start);
        dis[start] = 0;
        while (q.size()) {
            auto t = q.front();
            q.pop();
            if (t == end) return dis[t];
            vector<string> vs = get(t);
            for (auto& s : vs) {
                if (!dis.count(s)) {
                    dis[s] = dis[t] + 1;
                    q.push(s);
                }
            }
        }
        return -1;
    }

    int slidingPuzzle(vector<vector<int>>& g) {
        string start = change(g);
        //bfs
        int res = bfs(start, "123450");
        return res;
    }
};

```

# 909.蛇梯棋

![image-20210627152716270](https://gitee.com/xddadd/cloud-image/raw/master/image-20210627152716270.png)

![image-20210627152743629](https://gitee.com/xddadd/cloud-image/raw/master/image-20210627152743629.png)

## BFS最短路搜索

题意：从编号1的格子开始走，每次可以走1-6步，其中有传送门，每次传送门只能传一次。求走到编号为n*m格子最少要多少步。

1. 构造矩阵
2. bfs最短路从1编号的格子开始搜索
3. 搜索枚举走1-6步，且要特判是否是传送门，是传送门则直接可以传送到`board[x][y]`的位置

因为要快速的找到编号id在矩阵中的位置，所以需要用一个哈希表来映射

时间复杂度$O(N^2)$，空间复杂度$O(N^2)$

## AC代码

```cpp
class Solution {
public:
    typedef pair<int,int> PAIR;

    vector<vector<int>> id; //g[i][j]对应的编号
    vector<PAIR> pos; //编号对应矩阵的下标
    vector<vector<int>> dis;
    int n, m;

    int bfs(vector<vector<int>>& board) {
        queue<PAIR> q;
        q.push({n - 1, 0});
        dis[n - 1][0] = 0;
        while (q.size()) {
            auto t = q.front();
            q.pop();
            int x = t.first, y = t.second;
            int idx = id[x][y];
            if (idx == n * m) return dis[x][y];
            for (int i = idx + 1 ; i <= n * m && i <= idx + 6 ; i ++) {
                int a = pos[i].first, b = pos[i].second;
                //非传送门
                if (board[a][b] == -1) {
                    if (dis[a][b] == -1){
                        dis[a][b] = dis[x][y] + 1;
                        q.push({a, b});
                    }
                } else {//传送门
                    int r = board[a][b];
                    a = pos[r].first, b = pos[r].second;
                    if (dis[a][b] == -1){
                        dis[a][b] = dis[x][y] + 1;
                        q.push({a, b});
                    }
                }
            }
        }
        return -1;


    }

    int snakesAndLadders(vector<vector<int>>& board) {
        n = board.size(), m = board[0].size();
        id = vector<vector<int>>(n, vector<int>(m));
        pos = vector<PAIR>(n * m + 1);
        dis = vector<vector<int>>(n, vector<int>(m, -1));
        //构造矩阵
        for (int i = n - 1, s = 0, idx = 1 ; i >= 0 ; i --, s ++) {
            if (s % 2 == 0) {
                for (int j = 0 ; j < m ; j ++) id[i][j] = idx , pos[idx ++] = {i, j};
            } else {
                for (int j = m - 1 ; j >= 0 ; j --) id[i][j] = idx , pos[idx ++] = {i, j};
            }
        }

        //bfs
        int res = bfs(board);
        return res;
    }
};
```

#  815.公交路线

![image-20210628084841294](https://gitee.com/xddadd/cloud-image/raw/master/image-20210628084841294.png)

## 多源最短路+边权0t最短路

题意：给定n条公交路线，每条公交路线都是循环的，求得起点到终点站之间最少乘坐多少公交能到达。

如图：我们把**每条线路看成一个点**，看成是从**起点的所在公交路线到终点公交路线的最短路**。

![image-20210628091555771](https://gitee.com/xddadd/cloud-image/raw/master/image-20210628091555771.png)

若起点站可能被包含多个公交路线，则这多个公交路线都可以看作起点，则是一个多源点的最短路。

> 若把路线看成一个点，我们如何实现点到其他点的扩展呢？
>
> 还是需要枚举当前路线上的所有车站，且对于每个车站，还要枚举该车站能扩展到的公交路线，所以我们需要再记录一个车站到公交路线的映射，表明当前点能到哪些公交路线

优化：为了避免重复枚举车站，每次枚举车站向其他路线扩展后，将车站从映射中删除。

时间复杂度$O(NM + N^2)$，N是公交路线数量，M是车站数量

## AC代码

```cpp
class Solution {
public:
    int numBusesToDestination(vector<vector<int>>& routes, int source, int target) {
        if (source == target) return 0;//特判起点和终点相同
        int n = routes.size();
        unordered_map<int,vector<int>> p_to_route;
        vector<int> dis(n, -1);
        queue<int> q;
        //建图
        for (int i = 0 ; i < n ; i ++) {
            for (auto x : routes[i]) {
                if (x == source) { //多源
                    dis[i] = 1;
                    q.push(i);
                }
                //车站到路线映射
                p_to_route[x].push_back(i);
            }
        }

        //BFS
        while (q.size()) {
            auto t = q.front();
            q.pop();

            //枚举当前线路的车站
            for (auto x : routes[t]) {
                if (x == target) return dis[t];
                //对于每个车站，枚举能扩展到其他的路线
                for (auto rou : p_to_route[x]) {
                    if (dis[rou] == -1) {
                        dis[rou] = dis[t] + 1;
                        q.push(rou);
                    }
                }
                p_to_route.erase(x);
            }
        }

        return -1;

    }
};

```


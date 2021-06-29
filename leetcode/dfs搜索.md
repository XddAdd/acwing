# DFS搜索

lc中DFS搜索题

# 394.字符串解码

![image-20210310123935130](https://gitee.com/xddadd/cloud-image/raw/master/image-20210310123935130.png)

## DFS搜索

递归的处理字符串...

## AC代码

```cpp
class Solution {
public:
    string dfs(string& s, int& u){
        string res;
        while (u < s.size() && s[u] != ']'){
            if (s[u] >= 'a' && s[u] <= 'z' || s[u] >= 'A' && s[u] <= 'Z') res += s[u ++];
            else if (s[u] >= '0' && s[u] <= '9'){
                //计算括号内出现次数
                int k = u;
                while (s[k] >= '0' && s[k] <= '9') k ++;
                int x = stoi(s.substr(u, k - u));
                u = k + 1;
                string t = dfs(s, u);
                u ++;//过滤右括号
                while (x --) res += t;
            }
        }
        return res;
    }

    string decodeString(string s) {
        int u = 0;
        return dfs(s, u);
    }
};
```

# 22.括号生成

![image-20210311090050230](https://gitee.com/xddadd/cloud-image/raw/master/image-20210311090050230.png)

## dfs搜索

括号序列定理1：任意前缀中`(`数量 >= `)`数量

括号序列定理2：左右括号数量相等

搜索顺序是枚举每一位填什么，最终答案一定是有n个左括号和n个右括号组成。

1.填左括号: `(`的数量<n，则表示可以填左括号

2.填右括号:`)`的数量<n，并且已经填的`(`的数量>`)`的数量，则可以填右括号

时间复杂度卡特兰数

## AC代码

```cpp
class Solution {
public:
    vector<string> res;

    void dfs(int n, string path, int l, int r){
        if (path.size() == n * 2){
            res.push_back(path);
            return ;
        }
        //判断是否填左括号
        if (l < n) dfs(n, path + "(", l + 1 , r);
        //判断是否填右括号
        if (r < n && l > r) dfs(n, path + ")", l, r + 1);
    }

    vector<string> generateParenthesis(int n) {
        dfs(n, "", 0, 0);
        return res;
    }
};
```

# 17.电话号码的字母组合

![image-20210311130013574](https://gitee.com/xddadd/cloud-image/raw/master/image-20210311130013574.png)

## dfs搜索

搜索顺序是枚举每个位置上放什么

## AC代码

```cpp
class Solution {
public:
    vector<string> res;
    string m[11] = {"", "", "abc", "def", "ghi", "jkl",
                    "mno", "pqrs", "tuv", "wxyz"};

    
    void dfs(string& digits, int u, string path){
        if (u == digits.size()){
            res.push_back(path);
            return ;
        }
        for (char c : m[digits[u] - '0']){
            dfs(digits, u + 1, path + c);
        }
    }
    
    vector<string> letterCombinations(string digits) {
        if (digits.empty()) return {};
        dfs(digits, 0, "");
        return res;
    }
};
```

# 37.解数独

![image-20210311150352836](https://gitee.com/xddadd/cloud-image/raw/master/image-20210311150352836.png)

## dfs搜索

类似于N皇后的问题，搜索顺序是每一个格子，枚举每一个格子放什么。

## AC代码

```cpp
class Solution {
public:
    bool row[9][10], col[9][10], blo[4][4][10];

    bool dfs(vector<vector<char>>& g, int x, int y){
        if (y == 9) x ++, y = 0;
        if (x == 9) return true;

        if (g[x][y] >= '1' && g[x][y] <= '9') return dfs(g, x, y + 1);//已经填过数字了

        //枚举，当前格子可以填多少
        for (int i = 1 ; i <= 9 ; i ++){
            if (!row[x][i] && !col[y][i] && !blo[x / 3][y / 3][i]){
                g[x][y] = i + '0';
                row[x][i] = col[y][i] = blo[x / 3][y / 3][i] = true;
                if (dfs(g, x, y + 1)) return true;
                g[x][y] = ' ';
                row[x][i] = col[y][i] = blo[x / 3][y / 3][i] = false;
            }
        }
        return false;
    }

    void solveSudoku(vector<vector<char>>& g) {
        memset(row, false, sizeof row);
        memset(col, false, sizeof col);
        memset(blo, false, sizeof blo);
        for (int i = 0 ; i < 9 ; i ++){
            for (int j = 0 ; j < 9 ; j ++){
                int t = g[i][j] - '0';
                if (t >= 1 && t <= 9)
                    row[i][t] = col[j][t] = blo[i / 3][j / 3][t] = true;
            }
        }
        dfs(g, 0, 0);
    }
};
```

# 46.全排列

![image-20210311155911588](https://gitee.com/xddadd/cloud-image/raw/master/image-20210311155911588.png)

## dfs搜索

搜素顺序是每个每一个位置放什么数

老模板题了

## AC代码

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    vector<bool> st;

    void dfs(vector<int>& a, int u){
        if (u == a.size()) {
            res.push_back(path);
            return ;
        }

        for (int i = 0 ; i < a.size() ; i ++){
            if (!st[i]){
                st[i] = true;
                path.push_back(a[i]);
                dfs(a, u + 1);
                path.pop_back();
                st[i] = false;
            }
        }
    }

    vector<vector<int>> permute(vector<int>& nums) {
        st = vector<bool>(nums.size(), false);
        dfs(nums, 0);
        return res;
    }
};
```

# 47.全排列 ||

![image-20210311161103663](https://gitee.com/xddadd/cloud-image/raw/master/image-20210311161103663.png)

## dfs搜索

搜索顺序还是枚举每一个位置选哪个数。

该题有重复元素，我们要保证重复元素选的时候要保证相对顺序，否则结果重复。

即，对于相同的，我们一定要保证前一个相等的元素用过了，该元素才能用

即`i && s[i] == s[i - 1] && st[i - 1] == false`表示前一个元素还没用过，现在不能用

## AC代码

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    vector<bool> st;

    void dfs(vector<int>& a, int u){
        if (u == a.size()) {
            res.push_back(path);
            return ;
        }
        for (int i = 0 ; i < a.size() ; i ++){
            if (i && a[i] == a[i - 1] && st[i - 1] == false) continue;
            if (!st[i]){
                st[i] = true;
                path.push_back(a[i]);
                dfs(a, u + 1);
                path.pop_back();
                st[i] = false;
            }
        }
    }

    vector<vector<int>> permuteUnique(vector<int>& nums) {
        st = vector<bool> (nums.size() , false);
        sort(nums.begin(), nums.end());
        dfs(nums, 0);
        return res;
    }
};
```



# 39.组合总和

![image-20210311153635038](https://gitee.com/xddadd/cloud-image/raw/master/image-20210311153635038.png)

## 完全背包的dfs

不是求方案数，是输出方案，所以直接用dfs搜索即可。

搜索顺序是对于a[u]来说选0个，选1个....

## AC代码

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;

    void dfs(vector<int>& nums, int u,int target){
        if (target == 0){
            res.push_back(path);
            return ;
        }
        
        if (u == nums.size()) return ;
        if (target < 0) return ;

        for (int i = 0 ; i * nums[u] <= target ; i ++){
            dfs(nums, u + 1 , target - i * nums[u]);
            path.push_back(nums[u]);
        }

        for (int i = 0 ; i * nums[u] <= target ; i ++){
            path.pop_back();
        }
    }

    vector<vector<int>> combinationSum(vector<int>& nums, int target) {
        dfs(nums, 0, target);
        return res;
    }
};
```

# 40.组合总和||

![image-20210311162543120](https://gitee.com/xddadd/cloud-image/raw/master/image-20210311162543120.png)

## dfs搜索

和上题一样，还是枚举每个位置选多少个。

但是该题有重复元素，则是多重背包问题。

1.排序将每个相同的数放在一起

2.dfs搜索时候，枚举每个相同的数选0，1...cnt个。

## AC代码

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;

    void dfs(vector<int>& a,int u,int target){
        if (target == 0){
            res.push_back(path);
            return;
        }

        if (u == a.size()) return ;
        if (target < 0) return ;
        //计算每个相同的数有几个
        int k = u + 1;
        while (k < a.size() && a[k] == a[u]) k ++;
        int cnt = k - u;
        //枚举每个数选多少个，至多cnt个
        for (int i = 0 ; i * a[u] <= target && i <= cnt ; i ++){
            dfs(a, k, target - a[u] * i);
            path.push_back(a[u]);
        }
        //回溯
        for (int i = 0 ; i * a[u] <= target && i <= cnt ; i ++) 
            path.pop_back();
    }

    vector<vector<int>> combinationSum2(vector<int>& a, int target) {
        sort(a.begin(), a.end());
        dfs(a, 0, target);

        return res;
    }
};
```

# 51.N皇后

![image-20210311202922969](https://gitee.com/xddadd/cloud-image/raw/master/image-20210311202922969.png)

![image-20210311202934396](https://gitee.com/xddadd/cloud-image/raw/master/image-20210311202934396.png)

## dfs搜索

搜索顺序是在每一行的哪列填皇后。例如搜索到u行，我们要枚举在u行的具体哪列填上皇后，若当前列，主对角线，副对角线都没填过，则我们就可以填上皇后。

## AC代码

```cpp
class Solution {
public:
    int n;
    vector<vector<string>> ans;
    vector<string> path;
    vector<bool>  col, dg, udg;//记录当前列，主对角线，副对角线是否有皇后


    vector<vector<string>> solveNQueens(int t) {
        //初始化参数
        n = t ;
        col = vector<bool>(n, false);
        dg = udg = vector<bool>(2 * n, false);
        path = vector<string>(n, string(n, '.'));
        //枚举
        dfs(0);

        return ans;
    }

    void dfs(int u){
        //棋盘的每行都放完皇后了
        if (u == n){
            ans.push_back(path);
            return ;
        }

        for (int i = 0 ;i < n ; i++){
            //剪枝，当前列，主对角线，副对角线可以放
            if (!col[i] && !dg[u + i] && !udg[n - u + i]){
                path[u][i] = 'Q';
                col[i] = dg[u + i] = udg[n - u + i] = true;
                dfs(u + 1);
                //回溯
                col[i] = dg[u + i] = udg[n - u + i] = false;
                path[u][i] = '.';
            }
        }
    }
};
```

# 52.N皇后||

![image-20210311203345311](https://gitee.com/xddadd/cloud-image/raw/master/image-20210311203345311.png)

## dfs搜索

和51题一样，不过该题是求有多少合法的方案，即方案数

## AC代码

```cpp
class Solution {
public:
    vector<bool> col , dg , udg;

    int totalNQueens(int n) {
        //初始化
        col = vector<bool> (n , false);
        dg = udg = vector<bool> (2 * n , false);

        int res = dfs(0 , n);
        return res;
    }

    int dfs(int u ,int n ){
        //该方案可行
        if (u == n) return 1;
        int res = 0;
        //枚举每一列
        for (int i = 0 ; i < n ; i ++){
            if (!col[i] && !dg[u + i] && !udg[u - i + n]){
                col[i] = dg[u + i] = udg[u - i + n] = true;
                res += dfs(u + 1 , n);
                col[i] = dg[u + i] = udg[u - i + n] = false;
            }
        }

        return res;

    }
};
```

# 77.组合

![image-20210313140037799](https://gitee.com/xddadd/cloud-image/raw/master/image-20210313140037799.png)

## 组合枚举

和普通的暴搜不一样，例如2，1和1，2会看成一个方案，所以我们枚举需要保证一个顺序，从小到大枚举。

搜索顺序是k个位置分别填多少，每个搜索需要保证顺序，所以需要传一个start参数，即从多少开始搜索。

## AC代码

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;

    void dfs(int n , int k, int st){
        if (!k) {
            res.push_back(path);
            return ;
        }
        for (int i = st ; i <= n ; i ++){
            path.push_back(i);
            dfs(n, k - 1, i + 1);
            path.pop_back();
        }
    }
    
    vector<vector<int>> combine(int n, int k) {
        dfs(n, k, 1);
        return res;
    }
};
```

# 78.子集

![image-20210315000123460](https://gitee.com/xddadd/cloud-image/raw/master/image-20210315000123460.png)

## dfs搜索

枚举每个数，选或者不选

记得回溯

## AC代码

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;

    void dfs(vector<int>& nums, int u){
        if (u == nums.size()){
            res.push_back(path);
            return ;
        }

        dfs(nums, u + 1);//不选
        path.push_back(nums[u]);
        dfs(nums, u + 1);//选
        path.pop_back();
    }

    vector<vector<int>> subsets(vector<int>& nums) {
        dfs(nums, 0);
        return res;
    }
};
```

# 90.子集||

![image-20210315000457384](https://gitee.com/xddadd/cloud-image/raw/master/image-20210315000457384.png)

## dfs搜索

为了保证不重复，所以要保证枚举的顺序，不能枚举了a[k]，再枚举k前面的数,所以要排序

搜索顺序是每一个数选0,1,2...k个，k为当前数出现次数，一共有k+1种选法。类似多重背包.

## AC代码

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;

    void dfs(vector<int>& nums, int u){
        //结束
        if (u == nums.size()) {
            res.push_back(path);
            return ;
        }
        //对于当前数记录有多少个
        int k = u;
        while (k < nums.size() && nums[k] == nums[u]) k ++;
        int cnt = k - u;
        //cnt个数，则有cnt+1钟选法
        for (int i = 0 ; i <= cnt ; i ++){
            dfs(nums, k);
            path.push_back(nums[u]);
        }
        //回溯
        for (int i = 0 ; i <= cnt ; i ++){
            path.pop_back();
        }
    }

    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        sort(nums.begin(), nums.end());//保证顺序
        dfs(nums, 0);
        return res;
    }
};
```

# 216.组合总和|||

![image-20210603063116397](https://gitee.com/xddadd/cloud-image/raw/master/image-20210603063116397.png)

## dfs搜索

> 组合数枚举的变形，组合数需要传入一个start参数，每次从start开始枚举。
>
> 新增了一个和的要求，我们只需要在搜索的过程当中不仅记录路径，顺便记录和即可。

时间复杂度指数级别

## AC代码

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;

    void dfs(int u, int k,int start, int s, int n) {
        if (u == k) {
            if (s == n) 
                res.push_back(path);
            return ;
        }

        for (int i = start ; i <= 9 ; i ++) {
            path.push_back(i);
            dfs(u + 1, k, i + 1, s + i,n);
            path.pop_back();
        }
    }

    vector<vector<int>> combinationSum3(int k, int n) {
        dfs(0, k, 1, 0, n);
        return res;
    }
};
```

# 386.字典序排数

![image-20210610201307737](https://gitee.com/xddadd/cloud-image/raw/master/image-20210610201307737.png)

## trie树+dfs搜索

我们用一个trie树来表示所有的数，如果当前数是在$1-n$范围内的，就插入到答案中。因为表示了所有数，所以没必要模拟一个trie数并且插入。

> 在trie中搜索如何保证字典序是递增的呢？
>
> ![image-20210610202319162](https://gitee.com/xddadd/cloud-image/raw/master/image-20210610202319162.png)
>
> 遍历的顺序：根 0子树 1子树......

所以我们只需要模拟一个在trie上树搜索即可。

时间复杂度$O(N)$

## AC代码

```cpp
class Solution {
public:
    vector<int> res;


    vector<int> lexicalOrder(int n) {
        for (int i = 1 ; i <= 9 ; i ++) dfs(i, n);
        return res;
    }

    void dfs(int now, int n) {
        if (now > n) return ;
        res.push_back(now);
        for (int i = 0 ; i <= 9 ; i ++) {
            dfs(now * 10 + i, n);
        }
    }
};
```

# 526.优美的排列

![image-20210620154640261](C:\Users\jason\AppData\Roaming\Typora\typora-user-images\image-20210620154640261.png)

## dfs搜索

该题我们使用全排列的枚举方法，来枚举每一位填什么数。

每一位填什么数的条件应该满足题意给定的两个条件之一，这就相当于进行了剪枝。

时间复杂度不可知

## AC代码

```cpp
class Solution {
public:
    int res = 0;
    vector<bool> st;

    void dfs(int u, int n){
        if (u == n + 1) {
            res ++;
            return ;
        }

        for (int i = 1 ; i <= n ; i ++) {
            if (!st[i] && (i % u == 0 || u % i == 0)) {
                st[i] = true;
                dfs(u + 1, n);
                st[i] = false;
            }
        }
    
    }

    int countArrangement(int n) {
        st = vector<bool>(n + 1, false);
        dfs(1, n);
        return res;
    }
};
```


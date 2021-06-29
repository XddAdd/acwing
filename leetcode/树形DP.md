# 树形DP

lc究极班

# 337.打家劫舍|||

![image-20210309183118896](https://gitee.com/xddadd/cloud-image/raw/master/image-20210309183118896.png)

## 树形DP

![image-20210309183215410](https://gitee.com/xddadd/cloud-image/raw/master/image-20210309183215410.png)

时间复杂度O(N)

## AC代码

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> dfs(TreeNode* u){
        //空
        if (!u) return {0, 0};
        vector<int> f(2, 0);
        auto l = dfs(u->left), r = dfs(u->right);
        //不选
        f[0] = max(l[0], l[1]) + max(r[0], r[1]);
        //选
        f[1] = l[0] + r[0] + u->val;
        return f;
    }

    int rob(TreeNode* root) {
        auto f = dfs(root);
        return max(f[0], f[1]);
    }
};
```

# 1483.树节点的第K个组先

![image-20210625222726933](https://gitee.com/xddadd/cloud-image/raw/master/image-20210625222726933.png)

![image-20210625222743911](https://gitee.com/xddadd/cloud-image/raw/master/image-20210625222743911.png)

![image-20210625222754415](https://gitee.com/xddadd/cloud-image/raw/master/image-20210625222754415.png)

## 树形DP

看到该题，学过基础课的都能很直观的想到树形DP/预处理。

状态表示：`f[i][j]`表示`i`节点向上走$2^j$个节点是哪个节点。

> 为什么要这么别扭的表示呢？直接表示`f[i][j]`为`i`节点向上走`j`步的节点是哪个节点不行？
>
> 直接表示也可以，但是因为k很大，k大概是$5e4$，二维数组存不下啊，所以要取一个log

- 对于给定的节点$i$，如何求节点$i$向上走k步的节点是哪个节点呢？

> 例如：求v向上走64步的节点?
>
> $k$ = $69$ = $64 + 4 + 1$ =  $(1000101)_2$ = $2^0 + 2^2 + 2^6$
>
> 相当于v先向上走$2^0$到v1，再从v1向上走$2^2$到v2，最终从v2向上走$2^6$步即可求得答案
>
> 表达式：
>
> $v1 = f[v][0]$
>
> $v2 = f[v1][2]$
>
> $v3 = f[v2][6]$
>
> 这样就可推出答案。

- 如何预处理所有的`f[i][j]`呢？

> `f[i][0] = p[i]`，$i$向上走$2^0$步是它的父节点
>
> `f[i][1] = f[f[i][0]][0]`，一共要走$2^1$步，先走$2^0$步到t，在走$2^0$步
>
> `f[i][2] = f[f[i][1]][1]` ，一共要走$2^2$步，先走$2^1$步到t，在走$2^1$步
>
> ........
>
> `f[i][k] = f[f[i][k - 1]][k - 1]`，一共要走$2^k$步，先走$2^{k-1}$步到t，在走$2^{k-1}$步
>
> 由此可以看出预处理每个节点，依赖于上一个节点，即要处理该节点，要保证其所有祖先节点都被处理过才行。

**因为考虑到爆栈问题，所以是用BFS来实现预处理的**

时间复杂度$O(NlogN)$

## AC代码

```cpp
class TreeAncestor {
public:
    vector<vector<int>> f;
    vector<vector<int>> son;//bfs，所以需要存储儿子的关系

    TreeAncestor(int n, vector<int>& p) {
        f = vector<vector<int>>(n, vector<int>(20, -1));
        son = vector<vector<int>>(n);
        
        //预处理儿子关系
        int root = -1;
        for (int i = 0 ; i < n ; i ++) {
            if (p[i] == -1) root = i;
            else son[p[i]].push_back(i);
        }

        //bfs预处理f
        queue<int> q;
        q.push(root);
        while (q.size()) {
            int t = q.front();
            q.pop();
            //枚举所有儿子状态
            for (auto s : son[t]) {
                f[s][0] = t;
                for (int k = 1 ; k < 20 ; k ++) {
                    if (f[s][k - 1] != -1)
                        f[s][k] = f[f[s][k - 1]][k - 1];
                }
                q.push(s);
            }

        }
    }
    
    int getKthAncestor(int x, int k) {
        for (int i = 0 ; i < 20 ; i ++) {
            if (k >> i & 1) {
                x = f[x][i];
                if (x == -1) return x;
            }
        }
        return x;
    }
};

/**
 * Your TreeAncestor object will be instantiated and called as such:
 * TreeAncestor* obj = new TreeAncestor(n, parent);
 * int param_1 = obj->getKthAncestor(node,k);
 */
```


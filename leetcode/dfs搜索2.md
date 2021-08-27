# dfs搜索2

lcdfs搜索的题

# 726.原子的数量

# 93.复原IP地址

![image-20210721233659711](https://gitee.com/xddadd/cloud-image/raw/master/master/20210721233706.png)

## dfs搜索

搜索顺序是根据.这个分隔符来枚举，枚举每段组成的数字是什么。

dfs参数要传入：u,表示枚举到s的第u个字符，还有一个参数cnt表示已经枚举组成了多少个数组。IP地址是4个数字组成，所以当cnt>=4，则可以返回了。

注意：不能使用前导0。

## AC代码

```cpp
class Solution {
public:
    vector<string> res;
    int n;

    vector<string> restoreIpAddresses(string s) {
        n = s.size();
        dfs(s, 0, 0, "");
        return res;
    }

    void dfs(string& s,int u, int cnt, string path) {
        if (u == s.size()) {
            if (cnt == 4){
                path.pop_back();
                res.push_back(path);
            }
            return ;
        }
        if (cnt == 4) return;
        for (int i = u, t = 0 ; i < s.size() ; i ++) {
            if (i > u && s[u] == '0') break; //前导0,
            t = t * 10 + (s[i] - '0');
            if (t <= 255) {
                dfs(s, i + 1, cnt + 1, path + to_string(t) + ".");
            } else break;
        }
    }
};
```


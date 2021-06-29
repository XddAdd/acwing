# tire树

# 676.实现一个魔法字典

![image-20210531220243717](https://gitee.com/xddadd/cloud-image/raw/master/image-20210531220243717.png)

![image-20210531220250279](https://gitee.com/xddadd/cloud-image/raw/master/image-20210531220250279.png)

## Tire树+dfs搜索

> tire树来存储字符串。如何实现判断word是否跟集合中的字符串只差一个字符不同呢？
>
> 遍历树，对于某个节点来说。
>
> ![image-20210531221112351](https://gitee.com/xddadd/cloud-image/raw/master/image-20210531221112351.png)

1. 存储字符：用tire树存储
2. dfs来判断word是否跟集合中的字符串只差一个字符不同。

插入每个字符串：时间复杂度$O(N$)

查询判断字符串：时间复杂度$O(N$)

空间复杂度$O(26N$)

## AC代码

```cpp
class MagicDictionary {
public:
    static const int N = 1e4 + 10;
    int son[N][26];
    bool is_end[N];
    int idx;

    /** Initialize your data structure here. */
    MagicDictionary() {
        memset(son, 0, sizeof son);
        memset(is_end, false, sizeof is_end);
        idx ++;
    }

    void insert(string& s) {
        int p = 0;
        for (auto c : s) {
            int u = c - 'a';
            if (!son[p][u]) son[p][u] = ++ idx;
            p = son[p][u];
        }
        is_end[p] = true;
    }

    void buildDict(vector<string> dictionary) {
        for (auto& s : dictionary) insert(s);
    }

    bool dfs(string& word, int u, int p, int diff) {
        //word遍历完，且不同字符串只有一个
        if (u == word.size() && is_end[p] && diff == 1) return true;
        //遍历完
        if (u >= word.size() || diff > 1) return false;
        
        for (int i = 0 ; i < 26 ; i ++) {
            if (!son[p][i]) continue;
            if (dfs(word, u + 1, son[p][i],  word[u] - 'a' == i ? diff : diff + 1))
                return true;
        }
        return false;
    }
    
    bool search(string searchWord) {
        return dfs(searchWord, 0, 0, 0);
    }
};

/**
 * Your MagicDictionary object will be instantiated and called as such:
 * MagicDictionary* obj = new MagicDictionary();
 * obj->buildDict(dictionary);
 * bool param_2 = obj->search(searchWord);
 */
```

#  211.添加与搜索单词 - 数据结构设计

![image-20210602214730775](https://gitee.com/xddadd/cloud-image/raw/master/image-20210602214730775.png)

![image-20210602214740956](https://gitee.com/xddadd/cloud-image/raw/master/image-20210602214740956.png)

## tire树+dfs搜索

> 存储的单词都是小写字母，所以用tire树来存。
>
> 对于查询的单词来说：
>
> - 如果遇到`'.'`，则枚举所有的存在的下一个字母进行搜索。
> - 否则是单词，搜索下一个字母节点即可。

插入每个字符串：时间复杂度$O(N)$

查询每个字符串：最坏遍历所有的节点，时间复杂度$O(NM)$

空间复杂度$O(26N)$

## AC代码

```cpp
class WordDictionary {
public:
    static const int N = 1e5 + 10;
    int son[N][26], idx;
    bool is_end[N];
    /** Initialize your data structure here. */

    WordDictionary() {
        idx = 0;
        memset(son, 0, sizeof son);
        memset(is_end, false, sizeof is_end);
    }
    
    void insert(string& word) {
        int p = 0;
        for (auto c : word) {
            int u = c - 'a';
            if (!son[p][u]) son[p][u] = ++ idx;
            p = son[p][u];
        }
        is_end[p] = true;
    }

    bool dfs(string& word, int k, int p) {
        if (k == word.size()) return is_end[p];//字符串枚举到最后了
        //不为.
        if (word[k] != '.') {
            if (son[p][word[k] - 'a']) return dfs(word, k + 1, son[p][word[k] - 'a']);
            return false;
        }
        //为.,枚举所有子节点
        for (int i = 0 ; i < 26 ; i ++) {
            if (son[p][i] && dfs(word, k + 1, son[p][i]))
                return true;
        }
        return false;    
    }
    

    void addWord(string word) {
        insert(word);
    }
    
    bool search(string word) {
        return dfs(word, 0, 0);
    }
};

/**
 * Your WordDictionary object will be instantiated and called as such:
 * WordDictionary* obj = new WordDictionary();
 * obj->addWord(word);
 * bool param_2 = obj->search(word);
 */
```

# 677.键值映射

![image-20210617181418957](https://gitee.com/xddadd/cloud-image/raw/master/image-20210617181418957.png)

## Trie树+哈希

题意：实现两个操作

1. 插入key，value，如果key已经存在，更新value
2. 给定key的前缀，求同样有该前缀的key的value的总和

第一个操作用哈希表很容易实现，第二个操作看到前缀，我们就要立即想到trie树，因为trie树也叫前缀树。

> 因为第二个操作，所以一定要用前缀树。
>
> ![image-20210617182754952](https://gitee.com/xddadd/cloud-image/raw/master/image-20210617182754952.png)
>
> 如图：对于每个结点来说，我们还要多维护一个信息，以当前节点为根的子树的value之和，这样查询以某前缀开头的value之和我们也只需要遍历该前缀即可。

因为满足了第二个操作，如果是第一个操作要更新value呢？

更新value的时候边遍历边将节点$-oldValue$后再$+newValue$即可。

时间复杂度$O(N)$

## AC代码

```cpp
const int N = 5e3 + 10;
int son[N][26], s[N];
int idx;
unordered_map<string,int> mp;

class MapSum {
public:
    
    /** Initialize your data structure here. */
    MapSum() {
        memset(son, 0, sizeof son);
        memset(s, 0, sizeof s);
        idx = 0;
        mp = {};
    }
    
    //插入
    void add(string& str, int oldV, int newV) {
        int p = 0;
        for (auto c: str) {
            int u = c - 'a';
            if (!son[p][u]) son[p][u] = ++ idx;
            p = son[p][u];
            s[p] += newV - oldV;
        }
    }
    
    //查询
    int query(string& str) {
        int p = 0;
        for (auto c: str) {
            int u = c - 'a';
            if (!son[p][u]) return 0;
            p = son[p][u];
        }
        return s[p];
    }
    
    void insert(string key, int val) {
        if (mp.count(key)) {
            add(key, mp[key], val);
        }else add(key, 0, val);
        mp[key] = val;
    }
    
    int sum(string prefix) {
        return query(prefix);     
    }
};

/**
 * Your MapSum object will be instantiated and called as such:
 * MapSum* obj = new MapSum();
 * obj->insert(key,val);
 * int param_2 = obj->sum(prefix);
 */
```


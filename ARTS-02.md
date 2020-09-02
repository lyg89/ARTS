>ARTS：Algorithm、Review、Tip、Share
>- Algorithm 算法题
>- Review 英文文章
>- Tip 回想一下本周工作中学到的一个小技巧
>- Share思考一个技术观点、社会热点、一个产品或是一个困惑

### Algorithm
#### 547. 朋友圈
>班上有 N 名学生。其中有些人是朋友，有些则不是。他们的友谊具有是传递性。如果已知 A 是 B 的朋友，B 是 C 的朋友，那么我们可以认为 A 也是 C 的朋友。所谓的朋友圈，是指所有朋友的集合。
>
>给定一个 N * N 的矩阵 M，表示班级中学生之间的朋友关系。如果M[i][j] = 1，表示已知第 i 个和 j 个学生互为朋友关系，否则为不知道。你必须输出所有学生中的已知的朋友圈总数。
>
> 
>
>示例 1：
>
>输入：
>[[1,1,0],
> [1,1,0],
> [0,0,1]]
>输出：2 
>解释：已知学生 0 和学生 1 互为朋友，他们在一个朋友圈。
>第2个学生自己在一个朋友圈。所以返回 2 。
>示例 2：
>
>输入：
>[[1,1,0],
> [1,1,1],
> [0,1,1]]
>输出：1
>解释：已知学生 0 和学生 1 互为朋友，学生 1 和学生 2 互为朋友，所以学生 0 和学生 2 也是朋友，所以他们三个在一个朋友圈，返回 1 。
> 
>
>提示：
>
>1 <= N <= 200
>M[i][i] == 1
>M[i][j] == M[j][i]
>
>来源：力扣（LeetCode）
>链接：https://leetcode-cn.com/problems/friend-circles

##### 解法描述
1 DFS：遍历参数中的元素在碰到元素位置为1的情况时，应用递归，依次处理相邻的元素，关键点是使用数组记录已经访问过的元素，防止重复标记。

2 并查集：并查集是专门用于处理分组、配对类型问题的一种数据结构，为每个元素添加parent引用指向自己的父节点，父节点的父节点指向自己，即为一个分组的根节点，并以此来判断元素是否处于同一个分组（根节点一致则为同一分组）。一般提供find查询操作获取到对应的分组根节点，通过union合并操作将两个分组进行合并处理。

##### 编码实现
1 DFS：
```java
public int findCircleNum(int[][] M) {
    int[] visited = new int[M.length];
    int count = 0;
    for (int i=0; i < M.length; i++) {
        if (visited[i] == 0) {
            dfs(M, visited, i);
            count++;
        }
    }
    return count;
}

private void dfs (int[][] M, int[] visited, int i) {
    for (int j=0; j < M.length; j++) {
        if (M[i][j] == 1 && visited[j] == 0) {
            visited[j] = 1;
            dfs(M, visited, j);
        }
    }
}
```
2 并查集：
```java
public int findCircleNum(int[][] M) {
    int n = M.length;
    UnionFind unionFind = new UnionFind(n);
    for (int i=0; i< n-1; i++) {
        for (int j = i+1; j < n; j++) {
            if (M[i][j] == 1) {
                unionFind.union(i, j);
            } 
        }
    }
    return unionFind.count;
}

class UnionFind {
    private int count = 0;
    private int[] parent;

    public UnionFind(int n) {
        count = n; 
        parent = new int[n]; 
        for (int i = 0; i < n; i++) { 
            parent[i] = i;
        }
    }

    public int find(int p) { 
        while (p != parent[p]) { 
            parent[p] = parent[parent[p]]; 
            p = parent[p]; 
        }
        return p; 
    }

    public void union(int p, int q) { 
        int rootP = find(p); 
        int rootQ = find(q); 
        if (rootP == rootQ) return; 
        parent[rootP] = rootQ; 
        count--;
    }
}
```
##### 复杂度分析
DFS因为要遍历矩阵每个元素，因此时间复杂度为O(n^2)，空间复杂度为O(n);
并查集，遍历矩阵每个元素，同时并查集操作最坏O(n)时间，时间复杂度为O(n^3)，空间复杂度为O(n)，存储parent。

### Review
Kibana knowledge share：https://github.com/nreese/kibana-plugin-notes
这篇文章提到了Elastic stack 技术栈中 Kibana 实现上涉及到的基础知识，对于Kibana的插件开发以及源码学习、修改提供了指引。

### Tip
本周工作主要是想扩展kibana上数据展示的功能，公司将前端JS日志进行了统一收集，但因为前段js经过压缩，收集来的错误堆栈信息显示的行号、列号以及内容跟原始js文件中无法对应，对于排错效率有所影响。因此决定将kibana中显示的数据进行进一步的处理，显示出错的原始行号、列号及代码。

ES官方其实有提供集成了SourceMap功能的 APM 产品，但因为接入方式与现有的方式不同，改动较大。同时，也在网上搜索了相关功能的其他集成方案，除了sentry提供了比较原生友好的支持外，Elastic Stack 技术栈没有找到其他的实现方案了，也因此决定扩展kibana的展示功能。

目前实现了Discover页面上，数据详情 Table 展开页添加了一个操作选项，可以将对应字段数据解析为堆栈信息，找到对应的SourceMap文件，进一步处理回显原始数据就好了。麻烦的地方是定位Discover的代码，不过也还好，使用强大的搜索功能也可以找到的。

参考资料：
- JavaScript Source Map 详解--阮一峰
https://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html
- 脚本错误量极致优化-让脚本错误一目了然
https://github.com/joeyguo/blog/issues/14


### Share
过去一周过得很焦虑，应该是因为工作上的压力造成的，自己的状态调整的并不好。由于自身前端技术很薄弱，又急于实现目标，过程、思路都很是迷茫，使用搜索引擎不断搜索问题的解决方案，有些使蛮力的意思，幸好周末的时候，放松了下来，经过一些反思，调整了一下思路。即使没能完全理解一些东西，也算是能实现工作上的需求了。

这种感觉并不好，这让我思考，到底是先掌握对应的技术，再去解决对应问题；还是在面对问题的时候，再去寻找解决方案、熟悉相关技术？

首先，我认为两者之间并没有明显的边界，对于日常的一些工作，我觉得第一种的情况应该更常见、更应是我追求的一种状态，但知识、技术永远是学不完的，新技术不断涌现，我们不可能掌握所有的知识，也因此第二种情况也会是我们要时常面对的，这种情况，只能去掌握好基础技术及原理，理解解决问题的方案与思路，才能以不变应万变，不至于像我这样茫然不知所措。


题图：Photo by Hannah Rodrigo on Unsplash

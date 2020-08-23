
>ARTS：Algorithm、Review、Tip、Share
>- Algorithm 算法题
>- Review 英文文章
>- Tip 回想一下本周工作中学到的一个小技巧
>- Share思考一个技术观点、社会热点、一个产品或是一个困惑

### Algorithm
#### 208. 实现 Trie (前缀树)

>实现一个 Trie (前缀树)，包含 insert, search, 和 startsWith 这三个操作。
>
>示例:
>
>Trie trie = new Trie();
>
>trie.insert("apple");
>trie.search("apple");   // 返回 true
>trie.search("app");     // 返回 false
>trie.startsWith("app"); // 返回 true
>trie.insert("app");   
>trie.search("app");     // 返回 true
>说明:
>
>你可以假设所有的输入都是由小写字母 a-z 构成的。
>保证所有输入均为非空字符串。
>
>来源：力扣（LeetCode）
>链接：https://leetcode-cn.com/problems/implement-trie-prefix-tree

##### 解法描述
前缀树（Trie，also called digital tree or prefix tree）是指一种用于排序的树形数据结构，用于存储一个动态的数据结构关联的数组集合。通常是字符串的字符数组。不同于二叉树的是，树中没有节点存储与当前节点关联的目标键；相反，节点在树中的**位置**才定义了与该节点关联的键，示意图如下：


本题假设了所有输入都是由小写字母构成，因此每个节点都可以通过一个由26个英文字母构成的数组来表示，每个字母再依次向下关联另外一个同样结构的节点，构成了一个26叉树这样的数据结构，同时为了表示一个单词有没有结束，因此需要再节点对象中定义一个变量来进行表示。

##### 编码实现
首先实现Trie的节点定义：
```java
private class TrieNode {
    private TrieNode[] links;
    private boolean isEnd = false;

    public TrieNode() {
        links = new TrieNode[26];
    }

    public void setEnd() {
        this.isEnd = true;
    }

    public boolean isEnd() {
        return isEnd;
    }

    public boolean contains(char c) {
        return links[c - 'a'] != null;
    }

    public TrieNode get(char c) {
        return links[c - 'a'];
    }

    public void set(char c) {
        links[c - 'a'] = new TrieNode();
    }
}
```
然后定义外层的Trie数据结构，使用TrieNode作为节点类型，代码如下：

```java
class Trie {

    private TrieNode root;

    /**
     * Initialize your data structure here.
     */
    public Trie() {
        root = new TrieNode();
    }

    /**
     * Inserts a word into the trie.
     */
    public void insert(String word) {
        TrieNode node = root;
        for (int i = 0; i < word.length(); i++) {
            char c = word.charAt(i);
            if (!node.contains(c)) {
                node.set(c);
            }
            node = node.get(c);
        }
        node.setEnd();
    }

    /**
     * Returns if the word is in the trie.
     */
    public boolean search(String word) {
        final TrieNode node = this.searchPrefix(word);
        return node != null && node.isEnd();
    }

    /**
     * Returns if there is any word in the trie that starts with the given prefix.
     */
    public boolean startsWith(String prefix) {
        final TrieNode node = this.searchPrefix(prefix);
        return node != null;
    }

    private TrieNode searchPrefix(String word) {
        TrieNode node = root;
        for (int i = 0; i < word.length(); i++) {
            char c = word.charAt(i);
            if (!node.contains(c)) {
                return null;
            }
            node = node.get(c);
        }
        return node;
    }
}
```

##### 复杂度分析
添加、查询的时间复杂度为 O(m)，m为单词的平均长度。
空间复杂度为 O(1)，由26个英文字母拼接构成的单词总数量是固定的。

#### 212. 单词搜索 II
>给定一个二维网格 board 和一个字典中的单词列表 words，找出所有同时在二维网格和字典中出现的单词。
>
>单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母在一个单词中不允许被重复使用。
>
>示例:
>
>输入: 
>words = ["oath","pea","eat","rain"] and board =
>[
>  ['o','a','a','n'],
>  ['e','t','a','e'],
>  ['i','h','k','r'],
>  ['i','f','l','v']
>]
>
>输出: ["eat","oath"]
>说明:
>你可以假设所有输入都由小写字母 a-z 组成。
>
>提示:
>
>你需要优化回溯算法以通过更大数据量的测试。你能否早点停止回溯？
>如果当前单词不存在于所有单词的前缀中，则可以立即停止回溯。什么样的数据结构可以有效地执行这样的操作？散列表是否可行？为什么？ 前缀树如何？如果你想学习如何实现一个基本的前缀树，请先查看这个问题： 实现Trie（前缀树）。
>
>来源：力扣（LeetCode）
>链接：https://leetcode-cn.com/problems/word-search-ii

##### 解法描述
本题的提示中有提到前缀树，使用Trie可解决这个问题：先使用传递的参数单词列表构造一个前缀树，然后再遍历二维网格中的每个元素，与上下左右的元素进行拼接，去前缀树中搜索是否存在对应的字符串，如果不存在，即可退出当前元素的查询，向后遍历；若存在匹配，则进行DFS的网格元素拼接，直到找到所求结果。

注意DFS的处理过程中应注意边界元素的情况，同时题目要求不能重复使用元素，因此需要一个同等容量的二维数组存储当前DFS的访问情况。

因为要使用Trie数据结构，与上一题重复，省略了代码实现（与上一题不同之处在于：叶子节点保存了对应字符串的值，方便存储到最终结果中）

##### 编码实现
```java
public List<String> findWords(char[][] board, String[] words) {
        if (board == null || board[0] == null || words == null) {
            return null;
        }

        Trie trie = new Trie();
        for (String word : words) {
            trie.insert(word);
        }


        Set<String> result = new HashSet<>();
        final int m = board.length;
        final int n = board[0].length;
        boolean[][] visited = new boolean[m][n];

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                dfs(board, i, j, trie.root, result, visited, m, n);
            }
        }
        return new LinkedList<>(result);
    }

    private void dfs(char[][] board, int i, int j, TrieNode node, Set<String> result, boolean[][] visited, int m, int n) {
        if (i < 0 || i >= m || j < 0 || j >= n || visited[i][j]) {
            return;
        }

        // 得到当前字符对应的子节点
        node = node.links[board[i][j] - 'a'];
        // 若子树为空（即不存在匹配）则返回
        if (node == null) {
            return;
        }

        // 若isEnd，则当前单词匹配结束，加入结果集（此时不能回退，因为存在 a,d d,d 查找ad的情况）
        if (node.isEnd()) {
            result.add(node.val);
        }
        visited[i][j] = true;

        // 向下遍历
        this.dfs(board, i, j + 1, node, result, visited, m, n);
        this.dfs(board, i, j - 1, node, result, visited, m, n);
        this.dfs(board, i + 1, j, node, result, visited, m, n);
        this.dfs(board, i - 1, j, node, result, visited, m, n);

        // 恢复状态
        visited[i][j] = false;
    }
}
```
向下遍历的实现方式上这里有些死板了，可以通过定义两个相互匹配的方向数组，循环数组，每次元素移动方向通过该数组控制，这里使用的四向变量，如果要是八向的话，使用数组移动元素的方式更加灵活，本题的dfs使用示例如下：
```java
int[] rowOffset = {-1, 0, 1, 0};
int[] colOffset = {0, 1, 0, -1};
for (int k = 0; k < 4; ++k) {
  int newRow = i + rowOffset[k];
  int newCol = j + colOffset[k];
  this.dfs(board, newRow, newCol, node, result, visited, m, n);
}
```

##### 复杂度分析
构建Trie的时间复杂度为 O(k)，k为单词平均长度，遍历单词数组的为O(n)，n为单词数组的个数，
遍历二维数组的时间复杂度为 m * m，内部调用递归方法，递归深度为单词长度 n，最终时间复杂度为O(k * n + m * m * n)；空间复杂度为O(m * m)，存储元素是否已经使用的二维数组。

### Review
What are some of the most basic things every programmer should know?
https://www.quora.com/What-are-some-of-the-most-basic-things-every-programmer-should-know

Quora上的这个帖子讲到了很多程序员的修养，也就是程序员的价值观，从事于这个行业，我们要了解什么是好的，什么是不好的，列举几条如下：

1 If it's not tested, it doesn't work.
2 Source control is your friend - make sure you use it.
3 Don't reinvent the wheel, library code is there to help.
4 Code that's hard to understand is hard to maintain.
5 Code that's hard to maintain is next to useless.
6 There is no such thing as a 5 minute job. It'll always take at least half a day.
7 The true cost of poorly written code is in the maintenance.
8 Eat your own dog food — fixing bugs in your own code helps you code better and improves your understanding.

### Tip
ElastAlert：https://github.com/Yelp/elastalert

Yelp（美国一家点评网站公司）开源的基于 Elasticsearch 简单、灵活的报警框架，基于Python语言开发。主要解决对于近实时数据写入到ES，当数据匹配到某一确定规则时进行报警。ElastAlert有一定的可靠性、高度模块化，且易于设置与可灵活配置。

基于Elasticsearch，将规则类型（rule types）和报警（alerts）结合起来工作。定时查询ES，返回的数据经过规则类型匹配，针对匹配项数据，提供给一个或多个报警处理。

可靠性
	1 将自身的元状态信息存储到了ES中，启动时恢复先前停止的位置；
	2 ES没有响应时，ElastAlert将等到它恢复后再继续；
	3 出现错误的警报时会自动重试（一段时间）；

支持的典型规则类型：
	1 Frequency：某个事件出现至少指定次数后匹配（可以通过每个 query_key 统计）；
	2 Spike：波峰、波谷；
	3 flatline：低于最低值；
	4 blacklist：指定字段在黑名单中进行匹配；
	5 whitelist：指定字段不在白名单时匹配；
	6 Change：某个字段进行了更改时触发事件；
	7 any：任何匹配了 filter 条件的doc就报警；
	8 New Term：某些字段出现新值时匹配；
	9 Cardinality：唯一值总数大于或小于一个阈值时
	10 Metric Aggregation：度量窗口内一度量值大于或小于阈值时匹配
	11 Spike Aggregation
	12 Percentage Match
	……（可扩展）


支持的报警类型：
	Email
	JIRA
	OpsGenie
	Commands
	HipChat
	MS Teams
	Slack
	Telegram
	GoogleChat
	AWS SNS
	VictorOps
	PagerDuty
	PagerTree
	Exotel
	Twilio
	Gitter
	Line Notify
	Zabbix

##### 优势
灵活可扩展，支持很多通用的数据匹配规则类型、同时报警也支持各种方式，也可自行开发扩展。
匹配规则配置是基于文件的方式存储的，支持热部署，新增、修改匹配规则会及时更新，不需要重启。
• 成熟度高（6.9K star@github） 
• 监控种类丰富
• 开源，方便二次开发

##### 劣势
1 不支持分布式架构
	• 单实例架构，不能水平扩展，无法failover
	• Rules依赖配置文件存储，未入db
2 易用度不高
	• Rule配置项比较复杂
	• 不支持api配置rule
3 rules串行执行，效率低

### Share
过去的我，做过很多错事，如今看来是有违原则的，答应别人的事没能办到、欠缺充足的思考就给对方答复。也做了很多有过坚持却最终没有做好的事情。现在的我，缺点依旧很多，毛病很多，技术也差很多，想要改变行动上却一直断断续续。改变也许就在一念之间，随着年龄的增长，想想总要鼓起勇气去做一些事吧，否则年华依旧会匆匆而逝了，这就是重启ARTS的缘由。

再过5年、10年会是什么样子，不敢去想，但身边有人能给出前进的方向，自认这些经验分享也会对自己受用，我虽不算聪明，但想经过自己的努力，去做一些能提升自己，能锻炼自己，能让自己真正有所收获的一些事，因此更要努力去做好了。今天的Share没有多少实质内容，算是对自己的一个简短总结吧。


题图：Photo by Tim Arterbury on Unsplash

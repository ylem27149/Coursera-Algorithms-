# [Algorithms Part2 Week1 WordNet](https://www.coursera.org/learn/algorithms-part2/programming/BCNsp/wordnet)

编程顺序参考[FAQ](https://coursera.cs.princeton.edu/algs4/assignments/wordnet/faq.php)，先写SAP，再写WordNet，最后是Outcast

## 输入文件分析

### synset

```行号,noun1 noun2,释义```

一行中会有不止一个单词（noun），同一个单词也会出现在不同行。释义不需要存储。

读取的时候，使用In库的实例方法readLine( )

```java
// 实例方法，需要用变量调用。
In in1 = new In(synsets);
String synLine = in1.readLine();

```

然后借助split(",")分离出行号和同义词们。split( )方法返回的是String[ ]。  
行号要用静态方法Integer.parseInt( )实现从String向int的转换，同义词之间是用空格分离。

```java
number = Integer.parseInt(synLine.split(",")[0]);   // 该行同义词集的序号
aux = synLine.split(",")[1];    // 同义词集合，每个noun用空格隔开
synsetMap.put(number, aux.split(" "));     
```

然后不断向下读取，当到达文件尾，readLine( )会返回null，以此作为循环条件。

### hypernyms

```son,father1,father2```

这个文件中的每一行都是用逗号隔开的数字。其中后面的数字都是第一个数字所代表的同义词集的上位词，数字都是synset中的对应行号。

有向图的v -> w （v has path to w) 代表 WordNet中 w 是 v 的父节点。所以可以用有向图Diagraph来表征 hypernyms 中的上位词关系网。

## SAP：Shortest ancestral path

### SAP寻找算法

第一版被示意图影响，只考虑了单父节点的简单情况，借助Diagraph的adj[ ]实现遍历。

但是实际上WordNet中的一个词可以有多个父节点，应该借助[BreadthFirstDirectedPaths](https://algs4.cs.princeton.edu/42digraph/BreadthFirstDirectedPaths.java.html)实现最小公共祖先的寻找。

有向图的v -> w （v has path to w) 代表 WordNet中 w 是 v 的父节点。所以可以用  
```if (bfsV.hasPathTo(i) && bfsW.hasPathTo(i)) ``` 来寻找common ancestor。

在有多条路径的情况下，bfs.disto会返回最短路径。WordNet中节点可以视作它自身的ancestor，bfs中的每个点也总是可以到达它自己，所以这种情况不用特殊处理。

尝试过用借助Diagraph的adj[ ]，只检索两个节点的所有上位词而不是所有的词条，会提示 *Number of primitive operations in Digraph exceeds limit* ,最终还是遍历所有同义词集。

***

以下内容来自[FAQ](https://coursera.cs.princeton.edu/algs4/assignments/wordnet/faq.php) ：

* Is a vertex considered an ancestor of itself? Yes.
* Should I re-implement breadth-first search in my SAP class?   
  * It depends. You are free to call the relevant methods in BreadthFirstDirectedPaths.java.   
  * However, you can improve performance by several orders of magnitude by implementing it yourself, optimizing it for repeated calls with the same digraph. (See the optional optimization section below.)   
* How to make  the length(Iterable<Integer> v, Iterable<Integer> w) method taking time proportional to E + V?   
  * The key is using the **constructor** in BreadthFirstDirectedPaths that **takes an iterable of sources** instead of using a single source.
  
- [ ] 有个问题。在 Jul 14, 2019 提交的版本中，把四个计算的共同部分写成private int[] shortest(BreadthFirstDirectedPaths bfsV, BreadthFirstDirectedPaths bfsW)，返回的int数组中一个是公共祖先，一个是最小距离。这样 Test 16: check length() and ancestor() with iterable arguments 也就是 Iterable<Integer> 的测试过不去。为什么呢？单参数和迭代集合，传入传出的数据类型不都一样的，还是bfs吗？

### 参数合法性检测

引用变量要进行null检测，原始类型（数字）要检查是否在序号范围内。

Iterable<Integer>中的Integer也需要进行Null检测。

## WordNet

这个作业最关键的就是实例变量数据结构的选择。上面的SAP只要意识到到要用bfs，还是很容易想到解法的。

### API分析

```java
public class WordNet {

   public WordNet(String synsets, String hypernyms)    // constructor takes the name of the two input files

   public Iterable<String> nouns()                     // returns all WordNet nouns
   
   public boolean isNoun(String word)                  // is the word a WordNet noun?

   public int distance(String nounA, String nounB)     // distance between nounA and nounB (defined below)
   public String sap(String nounA, String nounB)       // a synset (second field of synsets.txt) that is the common ancestor of nounA and nounB in a shortest ancestral path (defined below)

```

API中,distance 和 sap 需要：

1. 搜索到输入的noun在synsets中的序号（**以词求号**）。因为同一个词可能会出现在不止一行，即一个词可能会有多个释义，所以这个序号也可能不止一个。也就是一个存储成对的 <String，int[ ]> 的数据结构。（实际上用的是 ```HashMap<String, ArrayList<Integer>>```）
2. 根据nounA和nounB 对应的序号，用SAP搜索最短路径或最小公共祖先。如果之前找到的序号不止一个，就会调用参数为 Iterable<Integer> 的SAP方法
3. sap( )还需要把SAP数据结构返回的数字转换成对应的同义词集（**以号求词**）

### 输入数据的存储

* hypernyms，如上所述，存储为Diagraph。  
* synset中，按行读取数据，容易想到应该用一个 <行号,同义词集> 对应的数据结构（以号求词）。API中会用到单个的noun,所以一行中的同义词应该一个一个分开存储，即```<int,String[]>```形式。  
  * 最开始我用了二维数组 ```private ArrayList<String[]> synArray```，但是 Test 4: check sap() with random noun pairs 会出现 WARNING: the time limit of 180 seconds was exceeded, so not all tests could be completed.
  * 后来改成了 ```HashMap<Integer, String[]> synsetMap``` 就过了。
  * 就像上面说的，sap( )还需要进行 以号求词，而 HashMap的检索速度会更快。
  * 另外，如果不存储 以词求号 的数据结构，在 Test 1: check distance() with random noun pairs= 就会超时挂掉。
  
- [ ] *这么说起来，我都是随便写一份，然后依靠测试系统的反馈修改。实际上应该按照题目的时间空间要求，先确定算法的性能再下笔的……* 

## WordNet中图的合法性检测

要求WordNet中创建的Diagraph应该是一个rooted DAG。可以分成两部分来考虑：

1. rooted：根节点并不会指向其他节点。  
  可以在读取 hypernyms 的时候用boolean[ ]记录下其中都出现了哪些行（每行的第一个数都有上位词，不会是根）。  
  如果根节点的数量超过一个，就是非法的。
  - [ ] 很奇怪的，不能在根节点数量 = 0 的时候报错，这会导致前面的题目过不去。我觉得这种情况就相当于有环……？

2. DAG 无环有向图
  可以用 DirectedCycle 的hasCycle()，或者 [Topological](https://algs4.cs.princeton.edu/code/javadoc/edu/princeton/cs/algs4/Topological.html#hasOrder()) 的 hasOrder() 检测

 

# [Algorithms Part2 Week1 WordNet](https://www.coursera.org/learn/algorithms-part2/programming/BCNsp/wordnet)

编程顺序参考[FAQ](https://coursera.cs.princeton.edu/algs4/assignments/wordnet/faq.php)，先写SAP，再写WordNet，最后是Outcast

## 输入文件分析

### synset

```行号,noun1 noun2,释义```

一行中会有不止一个单词（noun），同一个单词也会出现在不同行。

读取的时候，使用In库的实例方法readLine()

```java
// 实例方法，需要用变量调用。
In in1 = new In(synsets);
String synLine = in1.readLine();

```

然后借助split(",")分离出行号和同义词们。split()方法返回的是String[]。  
行号要用静态方法Integer.parseInt()实现从String向int的转换，同义词之间是用空格分离。

```java
number = Integer.parseInt(synLine.split(",")[0]);   // 该行同义词集的序号
aux = synLine.split(",")[1];    // 同义词集合，每个noun用空格隔开
synsetMap.put(number, aux.split(" "));     
```

然后不断向下读取，当到达文件尾，readLine()会返回null，以此作为循环条件。

### hypernyms

```son,father1,father2```

这个文件中的每一行都是用逗号隔开的数字。其中后面的数字都是第一个数字所代表的同义词集的上位词，数字都是synset中的对应行号。

## SAP

第一版被示意图影响，借助Diagraph的adj[]实现遍历，只考虑了单父节点的简单情况。

但是实际上WordNet中的一个词可以

## WordNet中数据结构的选择

采用HashMap 而不是  ArrayList<String[]> 二维数组来实现

散列get搜索更快。比线性的数组。

## WordNet中图的有效性检测



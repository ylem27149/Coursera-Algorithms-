# Coursera-Algorithms-

配套书为算法（第四版）。  
中文正版电子书[链接](https://www.ituring.com.cn/book/875)，从这里下载的电子书有Mobi和pdf版本，pdf是可以搜索的，很方便。  
[英文书官网](https://algs4.cs.princeton.edu/home/)，可以免费看，但网站上的内容并没有包括实体书中的所有内容，只看这个可能会不好理解。  

看过书以后就可以直接做公开课上的练习了。assignment specification 和 FAQ 可以解决大部分问题。  

接下来简单梳理一下做过的习题的思路，更具体的分析在每个文件夹的README里。

# [Part 1](https://www.coursera.org/learn/algorithms-part1)

## Week 1:  UnionFind - Percolation
题目：  
一个区域，里面的格子有打开/关闭两种状态，水会从上方流进相邻的打开格子。
判断格子是否打开（isOpen)，格子里是否有水（isFull），水能否流入区域的最下方（percolates)。

UnionFind:   
Union：给两个元素创建连接/把两个元素放在同一个集合（连通分量connected component)里。  
Find:看两个元素是否连接在一起，包括与其他元素的间接连接/看两个元素是否属于同一个集合 。

UnionFind的API中没有要求返回两个元素之间的连接路径，所以可以不考虑具体连接方式，只考虑元素和集合。

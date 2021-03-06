# [Coursera-Algorithms-1.1 Percolation](https://www.coursera.org/learn/algorithms-part1/programming/Lhp5z/percolation)

2019.1.27 100分通过。

## 总的来说，程序中出现的bug有：

1. percolation中没有考虑backwash和n=1的情况  
2. 最主要的问题出在percolationstats中，都是很初级的失误：  
    1. uniform用两个参数时是前闭后开，没有考虑这一点导致程序实际上不可能产生导通的图。    
    2. threshold是double，计算的时候需要把分子分母转换类型，同时分母（n*n)的括号不能省略  
  
## 在抓bug中查到的，有助于进一步提高程序的资料：

1.Handling backwash with only ONE union-find object  
  * https://www.coursera.org/learn/algorithms-part1/discussions/weeks/1/threads/6beAB7P3Eeatew7zqUaXxg  
  * https://github.com/kristinpeterson/coursera_algorithms
  * I do not use virtual sites at all in my implementation. Instead, for each connected component, I maintain the information about whether it connects to top and whether it connects to bottom. I take care to update this information properly. This way, it passes all the tests, even the memory bonus test.  
  * Yes, a 2d array storing a custom data type is very likely the issue, though I can say for certain w/o seeing the code.    It's hard to give guidance while not giving the answer away but here is what I can say. I can't assume how you are approaching the problem but here are a couple hints as to the direction I went with my data structures.    Even though visually the percolation is a grid, from a data structure point of view it's not (i.e. 2d array is likely not needed).    You need a separate very simple data structure to track open sites.    To prevent backwash you need an other data structure to track a certain subset of connections. Fixing the backwash problem will very likely push you in the correct direction.  
  
 ## 改代码的时候的参考资料
  
  * http://www.voidcn.com/article/p-rssjxsom-zu.html
  * https://www.jianshu.com/p/e12322891407
 

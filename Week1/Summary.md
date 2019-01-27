# Coursera-Algorithms-Week1-
https://www.coursera.org/learn/algorithms-part1

2019.1.27 终于过了TUT

>ASSESSMENT SUMMARY
>Compilation:  PASSED
>API:          PASSED
>Spotbugs:     PASSED
>PMD:          PASSED
>Checkstyle:   FAILED (0 errors, 3 warnings)
>Correctness:  33/33 tests passed
>Memory:       8/8 tests passed
>Timing:       20/20 tests passed

Aggregate score: 100.00%

[Compilation: 5%, API: 5%, Spotbugs: 0%, PMD: 0%, Checkstyle: 0%, Correctness: 60%, Memory: 10%, Timing: 20%]


总的来说，程序中出现的bug有：

1. percolation中没有考虑backwash和n=1的情况

2.最主要的问题出在percolationstats中，都是很初级的失误：

  1.uniform用两个参数时是前闭后开，没有考虑这一点导致程序实际上不可能产生导通的图。（怪不得我去查OperationCountLimitExceededException报错基本得不到前车之鉴。
  2.threshold是double，计算的时候需要把分子分母转换类型，同时分母（n*n)的括号不能省略
  （真的都是好傻的bug啊。


在寻找bug的过程中，学到了：

1.尝试使用github来管理代码版本。现在的用法还很初级，毕竟需求也很初级。
2.ctrl+R 批量替换
3.运行程序：需要输入文件名，用快捷键操作的时候要带上后缀
4.if中加入
5.适当的使用final修饰符。
6.在程序中可以直接调用同一文件夹中的其他库。当被调用的程序修改后，在测试当前程序前，要把被调用程序再build一遍。

在抓bug中查到的，有助于进一步提高程序的资料：
1.Handling backwash with only ONE union-find object
  *https://www.coursera.org/learn/algorithms-part1/discussions/weeks/1/threads/6beAB7P3Eeatew7zqUaXxg
  *Vassilis Papadimitriou · 2 years ago
    I do not use virtual sites at all in my implementation. Instead, for each connected component, I maintain the information about whether it connects to top and whether it connects to bottom. I take care to update this information properly. This way, it passes all the tests, even the memory bonus test.
  *Yes, a 2d array storing a custom data type is very likely the issue, though I can say for certain w/o seeing the code.
    It's hard to give guidance while not giving the answer away but here is what I can say. I can't assume how you are approaching the problem but here are a couple hints as to the direction I went with my data structures.
    Even though visually the percolation is a grid, from a data structure point of view it's not (i.e. 2d array is likely not needed).
    You need a separate very simple data structure to track open sites.
    To prevent backwash you need an other data structure to track a certain subset of connections. Fixing the backwash problem will very likely push you in the correct direction.
  
改代码的时候的参考资料

*http://www.voidcn.com/article/p-rssjxsom-zu.html
*https://www.jianshu.com/p/e12322891407
*https://github.com/kristinpeterson/coursera_algorithms

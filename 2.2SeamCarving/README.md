# [Algorithms Part 2 - Week 2 SeamCarving](https://www.coursera.org/learn/algorithms-part2/programming/cOdkz/seam-carving/submission)

这周的作业概念上很清晰，算法也不难。就是对Picture的每个像素实现relax()，找到最短路径。

可能要注意的点：

1. 行列坐标：Picture调用像素是（列column,行row），而平常习惯用（行，列）来调用二维数组，可能会造成一定的混淆。column < width(), row < height()  。  
2. relax()：    
   * 最开始我实现的是对一行每个像素从下往上搜索最短路径，然后找出最短的。但是这样写timing测试无法满分通过。  
   * 后来[参考](http://www.shicheng0829.cn/index.php/2018/11/28/%E3%80%90algorithms-part-ii%E3%80%91week2-seam-carving/),采用对第二行开始的每个像素从下往上寻找到该像素的最短路径。
  
 中间尝试过的优化：
 
 1. 把实例变量Picture改成存储每个像素getRGB()的int[][]。实现后 memory test过了。91->93  
 
 2. 把energy[][]做成实例变量。这个实现起来有点麻烦，并且不需要这么做也能满分过。  
    1. transpose 的时候也要把 energy[][] 转一下。注意width和height在这个过程中到底指的是原图的参数还是旋转后的图的参数
    2. 在removeSeam中，在被去除的像素点附近的点的energy值需要重新计算，不能直接复制，因为这些点附近的点已经跟之前不一样了
    
3. 如果实现了前两点，那么在removeSeam中可以采用System.arraycopy来进行数组的复制。为了能实现这一点，我的数组都是写成[height][width]

4. 在energy中不引入Color变量，直接用位操作得到getRGB中的Red, Blue, Green 分量  

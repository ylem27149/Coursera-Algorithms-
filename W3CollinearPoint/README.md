# [Week 3 CollinearPoint](http://coursera.cs.princeton.edu/algs4/assignments/collinear.html)

给出的代码可以满分过，没有做附加题

## 关键点

* 像这种不准用ArrayList，需要自己调整数组大小的，输出数组时需要重新创建一个数组，去掉原数组中多余的空间。  
  * 实例变量数组为了不反复Resize，扩大的时候都是直接double的，后面会有不少null元素  
  * clone()只能深拷贝一维数组。
  * 这种 Point[] pointcopy = points; 只是多创建了个指针，并没有创建副本。
  
* 快速实现中，先用点的大小排序，因为Arrays.sort()所用的归并和插入排序都是稳定的，此后再用斜率排序，点还是会按从大到小排列，能用来过滤重复线段
  
* 要返回Comparator的变量，定义一个内部类实现Comparator接口 
  * public Comparator<Point> slopeOrder()的写法。[参考](https://stackoverflow.com/questions/39677697/return-a-comparator-from-another-function)：
  
```
public Comparator<Point> slopeOrder() {
return new Comparator<Point>() 
{
@Override
public int compare(Point o1, Point o2) {

return Double.compare(slopeTo(o1), slopeTo(o2));
}
};
```

或者lamda表达式(不懂，先存着）

```
public Comparator<Point> slopeOrder()              // compare b points by slopes they make with this point
    {
        return (o1, o2) -> Double.compare(slopeTo(o1),
                                          slopeTo(o2));
    }
```


## 参考材料

* https://blog.csdn.net/OldDreamHYN/article/details/61930128 

这份有注释，仔细的看了一遍，是增加了一个变量记录同斜率数的点的数量。它的去除重复的方法大概是（没仔细看）：按数组排序的时候并没有去除已经排序过的节点，通过查找共线的点的最小点是否与参考点相同，来判断是不是重复。
因为没有去除已经排序过的节点，并不会产生子集，每次都是所有的共线的点都会被查到。它的点在按斜率排序以前，先用大小排序了。那么就只有第一次查找到共线点时候的参考点=所有点中的最小点。

* https://blog.csdn.net/littlehaes/article/details/79246041

* https://blog.csdn.net/liuweiran900217/article/details/19285111

这个有分析思路，但是查共线的两个算法没有按照API写

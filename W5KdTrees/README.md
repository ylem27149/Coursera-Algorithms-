# [Week 5 KdTrees](https://www.coursera.org/learn/algorithms-part1/programming/wuF0a/kd-trees)

## 参数变量的值传递

Java中的参数变量都是复制一份值传入方法中。根据参数变量是原始类型、还是引用类型，又可以分成两种情况：

1. 原始类型

原始类型的变量在方法中的改变，无法在外部通过传入的变量名调用。  
如果把改变后的变量作为参数，递归的调用该方法，当回退到原先的方法中时，会出现问题：

如下所示，如果把minSquDistance 作为自定义的辅助方法nearest()的参数，当搜索完一边的子节点，回退到父节点然后再去搜索另一边子节点时，传入第二个子节点的最小距离仍然是在父节点计算得到的最小距离，不会被前一个子节点的搜索结果更新。

```
 public Point2D nearest(
            Point2D
                    p)             // a nearest neighbor in the set to point p; null if the set is empty
    {
        double minSquDistance;
        if (p == null) throw new IllegalArgumentException();
        if (root.point == null) return null;

        minSquDistance = root.point.distanceSquaredTo(p);   // 当root有点时,distance进行初始化
        Node minNode = new Node();
        minNode.point = root.point;
        nearest(minSquDistance, root, p, 'x', minNode);
        return minNode.point;


    }
    
     private void nearest(double minSquDistance, Node searchNode, Point2D point, char ori,
                         Node minNode) 
```
    
   2. 引用类型
   
   引用类型中存储的是对对象的引用，所以在方法中对对象的修改，可以在外部通过传入的变量名调用。
   
   但是，在方法中不能修改传入的引用变量。也就是说，在方法内修改了引用变量指向的对象，这种改变是不会同步出去的。
   
 例如，如果在nearest()中用Point作为一个参数来记录搜索到的最小点，就无法达到效果。
   
```diff
   // 错误例子
   
     private void nearest(double minSquDistance, Node searchNode, Point2D point, char ori,
                         Point minPoint) 
if (searchNode.point.distanceSquaredTo(point) < minSquDistance) { //更新最小距离
                minSquDistance = searchNode.point.distanceSquaredTo(point);
-                minPoint = searchNode.point;
 }
```
 
 在最后一行代码中，修改了传入的minPoint指向的对象，这种改变无法在外部调用。
 
```diff
 //正确例子
  private void nearest(double minSquDistance, Node searchNode, Point2D point, char ori,
                         Node minNode) 
                         
  if (searchNode.point.distanceSquaredTo(point) < minSquDistance) { //更新最小距离
                minSquDistance = searchNode.point.distanceSquaredTo(point);
+               minNode.point = searchNode.point;
                }
```
最后一行代码改变了minNode指向的对象的point值，minNode仍然和传入的变量指向同一个对象。这种改变可以在外部调用。

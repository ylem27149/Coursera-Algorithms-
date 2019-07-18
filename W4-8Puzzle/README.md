# [8-Puzzle](http://coursera.cs.princeton.edu/algs4/assignments/8puzzle.html)  

给出的代码可以100分通过，没有做附加题。

以下为题意分析。

```
    1  3        1     3        1  2  3        1  2  3        1  2  3    
 4  2  5   =>   4  2  5   =>   4     5   =>   4  5      =>   4  5  6    
 7  8  6        7  8  6        7  8  6        7  8  6        7  8     
 
 initial        1 left          2 up          5 left          goal 
 ```
 
## Board

3*3的格子( 3-by-3 grid )，填入数字0~8，0代表blank block，实际不算个格子  
* twin 要交换两个非零的格子  
* neighbors 是零格子和上下左右格子（如果存在的话）交换 
 
 ### Distance
 
 只计算前八格
 
 ```
 8  1  3        1  2  3     1  2  3  4  5  6  7  8    1  2  3  4  5  6  7  8  
 4     2        4  5  6     ----------------------    ----------------------    
 7  6  5        7  8        1  1  0  0  1  1  0  1    1  2  0  0  2  2  0  3    

 initial          goal         Hamming = 5 + 0          Manhattan = 10 + 0  
 ```
 
 **Should the hamming() and manhattan() methods in Board return the Hamming and Manhattan priority functions, respectively?**   
 No.You will compute the priority function in Solver by calling hamming() or manhattan() and adding to it the number of moves. 
 
 ### API
 
 Organize your program by creating an immutable data type Board with the following API:
 
 ```java
 public class Board {
    public Board(int[][] blocks)           // construct a board from an n-by-n array of blocks
                                           // (where blocks[i][j] = block in row i, column j)
    public int dimension()                 // board dimension n
    public int hamming()                   // number of blocks out of place
    public int manhattan()                 // sum of Manhattan distances between blocks and goal
    public boolean isGoal()                // is this board the goal board?
    public Board twin()                    // a board that is obtained by exchanging any pair of blocks
    public boolean equals(Object y)        // does this board equal y? checklist上有参考
    public Iterable<Board> neighbors()     // all neighboring boards.checklist上说用Stack or Queue
    public String toString()               // string representation of this board (in the output format specified below) checklist上有参考

    public static void main(String[] args) // unit tests (not graded)
}
```

* Corner cases.  You may assume that the constructor receives an n-by-n array containing the n2 integers between 0 and n2 − 1, where 0 represents the blank square.

 * Performance requirements.  Your implementation should support all Board methods in time proportional to n2 (or better) in the worst case.  

 
 ## Solver
 
 ### API
 
 Also, create an immutable data type Solver with the following API:
 
 ```java
 public class Solver {
    public Solver(Board initial)           // find a solution to the initial board (using the A* algorithm)
    public boolean isSolvable()            // is the initial board solvable?
    public int moves()                     // min number of moves to solve initial board; -1 if unsolvable
    public Iterable<Board> solution()      // sequence of boards in a shortest solution; null if unsolvable
    public static void main(String[] args) // solve a slider puzzle (given below)
}
```
 
 ### SearchNode
 
 实例变量：  
 * Board  
 * predecessor
 * moves, neighbors.moves = 当前moves+1
 * Priority = ManhanttanDistance + moves
 
 Priority function: implements Comparable 接口。用这个数据结构作为元素，实现minPQ的大小判断 
 * 在整个求解过程中，delMin取出的Node.Priority是不会减小的（见[Checklist](http://coursera.cs.princeton.edu/algs4/checklists/8puzzle.html)）所以必须要把predecessor从neighbors中去除，它不仅会造成冗余，还可能会卡住求解过程。
 * 另外，如果两个SearchNode.priority相等，比较它们的Manhattan distances（这个是从网上找的参考代码学到的）
    * 如果遇到priority相等moves不同的两批neighbors，这时候应该取出更新的，距离目标更近的，也就是moves大ManhanttanDistance小的一个。  
 
 
### 求解过程
 
 1. 在minPQ中插入初始SearchNode（0 moves, null predecessor)  
 2. delMin，得到求解过程的下一个SearchNode。然后在minPQ插入得到的SearchNode.board.neighbors  
 3. 当取出的SearchNode.board.isGoal()，用取出的节点的predecessor不断回溯，得到最短路径。过程中记录delMin得到的可能并不是解。  
    * 所以应该用stack存储solution  
    *  如果遇到同一批（Moves相同）board具有相同manhattanDistance的情况，可能会有错误的尝试。即searchPQ.delMin()移动后（neighbors board)，反而增大了manhanttan distance。这时候delMin（）就会回去试上一批的相同distance的其他board，这种可能的错误尝试，导致一路上的delMin()并不等于solution
 

### 可解性
 
 Board和它的twinBoard，有且只有一个可解。
 
 To apply the fact, run the A* algorithm on two puzzle instances（应该是指Board)—one with the initial board and one with the initial board modified by swapping a pair of blocks—in lockstep (alternating back and forth between exploring search nodes in each of the two game trees). Exactly one of the two will lead to the goal board.  

Can I put the logic for detecting whether a puzzle is infeasible in Board instead of Solver? There is a elegant algorithm for determining whether a board is solvable that relies on a parity argument (and occasionally we change our API to require this solution). However, the current API requires you to detect infeasiblity in Solver by using two synchronized A* searches (e.g., using two priority queues).  

 ### [checklist](http://coursera.cs.princeton.edu/algs4/checklists/8puzzle.html)
 
 1. Solution  
 How do I reconstruct the solution once I've dequeued the goal search node?   
 Since each search node records the **predecessor** search node to get there, you can chase the pointers all the way back to the initial search node (and consider them in reverse order).
 
 2. The A* algorithm (with the Manhattan priority function) will struggle to solve even some **4-by-4** instances.  
 3. 使用 char 数组  
 How can I reduce the amount of memory a Board uses?  
 For starters, recall that an n-by-n int[][] array in Java uses about 24 + 32n + 4n^2 bytes; when n equals 3, this is 156 bytes. To save memory, consider using an n-by-n char[][] array or a length n^2 char[] array. You could use a more elaborate representation: since each board is a permutation of length n^2, in principle, you need only about lg ((n^2)!) bits to represent it; when n equals 3, this is only 19 bits.
 
 4. Use a 1d array instead of a 2d array   
 5. Use only one PQ to run the A* algorithm on the initial board and its twin  
 6. Exploit the fact that the difference in Manhattan distance between a board and a neighbor is either −1 or +1.
 
 #### 参考资料
 
 * https://blog.csdn.net/u011256637/article/details/52550570   
 这个没写可解性判断的代码。  
 * https://blog.csdn.net/qq_29672495/article/details/81297748  
这份是在solver构造函数中就放进twinboard，然后两个同步跑，等其中一个跑出结果就结束循环。  
用currentNode.getBoard().isGoal()来判断是否可解。因为构造函数总是先运行的，调用isSolverable的时候，currentNode肯定已经跑出以下while循环了。currentNode要么是最后一个结点（所以它的move就是总move）要么就不可解。  
这份的存储没达标  
* https://github.com/zysite/Algorithms  
这份是满分过的。  
* https://blog.csdn.net/zhangyuzuishuai/article/details/68167420  
    * 这份把两个插入了同一个minPQ，在Node里加了一个boolean来判断是初始序列还是twin。
    * 实现的时候有一个提醒，那就是不要把优先队列作为成员变量（实力变量），否则内存使用会爆炸  
    * 还有用一维数组代替二维数组也会改善空间的使用。
 

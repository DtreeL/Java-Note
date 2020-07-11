# 数据结构

## 数据结构描述

==数据项==相当于表中的一条==记录==，是数据元素不可分割的最小单位

==数据元素==相当于==表==，由一个个的数据项组成。

- 逻辑结构

  ![逻辑结构](https://note.youdao.com/yws/api/personal/file/B907B0888FA94B24ACADF1EC30E0730D?method=download&shareKey=328f97da21789d89a36f8e80ae055505)

  - 线性结构：一对一

  - 树形结构：一对多

  - 图结构：多对多

- 存储结构
  - 顺序存储结构（数组）：数组较容器，最大的优点就是效率。

    - 数组对象的对象头里有一个_length字段，记录数组长度。

    ```java
    new int[3];
    new int[3]{1,2,3};
    
    静态初始化：固定了
    动态初始化：只有长度没有值
    ```

  - 链式存储结构（链表）

- 数据的运算

### 复杂度

时间复杂度：一个算法中的语句执行次数称为语句频度或**时间频度**。记为T(n)，T(n)取最高次方的1系数为时间复杂度O()

- ```
  O(1)<O(log2^n)<O(n)<O(nlog2^n)<O(n^2)<O(n^3)<O(2^n)<O(n!)<O(n^n)
  ```

空间复杂度：一个算法在运行过程中临时占用存储空间大小的一个量度。S()衡量

## 线性结构

### 顺序表

数组作为内部存储

### 链表

链表由结点组成，所以结点可以作为链表的内部类

- 一个结点存储着数据和其他结点的信息
- 结点作为链表的内部类，则属于链表的有头指针和个数

结点

```java
public class Node<T> {
    //节点的数据
    private T data;
    //指向的下一个节点
    public Node<T> next;
    
    public Node(T data) {
        this.data = data;
    }

    public Node() {}
    public T getData() {
        return data;
    }
    private void setData(T data) {
        this.data = data;
    }
}
```

单向链表

```java
class LinkedList<T>{
    private Node head;
    private int size;
    
    public LinkedList(){
        head = new Node();
    }
}
```

### 栈

数组实现和链表实现，只不过添加删除需要满足先进后出

- 栈顶指针

表达式计算：后缀表达式1463*-2/+。遇见数字就入栈，遇见符号就出栈，然后把结果再入栈。

### 队列

数组实现和链表实现，只不过添加删除需要满足先进先出

- 头尾指针

#### 普通队列(一次性队列)

```java
class ArrayQueue<T>{
    private T[] arr;
    private int maxSize;
    //头尾指针
	private int font;
    private int rear; 
    
    // 创建队列
    public ArrayQueue(int maxSize){
        this.maxSize = maxSize;
        arr = new T[maxSize];
        font = -1;
        rear = -1;
    }
    
    //判断功能
    //1. 是否满
    public boolean isFull(){
        return rear = maxSize - 1;
    }
    //2. 是否空
    public boolean isEmpty(){
        return font == rear;
    }
    
    //进队，添加
    public boolean add(T element){
        if(isFull()){
            System.out.println("队满，不能进队");
            return;
        }
        arr[++rear] = element;
    }
    
    //出队，删除
    public T get(){
        if(isEmpty()){
            throw new RuntimeExpection("队空，不能出队");
        }
        return arr[font++];
    }
}
```

#### 环形队列

font指向队头元素

rear指向队尾元素的下一个位置，留出一个空位以便循环复用

```java
class ArrayQueue<T>{
    private T[] arr;
    private int maxSize;
    //头尾指针
	private int font;
    private int rear; 
    
    // 创建队列
    public ArrayQueue(int maxSize){
        this.maxSize = maxSize;
        arr = new T[maxSize];
    }
    
    //判断功能
    //1. 是否满
    public boolean isFull(){
        return font == (rear+1) % maxSize;
    }
    //2. 是否空
    public boolean isEmpty(){
        return font == rear;
    }
    
    //进队，添加
    public boolean add(T element){
        if(isFull()){
            System.out.println("队满，不能进队");
            return;
        }
        arr[rear] = element;
        rear = (rear+1) % maxSize;
    }
    
    //出队，删除
    public T get(){
        if(isEmpty()){
            throw new RuntimeExpection("队空，不能出队");
        }
        T value = arr[font];
        font = (font+1) % maxSize;
        return value;
    }
    
    //队列中有多少个数据
    public int getSize(){
        return (rear+maxSize - front) % maxSize;
    }
}
```



## 稀疏数组（压缩存储）

> 矩阵中大部分是0时，没有规律
>
> 记录数据为：行列值

一维数组代表行，`[0][]`记录整个棋盘的情况，几行几列，有几个非零数字

一维数组中的一维数组记录行列中非零值的情况。第一行/列是数组的0下标

---

棋盘游戏

- 棋盘是个二维数组，落子为值（如白子为,1，黑子为2），未落子为0值、
- 游戏有存盘和续盘功能
  - 退出时，二维数组-->稀疏数组存储
  - 续盘时，稀疏数组-->二维数组

---

二维数组-->稀疏数组

1. 遍历二维数组，得到有效数据的个数sum
2. 根据sum就可以创建稀疏数组 `sparseArr int[sum+1][3]`
3. 将二维数组的有效数据存入稀疏数组

```java
// 原始的二维数组
int[][] chessArr = new int[11][11];
chessArr[1][2] = 1;
chessArr[2][3] = 2;
chessArr[4][5] = 2;

// 1. 先遍历二维数组 得到非0数据的个数,遍历行，行里面遍历列
int sum = 0;
for (int i = 0; i < chessArr.length; i++) {
    for (int j = 0; j < chessArr[i].length; j++) {
        if (chessArr1[i][j] != 0) {
            sum++;
        }
    }
}

// 2. 创建对应的稀疏数组
int[][] sparseArr = new int[sum + 1][3];
// 给稀疏数组记录整个统计数据的行赋值
sparseArr[0][0] = chessArr.length;

//每一行的列数都是相同的
sparseArr[0][1] = chessArr[1].length;
sparseArr[0][2] = sum;

// 3. 遍历二维数组，将非0的值存放到 sparseArr中
//count 用于记录是第几个非0数据，每个非零数据占一行
int count = 0;
for (int i = 0; i < chessArr.length; i++) {
    for (int j = 0; j < chessArr[i].length; j++) {
        if (chessArr1[i][j] != 0) {
            count++;
            sparseArr[count][0] = i;
            sparseArr[count][1] = j;
            sparseArr[count][2] = chessArr1[i][j];
        }
    }
}
```

稀疏数组-->二维数组

1. 先读取稀疏数组的第一行，根据第一行创建二维数组
2. 再读取稀疏数组后面的行数据，并赋予给原始的二维数组即可

```java
//1. 先读取稀疏数组的第一行，根据第一行的数据，创建原始的二维数组
int [][]chessArr2 = new int[sparseArr[0][0]][sparseArr[0][1]];

//2. 在读取稀疏数组后几行的数据(从第二行开始)，并赋给 原始的二维数组 即可
for(int i = 1; i < sparseArr.length; i++) {
    chessArr2[sparseArr[i][0]][sparseArr[i][1]] = sparseArr[i][2];
}
```

## 广义表

## 布隆过滤器

加快判定一个元素是否在集合中出现的方法

第一步：建立一个32亿二进制（比特），也就是4亿字节的向量。全部置0。

## 非线性结构

### 树

#### 二叉查找树

左小右大（利用二分法查找）

- 查找时最差也是二叉树的高度次
- 插入也是一层一层比大小找到合适的位置插入

#### 二叉平衡树

任一一个结点的左子树和右子树的高度差不能大于1，当插入破坏平衡时需要旋转来调整。

LL型（右旋）：左孩子的左子树插入一个节点导致不平衡

- 左子树无孩子：向右旋转成为左孩子的右孩子
- 左子树有孩子：右旋，左孩子上位了，左孩子的右子树成为根结点的左子树作为补偿

RR型调整（左旋）：右孩子的右子树插入一个结点导致不平衡

LR型：右子树的根结点当老大，孩子分配，左子树作为右子树，右子树作为左子树

RL型：L当老大，一样的处理

#### 红黑树

> 解决二叉查找树多次插入新结点导致的不平衡

红黑树是一种自平衡二叉树

- 结点颜色为红和黑
- 根结点和叶子结点都是黑色
- 红结点的孩子都是黑色
- 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点。

按照二叉查找树的方式插入，打破红黑树的规则时需要调整，调整方式分为

- 变色
- 旋转

两种方式综合使用达到平衡效果

---

变色：会产生连锁反应，插入结点不变颜色

左旋转：向左旋转，右孩子取代父结点，作为补偿把左孩子给原来的父结点作为右孩子

#### B树

二叉查找树结构，查找的时间复杂度 O(log2-N) 与树的深度相关，降低树的深度会提高查找效率

- 于是有了多路的B-tree/B+-tree/ B*-tree (B~Tree)。

B树也就是B-树，B树的英文是B-tree，很多地方直译成了B-树，其实B-树和B树是同一种树。

#### 总结

##### B-树：

多路搜索树，每个结点存储M/2到M个关键字，非叶子结点存储指向关键

字范围的子结点；

所有关键字在整颗树中出现，且只出现一次，非叶子结点可以命中；

##### B+树：

在B-树基础上，为叶子结点增加链表指针，所有关键字都在叶子结点

中出现，非叶子结点作为叶子结点的索引；B+树总是到叶子结点才命中；

##### B*树：

在B+树基础上，为非叶子结点也增加链表指针，将结点的最低利用率

从1/2提高到2/3；

#### LSM树

**B+树最大的性能问题是会产生大量的随机IO**，随着新数据的插入，叶子节点会慢慢分裂，逻辑上连续的叶子节点在物理上往往不连续，甚至分离的很远，但做范围查询时，会产生大量读随机IO。

把一颗大树拆分成N棵小树， 它首先写入到内存中（内存没有寻道速度的问题，随机写的性能得到大幅提升），在内存中构建一颗有序小树，随着小树越来越大，内存的小树会flush到磁盘上。当读时，由于不知道数据在哪棵小树上，因此必须遍历所有的小树，但在每颗小树内部数据是有序的。

### 并查集



### 图

#### BFS

BFS的概念，即一层一层的遍历。在使用BFS解决问题的时候最先想到的方式应该是**队列（Queue，FIFO）**

#### DFS

使用DFS解决问题时最先想到的应该是**递归和栈（Stack）**

---

图和树的最大区别在于图的下一个节点可能指向已访问过的节点。因此在使用BFS及DFS遍历时，**应维护一个Set，Set中存放已被访问过的节点，在遍历时先判断节点未被访问过再遍历即可。**

# 算法

## 递归

1. 明确方法的功能，输入和输出
2. 寻找递归结束条件：即把规模压缩到最小可明确得到答案时
3. 寻找函数等价关系式

### 寻找函数等价关系式

青蛙跳台阶，一次跳1或者2阶，跳n阶时有几种跳法

1. 明显只有一阶时只有一种跳法

2. 每次跳时青蛙有两种跳法
   - 第一种，跳了一阶，还剩n-1个，那么剩下的跳法有`f(n-1)`种
     - 本来有`f(n)`种，跳了1阶后，规模减小1
   - 第二种，跳了两阶，还剩n-2个，那么剩下的跳法有`f(n-2)`种
     - 本来有`f(n)`种，跳了2阶后，规模减小2
3. 跳法即两种之和
4. 关注边界条件

### 优化

使⽤用递归的时候，必要 须要考虑有没有重复计算，如果重复计算了了，一定要把计算过的状态保存起来

- ⽤用什么保存呢？可以⽤用数组或者 HashMap 保存

```java
//先判断有没计算过
if(arr[n] != -1){
	//计算过，直接返回
	return arr[n];
}else{
	// 没有计算过，递归计算,并且把结果保存到 arr数组⾥里里
	arr[n] = f(n-1) + f(n-1);
	reutrn arr[n];
}
```

---

考虑是否可以⾃自底向上

当 n ⽐比较⼤大的时候，例例如当 n = 10000 时，那么必须要往下递归10000层直到 n <=1 才将结果慢慢返回，如果n太⼤大的话，可能栈空间会不不够⽤用。

知道f(1) = 1;f(2) = 2;

那么我们就可以推出 f(3) = f(2) + f(1) = 3。从⽽而可以推出f(4),f(5)等直到f(n)。因此，我们可以考虑使⽤用⾃自底向上的⽅方法来取代递归，代码如下：

```java
public int f(int n) {
	if(n <= 2)
		return n;
	int f1 = 1;
	int f2 = 2;
	int sum = 0;
    
    for (int i = 3; i <= n; i++) {
		sum = f1 + f2;
		f1 = f2;
		f2 = sum;
	}
	return sum;
}
```

## 动态规划

把原问题分解成子问题进行求解，也就是分治的思想。

> 公司要挑选出三个最优秀的员工。一般高级经理会跟手下的经理说，你去把你们那边最优秀的3个人报给我，经理又跟总监说你把你们那边最优秀的人报给我，经理又跟组长说，你把你们组最优秀的三个人报给我，这个其实就动态规划的思想！

利利⽤用历史记录，来避免我们的重复计算。

- 而这些历史记录，得需要一些变量量来保存，一般是⽤用一维数组或者二维数组来保存

1. 定义数组元素的含义，数组dp[i]表示什么呢？
2. 找出数组元素之间的关系式：递归关系式
3. 找初始值：递推初始值

### 一维DP

青蛙跳台阶：

1. 定义数组含义

   - 问题是要求⻘青蛙跳上 n 级的台阶总共由多少种跳法，那我们就定义 dp[i] 的含义为：跳上一个 i 级的台阶总共有 dp[i] 种跳法，能算出dp[n]则问题解决

2. 关系式

   由于情况可以选择跳一级，也可以选择跳两级，所以青蛙到达第 n 级的台阶有两种方式

   - 一种是从第 n-1 级跳上来
   - 一种是从第 n-2 级跳上来
   - 由于我们是要算所有可能的跳法的，所以有 dp[n] = dp[n-1] + dp[n-2]。

3. 初始条件

```java
int f( int n ){
	if(n <= 1)
		return n;
	
    // 先创建⼀一个数组来保存历史数据
	int[] dp = new int[n+1];
	
    // 给出初始值
	dp[0] = 0;
	dp[1] = 1;
    dp[2] = 2;
	
    // 通过关系式来计算出 dp[n]
	for(int i = 3; i <= n; i++){
		dp[i] = dp[i-1] + dp[i-2];
	}
	
    // 把最终结果返回
	return dp[n];
}
```

### 二维数组的DP

> 一个机器器⼈人位于⼀一个 m x n ⽹网格的左上⻆角 （起始点在下图中标记为“Start” ）。
>
> 机器器⼈人每次只能向下或者向右移动⼀一步。机器器⼈人试图达到⽹网格的右下⻆角（在下图中标记为“Finish”）。
>
> 问总共有多少条不不同的路路径？

1. 定义数组元素含义

   - 目的是从左上⻆角到右下⻆角一共有多少种路径，那就定义 dp[i] [j]的含义为：当机器人从左上⻆走到(i, j) 这个位置时，一共有 dp[i] [j] 种路径。那么，dp[m-1] [n-1] 就是我们要的答案。

2. 找出关系数组元素间的关系式

   机器人要怎么样才能到达 (i, j) 这个位置？

   - 由于机器器⼈人可以向下⾛或者向右⾛，所以有两种方式到达

     一种是从 (i-1, j) 这个位置⾛走一步到达

     一种是从(i, j - 1) 这个位置⾛走⼀一步到达

   - 所以关系式是 dp[i] [j] = dp[i-1] [j] + dp[i] [j-1]

3. 找出初始值

   - 当 dp[i] [j] 中，如果 i 或者 j 有一个为 0时，关系式无法使用这个时候把i - 1 或者 j - 1，就变成负数了，数组就会出问题了

4. ```java
   public static int uniquePaths(int m, int n) {
   	if (m <= 0 || n <= 0) {
   		return 0;
   	}
   	int[][] dp = new int[m][n]; //
   	// 初始化
   	for(int i = 0; i < m; i++){
   		dp[i][0] = 1;
   	}
   	for(int i = 0; i < n; i++){
   		dp[0][i] = 1;
   	}
       
   	// 推导出 dp[m-1][n-1]
   	for (int i = 1; i < m; i++) {
   		for (int j = 1; j < n; j++) {
   			dp[i][j] = dp[i-1][j] + dp[i][j-1];
   		}
   	}
   	return dp[m-1][n-1];
   }
   ```

### 优化

80% 的动态规划题都可以画图，其中 80% 的题都可以通过画图一下子知道怎么优化

## 排序

内部排序：所有数据加载进内存中进行排序

外部排序：数据量过大，需要借助外部存储（文件等）进行排序

![](https://gitee.com/DtreeL/Image-Hosting-Service/raw/master/img/20200510172345.png)

### 交换排序

#### 冒泡排序

> 冒泡排序是稳定的，主要原因是：**俩俩比较的时候，没有对相等的数据进行交换(因为没必要)**。
>
> 我们对一群人按年龄排序，但是人除了年龄属性还有身高体重属性，在年龄相同时如果不想破坏原先身高体重的次序，就必须用稳定排序算法.

思想：最值冒出来，一轮冒出一个未排序序列的最值，就像一个称，依次对比两个元素，按规则确定是否需要交换。

传统的冒泡排序法不管有序还是没序，闭着眼睛循环比较了再说。

```java
public static void sort(int arr[]){
    //1. 得到多少个最值才能有序？因为冒泡的原因，arr.length-1个即有序，最后一个数自动有序
    for( int i = 0;i < arr.length - 1 ; i++ ){
    
        //假设数组已经有序
        boolean isSort = true;
        
        //2. 得出一个最值需要比较多少次？第一轮从头到尾称，第二轮因为已经有一个有序的数，所以是第一轮称的次数减1，第二轮减2，依次类推，根据与i的关系得出j的判断条件
        for( int j = 0;j < arr.length - 1 - i ; j++ ){
            int temp = 0;
            
            //3. 相邻的两个数比较确定是否交换
            if(arr[j] < arr[j + 1]){
                temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
                
                //发生了交换就代表数组是无序的，如果有序的话，在从头到尾称过一次后，都无法触发交换步骤。
                isSort = false;
            }
        }
        //4. 称一轮结束后如果没有发生交换代表数组已经有序，有序则退出程序。
        if(isSort){
            break;
        }
    }
}
```

#### 快速排序*

**在数组中找一个支点(任意),经过一趟排序后，支点左边的数都要比支点小，支点右边的数都要比支点大！**

一轮之后左边的排好序，右边的也排好序就有序了，采用同样的方法，使用递归

```java
 /**
     * 快速排序
     *
     * @param arr
     * @param L   指向数组第一个元素
     * @param R   指向数组最后一个元素
     */
    public static void quickSort(int[] arr, int leftIndex, int rightIndex) {
        //左边索引
        int i = leftIndex;
        //右边索引
        int j = rightIndex;

        //支点值
        int value = arr[(i + j) / 2];

        //左右两端进行扫描，只要两端还没有交替，就一直扫描
        while (i <= j) {

            //寻找直到比支点大的数
            while (pivot > arr[i]) {
                i++;
            }
            //寻找直到比支点小的数
            while (pivot < arr[j]) {
                j--;
            }
            
            //此时已经分别找到了比支点小的数(右边)、比支点大的数(左边)，它们进行交换
            if (i <= j) {
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
                
                // 两端继续推进
                i++;
                j--;
            }
        }
        //上面一个while保证了第一趟排序支点的左边比支点小，支点的右边比支点大了。


        //“左边”再做排序，直到左边剩下一个数(递归出口)，j是往左边走的，所以是右边界
        if (leftIndex < j)
            quickSort(arr, leftIndex, j);

        //“右边”再做排序，直到右边剩下一个数(递归出口),i是往右边走的，所以是左边界
        if (i < rightIndex)
            quickSort(arr, i, rightIndex);
    }
```

### 选择排序

#### 简单选择排序

O(n^2) 稳定。一次选一个最值

1. 首先在未排序的数列中找到最小(or最大)元素，然后将其与数列的起始位置的数交换位置；
2. 接着，再从剩余未排序的元素中继续寻找最小(or最大)元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

```c
public static void sort(int arr[]){
    // 排除异常情况
    if (arr == null || arr.length <= 0) {
        return null;
    }
    
    //1. 需要选几个最值？arr.length-1个，剩下的自动有序
    for( int i = 0;i < arr.length-1 ; i++ ){
        
        //数组只要找到索引即可
        //找到的最值放在第一个位置、第二个位置...与i同步,并且与每一轮设定最小值同步
        int min = i;
        
        //2. 找一个最值需要比较多少轮？从设定的最小值的后一个开始与最小值比较，一直比到最后一个，才能确定得到一个最值
        for(int j = i + 1;j < arr.length ; j++ ){
            if(arr[j] < arr[min]){
                //找到最值进行最值索引更新
                min = j;
            }
        }
        //3. 一轮之后出现最值，进行交换
        int temp = arr[i];
        arr[i] = arr[min];
        arr[min] = temp;
    }
}
```



#### 堆排序*

### 插入排序

#### 直接插入排序

思想：像抓扑克牌一样进行排序，分为有序区和无序区，把无序区里的插入到有序区中适当的位置，初始时把第一个数当作有序区

适用：小规模数据需要排序，并且初始数据几乎是有序的

```java
public static void sort(int[] arr) {
	//1. 默认0位置为有序数。从1-arr.length-1都往有序数组中插入
    for (int i = 1; i < array.length; i++) {

    	// 取出要插入的值，方便后面插入
        int value = array[i];

         // 2. 从前一个位置开始比较
         int j = i - 1;

         
         while (j >= 0 && array[j] > value) {
         	//大的数往后退一个位置
            array[j+1]= array[j];
            j--;
         }
         //退出循环说明找到了合适的位置。此时array[j] 不再大于 value，在合适的位置中插入数值
         array[j+1] =value;
   }
}
```

#### 折半插入排序

二分查找

```java
public static int binary(int[] arr, int data) {
    int min = 0;
    int max = arr.length - 1;
    int mid;
    while (min <= max) {
        mid = (min + max) / 2;
        if (arr[mid] > data) {
            max = mid - 1;
        } else if (arr[mid] < data) {
            min = mid + 1;
        } else {
            return mid;
        }
    }
    return -1;
}

问题出在第 6 行代码处：mid = (min + max) / 2;
--在 min 和 max 很大的时候，会出现溢出的情况，从而导致数组访问出错。
    
一般的做法是这样的：将加法变成减法。

// 防止溢出
 mid =  min + (max - min) / 2;

官方的二分搜索法的实现写法：使用 位运算。
// 无符号位运算符的优先级较低，先括起来
mid =  min + ((max - min) >>> 1);
```



#### 希尔排序

```java
public static void shellSort(int[] arrays) {

  //增量每次都/2
  for (int step = arrays.length / 2; step > 0; step /= 2) {

    //从增量那组开始进行插入排序，直至完毕
    for (int i = step; i < arrays.length; i++) {

      int j = i;
      int temp = arrays[j];

      // j - step 就是代表与它同组隔壁的元素
      while (j - step >= 0 && arrays[j - step] > temp) {
        arrays[j] = arrays[j - step];
        j = j - step;
      }
      arrays[j] = temp;
    }
  }
}
```

### 归并排序*

**分治法**,先使每个子序列有序，再使子序列段间有序。**若将两个有序表合并成一个有序表，称为二路归并。**

遍历两个数组，比较它们的值。谁比较小，谁先放入大数组中，直到数组遍历完成

```java
 /**
     * 归并排序
     *
     * @param arrays
     * @param L      指向数组第一个元素
     * @param R      指向数组最后一个元素
     */
    public static void mergeSort(int[] arrays, int leftIndex, int rightIndex) {

        //如果只有一个元素，那就不用排序了
        if (leftIndex == rightIndex) {
            return;
        } else {

            //取中间的数，进行拆分
            int M = (leftIndex + rightIndex) / 2;

            //左边的数不断进行拆分
            mergeSort(arrays, leftIndex, M);

            //右边的数不断进行拆分
            mergeSort(arrays, M + 1, rightIndex);

            //合并
            merge(arrays, leftIndex, M + 1, rightIndex);

        }
    }


    /**
     * 合并数组
     *
     * @param arrays
     * @param L      指向数组第一个元素
     * @param M      指向数组分隔的元素
     * @param R      指向数组最后的元素
     */
    public static void merge(int[] arrays, int leftIndex, int M, int rightIndex) {

        //左边的数组的大小
        int[] leftArray = new int[M - leftIndex];

        //右边的数组大小
        int[] rightArray = new int[rightIndex - M + 1];

        //往这两个数组填充数据
        for (int i = leftIndex; i < M; i++) {
            leftArray[i - leftIndex] = arrays[i];
        }
        for (int i = M; i <= rightIndex; i++) {
            rightArray[i - M] = arrays[i];
        }

        int i = 0, j = 0;
        // arrays数组的第一个元素
        int  k = leftIndex;


        //比较这两个数组的值，哪个小，就往数组上放
        while (i < leftArray.length && j < rightArray.length) {

            //谁比较小，谁将元素放入大数组中,移动指针，继续比较下一个
            if (leftArray[i] < rightArray[j]) {
                arrays[k] = leftArray[i];

                i++;
                k++;
            } else {
                arrays[k] = rightArray[j];
                j++;
                k++;
            }
        }

        //如果左边的数组还没比较完，右边的数都已经完了，那么将左边的数抄到大数组中(剩下的都是大数字)
        while (i < leftArray.length) {
            arrays[k] = leftArray[i];
            i++;
            k++;
        }
        //如果右边的数组还没比较完，左边的数都已经完了，那么将右边的数抄到大数组中(剩下的都是大数字)
        while (j < rightArray.length) {
            arrays[k] = rightArray[j];
            k++;
            j++;
        }
    }
```

### 基数排序

### 外部排序--多路归并排序
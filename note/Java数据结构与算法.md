# 数据结构描述

==数据项==相当于表中的一项==记录==，是数据元素不可分割的最小单位

==数据元素==相当于==表==，由一个个的数据项组成。

- 逻辑结构
- 存储结构
  - 顺序存储结构（数组）
  - 链式存储结构（链表）
- 数据的运算

## 逻辑结构分类

![逻辑结构](https://note.youdao.com/yws/api/personal/file/B907B0888FA94B24ACADF1EC30E0730D?method=download&shareKey=328f97da21789d89a36f8e80ae055505)

线性结构：一对一

树结构：一对多

图结构：多对多

# 线性结构

## 顺序表



## 链表

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

## 栈

## 队列

排队，先进先出。

### 普通队列(一次性队列)

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

### 环形队列

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



# 稀疏数组（压缩存储）

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

# 广义表

# 非线性结构

## 树

## 图


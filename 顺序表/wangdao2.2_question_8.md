题目：设计算法，要求：用最少时间在递增有序表中查找数值为x的元素。找到的话与后继元素交换，找不到则插入表中使其保持有序

先放个代码块在这里，等会我们将引用ta
```c
#define maxsize 50
typedef struct{
    ElemType data[maxsize];
    int length;
} SqList
```

查找操作的话，我个人的想法是采用二分法的形式：将中间值与x作比较，如果大于就比后半个顺序表，反之与前半个顺序表进行对比，这样的话复杂度从O（n）变成了O（logn）
```c
bool CompareElem(SqList &L,int &a,int &b,ElemType x)
{
    if(x > L.data[(a+b)/2])
    {
        a=(a+b)/2;
        return CompareElem(&L,&a,&b,x);
    }
    if(x < L.data[(a+b)/2])
    {
        b=(a+b)/2;
        return CompareElem(&L,&a,&b,x);
    }
    if(x == L.data[a] || x == L.data[b])
    {
        return true;
    }
    else
    {
        return false;
    }
}
```

这个查找有一个问题就是：不能判断x跟这个数组里面的L.data[a]相等还是跟L.data[b]相等，所以后面得搞个判断
       还有一个比较难受的点：要是x比这个数组里面全部的数都大/小，这个也是判断不出来的，但是都属于false，所以在最后的插入操作补救一下


接下来是根据CompareElem的返回结果操作顺序表了,先整个改变元素顺序
```c
bool ReverseElem(SqList &L,int &a)
{
    if(a == n-1)
        return false;
    if(a >=0 && a< L.length-1)
    {
        ElemType temp = 0;//有点多余了，直接设定等于后面的L.data[a]就行了
        temp = L.data[a];
        L.data[a] = L.data[a+1];
        L.data[a+1] = temp;
    }
    
    return true; 
}
```

然后就是不存在的情况，这个挺麻烦的，毕竟顺序表的特性就决定了要是插入后续就是得全部都要改
```c
bool InsertElem(SqList &L,int &a,int x)
{
    if(L.length == maxsize)
    {
        return false;
    }
   if(x > L.data[L.length-1])
   {
    x = L.data[n];
    L.length++;
   }
   if(x < L.data[0])
   {
      for(int i=L.length-1;i>0;i--)
      {
        L.data[i]=L.data[i-1];//这个代码条件下前面for循环i的初始值就应该改成L.length了
      }
      L.data[0] = x;
      L.length++;
   }
   else
   {
    for(int i=L.length-1;i>a;i--)
      {
        L.data[i]=L.data[i-1];
      }
      L.data[a+1] = x;
      L.length++;
   }
   return true;
}
```

三个主要模块写完了，最后就是当成函数进行应用即可
```c
bool pushElem(SqList &L,ElemType x)
{
    if(L.length == 0 || L.length > maxsize)
    {
        return false;
    }
    int a= 0;
    int b= L.length-1;
    bool com =CompareElem(&L,&a,&b,x);
    if(com == true)
    {
        ReverseElem(&L,&a);
    }
    else
    {
        InsertElem(&L,&a,x);
    }
    return true;
}
```

王道解法：在前面的寻找也是使用了二分法的思想，只不过用的是while循环而不是递归函数调用

最大的问题是二分查找完全写错了（力竭了，不过知道学习思路的话就还好）
```C
int BinarySearch(SqList &L, int low, int high, ElemType x)
{
    if (low > high)//保证代码健壮性
        return -1;  // 查找失败

    int mid = (low + high) / 2;//设定中间变量，作为查找的信息之一

    if (x == L.data[mid])//将x直接与L.data[mid]比较，使得a与b可以正常收缩区间
        return mid;
    else if (x < L.data[mid])
        return BinarySearch(L, low, mid - 1, x);//应用递归，对数组的左半部分进行比较
    else
        return BinarySearch(L, mid + 1, high, x);//应用递归，对数组的右半部分进行比较
}
```
其次就是插入，其实可以再简化一点（最关键的是前面的二分查找成功把之前那个版本中所有的坑填上了，这样插入操作只需要整个while循环，将大于x的部分全部往后移就行）不过好在原来版本的插入操作写的是对的，就是二分查找爆了个大雷
```C
void InsertElem(SqList &L, ElemType x)
{
    int i = L.length - 1;
    while (i >= 0 && L.data[i] > x)
    {
        L.data[i + 1] = L.data[i];
        i--;
    }
    L.data[i + 1] = x;
    L.length++;
}
```

最后的答案
版本一：chat大人给出的递归方法
```C
int BinarySearch(SqList &L, int low, int high, ElemType x)
{
    if (low > high)//保证代码健壮性
        return -1;  // 查找失败

    int mid = (low + high) / 2;//设定中间变量，作为查找的信息之一

    if (x == L.data[mid])//将x直接与L.data[mid]比较，使得a与b可以正常收缩区间
        return mid;
    else if (x < L.data[mid])
        return BinarySearch(L, low, mid - 1, x);//应用递归，对数组的左半部分进行比较
    else
        return BinarySearch(L, mid + 1, high, x);//应用递归，对数组的右半部分进行比较
}
void SwapWithNext(SqList &L, int pos)//代表x所在位置与其下一个元素交换的函数
{
    if (pos >= 0 && pos < L.length - 1)
    {
        ElemType temp = L.data[pos];//经典交换三行式：设置辅助变量然后进行变换
        L.data[pos] = L.data[pos + 1];
        L.data[pos + 1] = temp;
    }
}
void InsertElem(SqList &L, ElemType x)//代表找不到x的情况下插入合适位序的函数
{
    int i = L.length - 1;
    while (i >= 0 && L.data[i] > x)
    {
        L.data[i + 1] = L.data[i];//插入的时候需要注意从最后一个开始进行循环，从头开始会导致后一个元素的数据会被覆盖
        i--;
    }
    L.data[i + 1] = x;//while循环中实现了比x大的元素全部向右移动了一个位置，最后空出来的位置就是x的
    L.length++;
}
void SearchAndProcess(SqList &L, ElemType x)
{
    int pos = BinarySearch(L, 0, L.length - 1, x);//先找x的位置

    if (pos != -1)//代表在表中找到了x
        SwapWithNext(L, pos);//交换
    else//没找到x
        InsertElem(L, x);//插入x使得表保持递增有序
}
```C

版本二：王道给出的while循环
```c
void SearchExchangeInsert(Elemrype A[],ElemType x)
{
    int low=0,high=n-1,mid;//不应用函数块，直接设定变量
    while(low<=high)//直接套二分查找
    {
        mid=(low+high) /2;
        
        if (A[mid]==x) break;
        else if(A[mid]<x) 
        low=mid+1;
        else high=mid-1;
    }
    if(A[mid]==x&&mid!=n-1)//找到了，直接交换即可
    {
        t=A[mid]; A[mid]=A[mid+1]; A[mid+1]=t;
    }
    if(low>high)//没找到：找个合适的位置，插进去
    {
        for(i=n-1;i>high;i--) A[i+1]=A[i];
        A[i+1] = x;
    }
}
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
        L.data[i]=L.data[i-1];
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
    if (low > high)
        return -1;  // 查找失败

    int mid = (low + high) / 2;//设定中间变量

    if (x == L.data[mid])//将x直接与L.data[mid]比较，使得a与b可以正常收缩区间
        return mid;
    else if (x < L.data[mid])
        return BinarySearch(L, low, mid - 1, x);
    else
        return BinarySearch(L, mid + 1, high, x);
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
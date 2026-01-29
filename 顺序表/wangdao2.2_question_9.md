今天的题目有点长了...\
给定三个序列A、B、C，长度均为`n`，且均为无重复元素的递增序列，请设计一个时间上尽可能高效的算法，逐行输出同时存在于这三个序列中的所有元素。例如，数组A为{1,2,3}，数组B为{2,3,4}，数组C为{-1,0,2}，则输出2。要求:
1) 给出算法的基本设计思想。
2) 根据设计思想，采用C或C++语言描述算法，关键之处给出注释。
3) 说明你的算法的时间复杂度和空间复杂度。
----
\
老规矩，先把结构体的代码奉上
```C
#define maxsize 50
typedef struct{
    ElemType data[maxsize];
    int length;
} SqList
```
----

(1) 基本设计思想：\
（馊主意一枚）给三个数组安插三个指针进行遍历，然后分别进行比较，小于最大值的指针向后遍历，最后得出结果后输出并且三个指针同时指向下一个位置\
（后续来了）并非馊主意，哈哈\

（2）描述算法
```C
void FindSameElem(SqList &A,SqList &B,SqList &C,int n)
{

    int a=0;
    int b=0;
    int c=0;
    while(a<n && b<n && c<n)
    {
        if(A.data[a] == B.data[b]==C.data[c])//想整个连等，但是问题在于算法会先判断前两个，最后返回一个bool值（0或1），然后与C.data比较，必定是错的
        {
            printf("%d/n",A.data[a]);//换行符忘记了哦
            a++;
            b++;
            c++;
        }
        else if(A.data[a] < max{B.data[b],C.data[c]})//这一段代码理论上没什么问题，虽然最终实现的复杂度确实还是O(n)，但是在算法中由于没有判定最后需要移动一个指针还是两个指针，将导致时间浪费
        {
            a++;
        }
        else if(B.data[b] < max{A.data[a],C.data[c]})
        {
            b++;
        }
        else if(C.data[c] < max{A.data[a],B.data[b]})
        {
            c++;
        }
    }
}
```

（3）通过这个代码，实现的时间复杂度为$O(n)$，实现的空间复杂度为$O(1)$

##请教chat大人进行修改
```C
void FindSameElem(SqList &A,SqList &B,SqList &C,int n)
{
    int a=0;
    int b=0;
    int c=0;
    while(a<n && b<n && c<n)
    {
        if(A.data[a] == B.data[b]&& A.data[a] ==C.data[c])
        {
            printf("%d\n",A.data[a]);
            a++;
            b++;
            c++;
        }
        else
        {
            int max = A.data[a];//这个是没有使用max函数的情况，事实上，这个在考试中100%正确，用最基本的算法去实现max操作本身就是我们需要做的事情
            if(max < B.data[b])
                max = B.data[b];
            if(max < C.data[c])
               max = C.data[c];
            if(max != A.data[a])
              a++;
            if(max != B.data[b])
              b++;
            if(max != C.data[c])
              c++;
        }
    }
}
```

王道解法:\
实现思路：\
使用三个下标变量从小到大遍历数组。当三个下标变量指向的元素相等时，输出并向前推进指针，否则仅移动小于最大元素的下标变量，直到某个下标变量移出数组范围，即可停止。\
实现代码：
```C
void samekey(int A[], int B[], int C[], int n)
{
    int i = 0, j = 0, k = 0;   // 定义三个工作指针
    while (i < n && j < n && k < n)
    {
        if (A[i] == B[j] && B[j] == C[k])   // 相同则输出，并集体后移
        {
            printf("%d\n", A[i]);
            i++;
            j++;
            k++;
        }
        else
        {
            int maxNum = max(A[i], max(B[j], C[k]));
            if (A[i] < maxNum) i++;
            if (B[j] < maxNum) j++;
            if (C[k] < maxNum) k++;
        }
    }
}

```
##总结\
好在好在，实现思路是正确的，就差写对代码了（\
三个数字连等输出的算法大致过程就是：
三个指针一起动，小于最大值的指针移动，三个指针指向的元素都相等的时候再输出and将指针一起指向下一个元素

还有一个严重的问题：三个数不能直接写连等（非bool情况下），得写成
```C
A.data[a] == B.data[b] &&  B.data[b] == C.data[c]
```
换成两个取等条件\
另外，写完算法（类似于今天的比大小问题，其实最后算法肯定是可以实现的，但是这种实现方法太浪费时间了，而且与算法的语义并不吻合）需要比较一下这个实现方式是不是与自己的实现思路吻合\
哦说到这里突然想起来，我的思路写的也是一坨屎（哭）
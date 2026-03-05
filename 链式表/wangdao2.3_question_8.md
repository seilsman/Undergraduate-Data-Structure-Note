## 1、题目
### 算法题目：输出递增两个单链表中的重复元素

设 $A$ 和 $B$ 是两个单链表（带头结点），其中元素递增有序。  
设计一个算法从 $A$ 和 $B$ 中的公共元素产生单链表 $C$，要求不破坏 $A$、$B$ 的结点。

## 2、本人胡思乱想环节
递增表无法破坏原来的表的环境，所以我们只能用两个指针去遍历两个数组中的元素，不过由于是递增表，所以理论上最麻烦的情况就是遍历2n个元素即可

### 代码实现
```C
LinkList RemainSame(LinkList A,LinkList B,LinkList &C)
{
    C -> next = NULL;
    LNode *preA = A;
    LNode *preB = B;
    LNode *preC = C;
    while(preA -> next != NULL && preB -> next != NULL)
    {
        if(preA -> next -> data == preB -> next -> data)
        {
            LNode *curC = (LNode *) malloc(sizeof(LNode));
            curC = NULL;
            curC -> data = preA -> next -> data;
            preC -> next = curC;
            preC = preC -> next;
            preA = preA -> next;
            preB = preB -> next;
        }
        else if(preA -> next -> data > preB -> next -> data)
        {
            preB = preB -> next;
        }
        else
        {
            preA = preA -> next;
        }
    }
    return C;
}
```
### 问题
> 初始化结点有问题，根本就是没理解结点结构和链表结构的区别
> 尾插法应用有问题，将curC结点后接链表的赋值式子写反可能会导致内存溢出
> 未初始化curC结点为NULL，可能混入野指针
## 3、chat大人给出的算法
## 算法思路

由于链表 $A$ 和 $B$ 均为**递增有序单链表**，可以采用类似**归并扫描**的方法：

1. 设置两个工作指针分别扫描链表 $A$ 和 $B$。
2. 比较当前两个结点的数据：
   
   - 若相等，则说明该元素是公共元素  
     - 申请新结点加入链表 $C$
     - 两个指针同时后移
     
   - 若 $A$ 当前元素较小，则只移动 $A$ 的指针
   
   - 若 $B$ 当前元素较小，则只移动 $B$ 的指针

3. 当任一链表扫描结束时算法结束。

由于 **不允许破坏 $A$、$B$ 的结点**，因此链表 $C$ 必须 **重新申请结点存储公共元素**。

---

## C语言实现

```c
LinkList RemainSame(LinkList A, LinkList B, LinkList C)
{
    C->next = NULL;          // 初始化结果链表

    LNode *pa = A->next;     // 扫描A的指针
    LNode *pb = B->next;     // 扫描B的指针
    LNode *pc = C;           // C的尾指针

    while (pa != NULL && pb != NULL)
    {
        if (pa->data == pb->data)        // 找到公共元素
        {
            LNode *s = (LNode *)malloc(sizeof(LNode));
            s->data = pa->data;
            s->next = NULL;

            pc->next = s;                // 尾插到C
            pc = s;

            pa = pa->next;
            pb = pb->next;
        }
        else if (pa->data < pb->data)
        {
            pa = pa->next;               // A较小，移动A
        }
        else
        {
            pb = pb->next;               // B较小，移动B
        }
    }

    return C;
}
```
## 4、王道解法
### 算法思想

表 $A$、$B$ 都有序，可从第一个元素起依次比较 $A$、$B$ 两表的元素：

- 若元素值不等，则值小的指针后移；
- 若元素值相等，则创建一个值等于两结点元素值的新结点，使用尾插法插入到新的链表 $C$ 中，并将两个原表指针后移一位；
- 直到其中一个链表遍历到表尾为止。

---

### C语言代码

```c
void Get_Common(LinkList A, LinkList B)
{
    LNode *p = A->next, *q = B->next, *r, *s;
    LinkList C = (LinkList)malloc(sizeof(LNode));   // 建立表C

    r = C;                                          // r始终指向C的尾结点

    while (p != NULL && q != NULL)                  // 循环跳出条件
    {
        if (p->data < q->data)
            p = p->next;                            // 若A当前元素较小，后移指针
        else if (p->data > q->data)
            q = q->next;                            // 若B当前元素较小，后移指针
        else
        {
            s = (LNode *)malloc(sizeof(LNode));     // 找到公共元素结点
            s->data = p->data;                      // 复制产生结点*s

            r->next = s;                            // 将*s链接到C上（尾插法）
            r = s;

            p = p->next;                            // 表A和B继续向后扫描
            q = q->next;
        }
    }

    r->next = NULL;                                 // 置C尾结点指针为空
}
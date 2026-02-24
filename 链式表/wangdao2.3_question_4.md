## 1、题目
设在一个带表头结点的单链表中，所有结点的元素值无序，试编写一个函数，删除表中所有处于给定的两个值（作为函数参数给出）之间的元素（若存在）。
## 2、本人胡思乱想环节
经过前几次的毒打后我明白一件事情：做链表题的时候一定要注意指针的数量
设定两个辅助变量以确定两个指针的相对前后关系
删除操作只要定位到两个给定值的指针，再将在头部的指针直接插到尾部即可


### 代码实现
```C
void DeleteElem(LinkList L,ElemType x,ElemType y)
{
    LNode *prex = L;//创建辅助指针表示指向x，y结点的前驱结点，两个int变量表示其在链表中的相对位置
    int px = 0;
    LNode *prey = L;
    int py = 0;
    while(prex -> next -> data !=x)//查找x结点的相对位置
    {
        px++;
        prex = prex->next;
    }
    while(prey -> next -> data !=y)//查找y结点的相对位置
    {
        py++;
        prey = prey ->next;
    }
    if(px > py)//x结点在y对应结点的后面
    {
        LNode *cury = prey ->next;
        cury ->next = prex ->next;
        prex ->next = NULL;
    }
    else//y结点在x结点的后面
    {
        LNode *curx = prex ->next;
        curx ->next = prey ->next;
        prey ->next = NULL;
    }
}

void DeleteElem(LinkList L,ElemType x,ElemType y)
{
    LNode *pre = L;
    LNode *cur = L;
    ElemType min =0,ElemType max = 0;
    if(x<y)
    {
        min = x;
        max = y;
    }      
    else if(x > y)
    {
        min = y;
        max = x;
    }
    else
    {
        return;
    }
       
    while(pre -> next != NULL)
    {
        cur = pre -> next;
        if(cur ->data > min && cur ->data < max)
        {
            pre -> next = cur -> next;
            free(cur);
        }
        else
            pre = pre -> next;
    }
}




```
后续：整道题目理解错了，应该按照数值区间删除的（
## 3、chat大人给出的算法

### 算法实现（C 语言）
```C
void DeleteElem(LinkList L, ElemType x, ElemType y)
{
    // 若区间退化，无需处理
    if (x == y)
        return;

    // 计算区间上下界
    ElemType min = (x < y) ? x : y;
    ElemType max = (x > y) ? x : y;

    // pre 始终指向“已确认保留区”的尾结点
    LNode *pre = L;

    // 当未来区仍存在结点时继续处理
    while (pre->next != NULL)
    {
        // 当前待决策结点
        LNode *cur = pre->next;

        // 若当前结点值处于区间 (min, max) 内，则删除
        if (cur->data > min && cur->data < max)
        {
            pre->next = cur->next;   // 跳过当前结点
            free(cur);               // 释放被删除结点
        }
        else
        {
            pre = pre->next;         // 扩展保留区
        }
    }
}
```

## 4、王道解法
因为链表是无序的，所以只能逐个结点进行检查，执行删除。

### 本题代码如下：

```c
void RangeDelete(LinkList &L, int min, int max)
{
    LNode *pr = L, *p = L->link;   // p 是检测指针，pr 是其前驱

    while (p != NULL)
    {
        if (p->data > min && p->data < max)   // 找到被删除结点，删除
        {
            pr->link = p->link;
            free(p);
            p = pr->link;
        }
        else                                  // 否则继续寻找被删除结点
        {
            pr = p;
            p = p->link;
        }
    }
}
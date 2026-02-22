## 1、题目

试编写在带头结点的单链表 L 中删除一个最小值结点的高效算法（假设该结点唯一）。




## 2、本人胡思乱想环节
与第一题有略微不同，我们可以先将第一遍数组遍历，保存最小值结点的前驱结点，然后正常实行删除操作即可


### 代码实现
```C
void DeleteElem(LinkList &L)
{
    LNode *I = L -> next;//表示遍历的结点
    ElemType min = I -> data;//代表最小值的数值
    LNode *J = L -> next;//代表最小值对应结点的前驱结点

    while (I->next != NULL)
    {
        if (I->next->data < min)
        {
           min = I->next->data;
           J = I;
        }
        I = I->next;
    }
    LNode *N = J -> next;
    J -> next = N -> next;
    free(N);
}

```
### 单链表删除算法代码问题分析
#### ❗问题 1：前驱初始化错误

```c
LNode *J = L->next;
```
J 被定义为“最小值结点的前驱”，
但若最小值在第一个数据结点，其前驱应为 L（头结点），而不是 L->next。

因此当最小值在首位时，会删除错误结点。
#### ❗问题 2：扫描模型不统一
当前结构：
I 表示当前数据结点,比较的是 `I->next->data` \
这属于“前驱扫描模式”，
但初始化却不是从头结点开始，模型语义不一致，容易导致逻辑错误。

## 3、chat大人给出的算法

### 算法实现（C 语言）

```c
//前驱扫描模型
void DeleteMin_PreScan(LinkList L)
{
    // 防御式编程：空表或无数据结点直接返回
    if (L == NULL || L->next == NULL)
        return;

    LNode *pre = L;                   // 当前扫描到的“前驱”
    LNode *minPre = L;                // 最小值结点的前驱
    ElemType min = L->next->data;     // 最小值初始化为第一个数据结点

    while (pre->next != NULL)
    {
        /* 
           pre->next 是当前被比较的数据结点
           若发现更小值，则更新：
           1. min
           2. 最小值结点的前驱 minPre
        */
        if (pre->next->data < min)
        {
            min = pre->next->data;
            minPre = pre;
        }

        pre = pre->next;   // 前驱后移
    }

    // 删除 minPre->next（即最小值结点）
    LNode *target = minPre->next;
    minPre->next = target->next;
    free(target);
}


//当前结点扫描模型
void DeleteMin_CurScan(LinkList L)
{
    if (L == NULL || L->next == NULL)
        return;

    LNode *cur = L->next;        // 当前扫描的数据结点
    LNode *pre = L;              // cur 的前驱

    LNode *minNode = cur;        // 当前最小值结点
    LNode *minPre = L;           // 最小值结点的前驱

    while (cur != NULL)
    {
        /*
           cur 是当前数据结点
           若发现更小值，更新：
           1. minNode
           2. minPre
        */
        if (cur->data < minNode->data)
        {
            minNode = cur;
            minPre = pre;
        }

        pre = cur;
        cur = cur->next;
    }

    // 删除 minNode
    minPre->next = minNode->next;
    free(minNode);
}
```


## 4、王道解法

### 算法思想

用 `p` 从头至尾扫描链表，`pre` 指向 `p` 结点的前驱；  
用 `minp` 保存值最小的结点指针（初值为 `p`），  
`minpre` 指向 `minp` 结点的前驱（初值为 `pre`）。

一边扫描，一边比较：

- 若 `p->data < minp->data`，  
  则将 `p`、`pre` 分别赋值给 `minp`、`minpre`。
- 当扫描结束时，`minp` 指向最小值结点，  
  `minpre` 指向最小值结点的前驱结点。
- 最后删除 `minp` 所指结点即可。

---

### 本题代码

```c
//采用当前结点扫描模型
LinkList Delete_Min(LinkList &L)
{
    LNode *pre = L, *p = pre->next;   // 工作指针，pre 指向 p 的前驱
    LNode *minpre = pre, *minp = p;   // 保存最小值结点及其前驱

    while (p != NULL)
    {
        if (p->data < minp->data)     // 找到更小的结点
        {
            minp = p;
            minpre = pre;
        }

        pre = p;         // 继续扫描下一个结点
        p = p->next;
    }

    minpre->next = minp->next;   // 删除最小值结点
    free(minp);

    return L;
}
```
---

### 复杂度分析

- 时间复杂度：`O(n)`
- 空间复杂度：`O(1)`
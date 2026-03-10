## 1、题目

### 13. 访问频度调整的链表查找问题

设有一个带头结点的**非循环双链表** $L$，其每个结点除含有 `pre`、`data` 和 `next` 域外，  
还有一个**访问频度域** `freq`，其值初始均为 $0$。

每当在链表中进行一次 `Locate(L, x)` 运算时：

1. 令值为 $x$ 的结点中 `freq` 域的值增加 $1$；
2. 并使此链表中的结点保持按**访问频度递减**的顺序排列；
3. 最后访问的结点排在**相同访问频度结点的最前面**，以便使频繁访问的结点总是靠近表头。

要求：

试编写满足上述要求的 `Locate(L, x)` 函数，返回找到结点的地址，类型为**指针型**。

## 2、本人胡思乱想环节
目前就想到了三点：
> 遍历：找到节点并使`freq++`，保存结点地址
> 读取当前`freq`，遍历到`freq+1`节点的末尾
> 插入
> 最后返回结点地址
### 代码实现
```C
LNode *Locate(LinkList L,int x)
{
    LNode *cur = L -> next;//寻找含有元素x的结点
    while(cur -> data != x && cur != NULL)
    {
        cur = cur -> next;
    }
    if(cur == NULL)//找不到：直接返回NULL即可
    {
        return NULL;
    }
    cur -> freq = cur -> freq + 1;//查找一次x：freq值+1
    int fr = cur -> freq + 1; //辅助变量：寻找freq比x大的最后一个结点
    LNode *re = L -> next; // 辅助结点：不大于x结点的freq的第一个结点
    while(re -> freq >= fr && re != NULL)//寻找操作，但是最后一定会找到
    {
        re = re -> next;
    }
    if(re == cur)//表中所有的结点freq都比x大：不用进行移动操作，直接返回cur的地址即可
    {
        return cur;
    } 
    LNode *add = cur;//找到x元素：保存找到节点的地址

    //将x元素的前驱元素和后继元素接在一起
    cur -> pre -> next = cur -> next;
    cur -> next -> pre = cur -> pre;

    //将x元素接到寻找到的辅助结点之前
    LNode *pre = freq -> pre;
    cur -> pre = pre;
    cur -> next = re;
    re -> pre = cur;
    pre -> next = cur;
    //返回原来的找到的地址
    return add;
}


```
### 问题
> 双链表的结点类型应该是`DNode`而不是`LNode`

> 要记住：while循环中条件判断不是并行的，是按照指令顺序判断的，所以在使用当前结点进行遍历的时候一定要先把边界判断放在前

> 算法仍未达到最优：由于递减排序，所以访问x结点并且增加以后不用判断x结点之后的任何结点，只需要判断前面的部分即可

> 删除操作的边界判断有误：双链表中NULL结点是没有pre指针指向尾结点的，所以执行删除操作的时候要看是否为后继结点（重要的边界判断，要记住！）

> 算法语义可以写得更好：有些数学上成立的逻辑在算法中实现复杂度可能更高且可读性变差and虽然隐藏前提被逻辑闭合实现了但是不符合代码安全原则。在以后写代码的时候还是尽量写一些可读性和鲁棒性更高的代码
## 3、chat大人给出的算法

### 一、 算法推导过程
Locate(L, x) 运算步骤：

**查找结点**：从 L->next 开始遍历，找到 data == x 的结点 p。

**更新访问频度**：p->freq += 1

**断开结点**：将 p 从原位置移除。

**重新插入**：

从头结点开始遍历链表，找到第一个 freq 小于等于 p->freq 的位置 q。

将 p 插入到 q 前面（若 freq 相同，则插入到同类结点的最前面）。

**返回结点**：返回找到的结点地址（或 NULL 如果未找到）。

### 二、 最终代码实现


```C
// Locate 操作：查找并调整
DNode* Locate(LinkList L, int x) {
    DNode* cur = L->next;

    // 查找结点
    while(cur != NULL && cur->data != x)
        cur = cur->next;

    if(cur == NULL)  // 未找到
        return NULL;

    // 访问频度加1
    cur->freq += 1;

    // 断开 cur
    if(cur->prev) cur->prev->next = cur->next;
    if(cur->next) cur->next->prev = cur->prev;

    // 从头结点开始寻找插入位置
    DNode* pos = L->next;
    while(pos != NULL && pos->freq > cur->freq)
        pos = pos->next;

    // 插入 cur 到 pos 前
    if(pos == NULL) { // 插入到尾部
        DNode* tail = L;
        while(tail->next != NULL) tail = tail->next;
        tail->next = cur;
        cur->prev = tail;
        cur->next = NULL;
    } else { // 插入到 pos 前
        cur->prev = pos->prev;
        cur->next = pos;
        if(pos->prev) pos->prev->next = cur;
        pos->prev = cur;
        if(pos == L->next) L->next = cur; // 更新头结点后的第一个元素
    }

    return cur;
}
```

## 4、王道解法

13. 【解答】
算法思想：首先在双链表中查找数据值为 x 的结点，查找到后，将该结点从链表上摘下，然后顺着结点的前驱链查找该结点的插入位置（频度递减，且排在同频度的第一个，即向前找到第一个比它的频度大的结点，插入位置为该结点之后），并插入到该位置。

本题代码如下：

```C++
DLinkList Locate(DLinkList &L, ElemType x) {
    DNode *p = L->next, *q;    // p 为工作指针，q 为 p 的前驱，用于查找插入位置
    while (p && p->data != x)
        p = p->next;           // 查找值为 x 的结点
    if (!p)
        exit(0);               // 不存在值为 x 的结点
    else {
        p->freq++;             // 令元素值为 x 的结点的 freq 域加 1
        if (p->pre == L || p->pre->freq > p->freq)
            return p;          // p 是链表首结点，或 freq 值小于前驱
        
        if (p->next != NULL) 
            p->next->pre = p->pre;
        
        p->pre->next = p->next; // 将 p 结点从链表上摘下
        q = p->pre;             // 以下查找 p 结点的插入位置
        while (q != L && q->freq <= p->freq)
            q = q->pre;
        
        p->next = q->next;
        if (q->next != NULL) 
            q->next->pre = p;   // 将 p 结点排在同频度的第一个
        
        p->pre = q;
        q->next = p;
    }
    return p;                   // 返回值为 x 的结点的指针
}
```
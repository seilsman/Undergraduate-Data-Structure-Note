## 1、题目
设 $C=\{a_1,b_1,a_2,b_2,\dots,a_n,b_n\}$ 为线性表，采用带头结点的单链表存放，设计一个就地算法，
将其拆分为两个线性表，使得

$$
A=\{a_1,a_2,\dots,a_n\}
$$

$$
B=\{b_n,\dots,b_2,b_1\}
$$
## 2、本人胡思乱想环节
先初始化两个链式表，然后根据奇偶性分别放入两个不同的链表中，B链表可以使用头插法


### 代码实现
```C
void DivideList(LinkList &C,LinkList B)
{
    B -> next = NULL;
    LNode *cur = C -> next;
    while(cur -> next != NULL)
    {
        LNode *b = cur -> next;
        cur -> next = b -> next;
        b -> next = B -> next;
        B -> next = b;
        cur = cur -> next;
    }
}
```
后续：整道题目理解错了，应该按照数值区间删除的（
## 3、chat大人给出的算法

### 算法实现（C 语言）
```C
// 结点定义
typedef struct LNode
{
    ElemType data;
    struct LNode *next;
} LNode, *LinkList;


// 将 C 拆分为 A(C) 和 B
void DivideList(LinkList C, LinkList B)
{
    // 1️⃣ 参数合法性检查
    if (C == NULL || B == NULL)
        return;

    // 2️⃣ 初始化 B 为空链表
    B->next = NULL;

    // 3️⃣ cur 始终指向当前 a 结点
    LNode *cur = C->next;

    // 4️⃣ 当存在 b 结点时循环
    while (cur != NULL && cur->next != NULL)
    {
        // 取出 b 结点
        LNode *b = cur->next;

        // 从 C 中删除 b
        cur->next = b->next;

        // 头插法插入 B（实现逆序）
        b->next = B->next;
        B->next = b;

        // cur 向后移动到下一个 a
        cur = cur->next;
    }
}
```
## 4、王道解法
## 算法思想

循环遍历链表 C：

- 采用**尾插法**将第一个结点插入表 A（该结点为奇数号结点），  
  这样建立的表 A 与原链表中的结点顺序相同；
- 采用**头插法**将下一个结点插入表 B（该结点为偶数号结点），  
  这样建立的表 B 与原链表中的结点顺序正好相反。

---

## 本题代码如下

```c
LinkList DisCreat_2(LinkList &A)
{
    LinkList B = (LinkList)malloc(sizeof(LNode)); // 创建 B 表表头
    B->next = NULL;                               // B 表初始化

    LNode *p = A->next, *q;   // p 为工作指针
    LNode *ra = A;            // ra 始终指向 A 的尾结点

    while (p != NULL)
    {
        // —— 奇数结点：尾插到 A ——
        ra->next = p;
        ra = p;
        p = p->next;

        if (p != NULL)
        {
            // —— 偶数结点：头插到 B ——
            q = p->next;          // 保存 p 的后继（否则会断链）
            p->next = B->next;
            B->next = p;
            p = q;
        }
    }

    ra->next = NULL;  // A 表尾结点 next 置空

    return B;
}
```
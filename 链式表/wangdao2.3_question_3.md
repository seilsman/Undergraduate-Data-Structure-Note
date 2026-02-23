## 1、题目

试编写算法将带头结点的单链表就地逆置，所谓“就地”是指辅助空间复杂度为 O(1)。

## 2、本人胡思乱想环节
试试循环尾插，但是太麻烦了（
### 代码实现
```C


```

## 3、chat大人给出的算法

### 算法实现（C 语言）

```c
//使用三指针进行操作
bool ReverseList(LinkList L)
{
    if (L == NULL || L->next == NULL)
        return false;   // 空表或无数据结点

    LNode *prev = NULL;        // 前驱指针
    LNode *cur  = L->next;     // 当前结点
    LNode *next = NULL;        // 后继指针

    while (cur != NULL)
    {
        next = cur->next;      // ① 保存后继
        cur->next = prev;      // ② 反转指针
        prev = cur;            // ③ 前移 prev
        cur = next;            // ④ 前移 cur
    }

    L->next = prev;            // 头结点指向新的首元结点

    return true;
}
```


## 4、王道解法
### 解法1

将头结点摘下，然后从第一个结点开始，依次插入到头结点的后面（头插法建立单链表），
直到最后一个结点为止，这样就实现了链表的逆置。

#### 本题代码如下：

```cpp
LinkList Reverse_1(LinkList L){
    LNode *p, *r;                 // p 为工作指针，r 为 p 的后继，以防断链
    p = L->next;                  // 从第一个结点开始
    L->next = NULL;               // 先将头结点 L 的 next 域置为 NULL

    while(p != NULL){             // 依次将元素结点插下
        r = p->next;              // 暂存 p 的后继
        p->next = L->next;        // 将 p 结点插到头结点之后
        L->next = p;
        p = r;
    }

    return L;
}
```

---

### 解法2

大部分辅导书都只介绍解法1，这对读者的理解和思维是不利的。  
为了将调整指针这个复杂的过程分析清楚，我们借助图形来进行直观分析。

假设 `pre`、`p` 和 `r` 指向三个相邻的结点。  
假设经过若干操作后，`pre` 之前的结点都已经调整完毕，它们的 `next` 都指向其前驱结点。  
现在令 `p` 结点的 `next` 指向 `pre` 结点。

注意两点：

1. 在处理第一个结点时，应将其 `next` 域置为 `NULL`（因为它将作为新表的尾结点）。
2. 在处理完最后一个结点后，需要将头结点的指针指向它。

---

#### 本题代码如下：

```cpp
LinkList Reverse_2(LinkList L){
    LNode *pre, *p = L->next, *r = p->next;

    p->next = NULL;               // 处理第一个结点

    while(r != NULL){             // r 为空，说明 p 为最后一个结点
        pre = p;                  // 依次继续遍历
        p = r;
        r = r->next;
        p->next = pre;            // 指针反转
    }

    L->next = p;                  // 处理最后一个结点
    return L;
}
```

---

## 复杂度分析

两个算法的时间复杂度：
O(n)

空间复杂度：
O(1)

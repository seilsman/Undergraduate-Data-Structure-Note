## 1、题目
###  11

设计一个算法用于判断带头结点的循环双链表是否对称


## 2、本人胡思乱想环节
对于双链表这个对称判断就非常简单了：将表头和表尾指针进行比较然后逐级推进即可

### 代码实现
```C
bool SymmeticLink(LinkList A)
{
    LNode *pa = A -> prev;
    LNode *na = A -> next;

    while(pa != na)
    {
        if(pa -> data != na -> data)
        {
            return false;
        }
        else
        {
            pa = pa -> prev;
            na = na -> next;
        }
    }
    return true;
}


```
### 问题
> 在应用双端收缩的时候，没有根据链表长度为偶数的情况进行if条件判断，这将导致实际算法执行速度大幅减慢（因为这俩结点在链表中间会交错，最后只能通过同时访问头结点结束while循环）

> 空链表判断不准确：双向循环链表若数据域为空，则头结点的prev和next结点将变成L结点本身，不会出现前驱/后继结点为NULL的情况

> 双向链表初始化错误
## 3、chat大人给出的算法

### 一、算法设计思想

设循环双链表结构为：

```
Head ⇄ a1 ⇄ a2 ⇄ ... ⇄ an
```

其中：

* `Head->next` 指向第一个数据结点
* `Head->prev` 指向最后一个数据结点

算法步骤：

1. 设置两个指针

   * `left` 指向链表首结点 `Head->next`
   * `right` 指向链表尾结点 `Head->prev`

2. 每轮比较：

$$
left->data \quad 与 \quad right->data
$$

3. 若不相等，则链表 **不对称**。

4. 若相等，则：

```
  left  = left->next
  right = right->prev
```

继续向中间移动。

5. 当指针 **相遇或交叉** 时说明已经检查完一半元素：

* 奇数长度：`left == right`
* 偶数长度：`left->prev == right`

此时链表对称。

时间复杂度：

$$
O(n)
$$
空间复杂度：

$$
O(1)
$$

---

### 二、C语言算法实现（带头结点循环双链表）

```c
bool SymmetricLink(DLinkList L)
{
    // 若链表为空（只有头结点）
    if(L->next == L)
        return true;

    DNode *left  = L->next;   // 从表头开始
    DNode *right = L->prev;   // 从表尾开始

    // 当指针尚未相遇或交叉时继续比较
    while(left != right && left->prev != right)
    {
        // 若对应位置元素不同，则链表不对称
        if(left->data != right->data)
            return false;

        // 向中间移动
        left  = left->next;
        right = right->prev;
    }

    // 若所有对应元素均相等，则链表对称
    return true;
}
```

---

### 三、关键实现细节说明

#### 1️⃣ 空表判断

循环双链表中空表结构为：

```
Head
 ↑  ↓
 └──┘
```

即：

```
L->next == L
```

因此不能用 `NULL` 判断。

---

#### 2️⃣ 循环终止条件

循环条件：

```
left != right && left->prev != right
```

含义：

| 情况   | 停止条件                  |
| ---- | --------------------- |
| 奇数长度 | `left == right`       |
| 偶数长度 | `left->prev == right` |

这样可以保证：

* 只比较 **前半部分**
* 不会访问头结点

---

#### 3️⃣ 为什么时间复杂度是 O(n)

每次循环：

```
left  向后移动一步
right 向前移动一步
```

因此比较次数最多为：

$$
\lfloor n/2 \rfloor
$$

整体复杂度仍为：

$$
O(n)
$$

但遍历次数仅为 **半个链表**。

---

### 四、算法模型总结

该问题本质属于一种典型结构模型：

#### **双端收缩（Two-End Contraction）**

基本模式：

```
left  → → → → 
← ← ← ← right
```

每轮操作：

```
比较(left, right)
left++
right--
```

直到：

```
相遇 或 交叉
```

常见应用包括：

* 回文串判断
* 双链表对称判断
* 数组首尾匹配问题

---

✔ **一句方法论总结**

> 当数据结构允许同时访问首尾元素时，可采用“双端收缩策略”在一次扫描内完成对称性判断。


## 4、王道解法

### 算法思想
让 `p` 从左向右扫描，`q` 从右向左扫描，直到它们指向同一结点（`p == q`，当循环双链表中结点个数为奇数时）或相邻（`p->next == q` 或 `q->prior == p`，当循环双链表中结点个数为偶数时）为止。若它们所指结点值相同，则继续进行下去，否则返回 0。若比较全部相等，则返回 1。

### 本题代码如下

```c
int Symmetry(DLinkList L) {
    DNode *p = L->next, *q = L->prior; // 两头工作指针
    while (p != q && q->next != p) {    // 循环跳出条件
        if (p->data == q->data) {       // 所指结点值相同则继续比较
            p = p->next;
            q = q->prior;
        } else {                         // 否则，返回 0
            return 0;
        }
    }
    return 1;                            // 比较结束后返回 1
}
```
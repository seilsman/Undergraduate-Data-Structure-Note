## 1、题目

### 20.【2019 统考真题】

设线性表  
$$
L = (a_1, a_2, a_3, \cdots, a_{n-2}, a_{n-1}, a_n)
$$  
采用带头结点的单链表保存，链表中的结点定义如下：

```c
typedef struct node
{
    int data;
    struct node *next;
} NODE;
```

请设计一个空间复杂度为 $O(1)$、时间上尽可能高效的算法，重新排列 $L$ 中的各结点，得到线性表

$$
L' = (a_1, a_n, a_2, a_{n-1}, a_3, a_{n-2}, \cdots)
$$

要求：

1. 给出算法的基本设计思想。
2. 根据设计思想，采用 C 或 C++ 语言描述算法，关键之处给出注释。
3. 说明你所设计的算法的时间复杂度。


## 2、本人胡思乱想环节
目前以我的脑子只能这么想了：
先应用快慢指针，将链表分为两个部分
前一个链表不变，后一个链表应用三指针法进行逆置
最后将后半链表以倒序的方式逐级插入前半链表

### 代码实现
```C
typedef struct node
{
    int data;
    struct node *next;
}NODE;


void ReverseNode(NODE* L)
{
    //第一步：应用快慢指针思想对半分链表；
    NODE *fast = L -> next;
    NODE *slow = L -> next;

    while(fast != NULL &&fast -> next != NULL)
    {
        slow = slow -> next;
        fast = fast -> next -> next;
    }
    //第二步：应用三指针法将后半链表进行逆置,同时将前半部分链表的尾部进行“切割”
    NODE *pre = NULL;
    NODE *cur = slow -> next;
    NODE *next = NULL;
    while(cur != NULL)
    {
        next = cur -> next;
        cur -> next = pre;
        pre = cur;
        cur = next;
    }
    slow -> next = NULL;

    //第三步：将后半链表插入前半部分链表
    NODE *front = L -> next;
    NODE *rear  = pre;
    NODE *renext = pre -> next;
    while(front != NULL && rear != NULL)
    {
        renext = rear -> next;
        rear -> next = front -> next;//保存这个指针，然后将后半部分的链表逐个插入到对应前半链表结点之后
        front -> next = rear;

        front = front -> next -> next;
        rear = renext;//推进指针
    }

}


```
### 效率分析

**时间复杂度**：$O(n)$


### 问题
> 快慢指针的循环条件，退出条件写反

> 访问空指针的边界条件没有判断


## chat大人的解法


### 一、算法设计思想

将原链表重排为
[
(a_1, a_n, a_2, a_{n-1}, \cdots)
]
可分解为三个步骤：

### （1）寻找中点并拆分链表

* 使用**快慢指针**：

  * `fast` 每次走两步，`slow` 每次走一步
* 当循环结束时：

  * `slow` 指向**前半部分的尾结点**
* 将链表从 `slow` 处分为：

  * 前半部分：(a_1 \sim a_k)
  * 后半部分：(a_{k+1} \sim a_n)

---

### （2）逆置后半部分链表

* 使用**三指针法（pre, cur, next）**
* 将后半部分变为：
  [
  a_n \rightarrow a_{n-1} \rightarrow \cdots
  ]

---

### （3）交叉合并两个链表

* 依次将后半部分结点插入前半部分：
  [
  a_1 \to a_n \to a_2 \to a_{n-1} \to \cdots
  ]
* 每次操作：

  * 保存前后继指针
  * 进行“拉链式”插入

---

### 二、算法实现（C语言，关键注释齐全）

```c
typedef struct node
{
    int data;
    struct node *next;
} NODE;

void ReorderList(NODE *L)
{
    // 边界情况：空表或只有1个结点，直接返回
    if(L == NULL || L->next == NULL || L->next->next == NULL)
        return;

    /********** 1. 快慢指针找中点 **********/
    NODE *slow = L->next;
    NODE *fast = L->next;

    // 保证 slow 最终停在前半部分的尾结点
    while(fast != NULL && fast->next != NULL && fast->next->next != NULL)
    {
        slow = slow->next;
        fast = fast->next->next;
    }

    /********** 2. 逆置后半部分链表 **********/
    NODE *cur = slow->next;   // 后半部分头结点
    slow->next = NULL;        // 断开前后链表

    NODE *pre = NULL;
    NODE *next = NULL;

    while(cur != NULL)
    {
        next = cur->next;     // 保存后继
        cur->next = pre;      // 反转指针
        pre = cur;            // pre前移
        cur = next;           // cur前移
    }
    // 此时 pre 为逆置后链表头（即 a_n）

    /********** 3. 交叉合并两个链表 **********/
    NODE *front = L->next;  // 前半部分
    NODE *rear = pre;       // 后半部分（已逆置）

    while(front != NULL && rear != NULL)
    {
        NODE *fnext = front->next; // 保存前半后继
        NODE *rnext = rear->next;  // 保存后半后继

        front->next = rear;        // 插入后半结点
        rear->next = fnext;

        front = fnext;             // 前进
        rear = rnext;
    }
}
```

---

### 三、时间复杂度分析

设链表长度为 (n)

#### （1）找中点

* 遍历一次链表
  [
  O(n)
  ]

---

#### （2）逆置链表

* 每个结点访问一次
  [
  O(n)
  ]

---

#### （3）交叉合并

* 每个结点最多访问一次
  [
  O(n)
  ]

---

#### 总时间复杂度

[
T(n) = O(n)
]

---

#### 空间复杂度

* 仅使用常数个辅助指针
  [
  S(n) = O(1)
  ]

---

### 四、考场得分点总结（关键）

如果你这样写，基本可以拿满分：

#### ✔ 思路分（核心）

* 分三步：找中点 → 逆置 → 合并

#### ✔ 实现分

* 正确使用快慢指针
* 三指针逆置
* 合并时保存 `next`

#### ✔ 严谨性

* 判空
* 断链（`slow->next = NULL`）

---

### 五、一句话方法论总结

> **链表重排问题的标准解法是：通过中点分割建立对称结构，再利用逆置将后半部分映射为“反序序列”，最后进行拉链式合并。**



## 4、王道解法

### 20.【解答】

#### （1）算法的基本设计思想

先观察  
$$
L = (a_1, a_2, a_3, \cdots, a_{n-2}, a_{n-1}, a_n)
$$  
和  
$$
L' = (a_1, a_n, a_2, a_{n-1}, a_3, a_{n-2}, \cdots)
$$  

发现 $L'$ 是由 $L$ 摘取第一个元素，再摘取倒数第一个元素，再摘取第二个元素……依次合并而成的。

为了方便链表后半段取元素，需要先将 $L$ 的后半段**原地逆置**。又由于题目要求空间复杂度为 $O(1)$，不能借助栈，否则每取最后一个结点都需要遍历一次链表。

具体步骤如下：

1. 先找出链表 $L$ 的中间结点。为此设置两个指针 $p$ 和 $q$：
   - 指针 $p$ 每次走一步  
   - 指针 $q$ 每次走两步  
   当 $q$ 到达链尾时，指针 $p$ 正好在链表的中间结点

2. 然后将 $L$ 的后半段结点**原地逆置**

3. 从单链表前后两段中依次各取一个结点，按要求重排


#### （2）算法实现

```c
void change_list(NODE *h){
    NODE *p, *q, *r, *s;

    p = q = h;

    // 寻找中间结点
    while(q->next != NULL){
        p = p->next;          // p走一步
        q = q->next;
        if(q->next != NULL)   // q走两步
            q = q->next;
    }

    // 此时 p 指向中间结点，q 指向链尾
    q = p->next;              // q 指向后半段首结点
    p->next = NULL;           // 断开前后两段

    // 将后半段链表逆置（头插法）
    while(q != NULL){
        r = q->next;
        q->next = p->next;
        p->next = q;
        q = r;
    }

    // 交叉合并两个链表
    s = h->next;              // 前半段首结点（插入位置）
    q = p->next;              // 后半段首结点
    p->next = NULL;

    while(q != NULL){
        r = q->next;          // 保存后继
        q->next = s->next;    // 插入到当前结点之后
        s->next = q;

        s = q->next;          // 更新前半段指针
        q = r;                // 后半段前进
    }
}
```

#### （3）时间复杂度分析

* 第一步：寻找中间结点，时间复杂度为
  $$
  O(n)
  $$

* 第二步：逆置后半段链表，时间复杂度为
  $$
  O(n)
  $$

* 第三步：合并链表，时间复杂度为
  $$
  O(n)
  $$

---

**总时间复杂度：**

$$
O(n)
$$

**空间复杂度：**

$$
O(1)
$$


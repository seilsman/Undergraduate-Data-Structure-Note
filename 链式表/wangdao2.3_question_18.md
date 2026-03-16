## 1、题目

下面是将题目内容整理后的 **Markdown 版本**（包含结构说明与公式排版），可以直接用于笔记或文档。


## 18.【2012 统考真题】

假定采用**带头结点的单链表**保存单词，当两个单词有相同的后缀时，
可**共享相同的后缀存储空间**。例如，`loading` 和 `being` 的存储映像如下图所示。

```

str1 → □ → l → o → a → d
                ↓
                 i → n → g → ^

str2 → □ → b → e ───────────┘

```

设 `str1` 和 `str2` 分别指向两个单词所在单链表的**头结点**，  
链表结点结构为：

```

[data | next]

```

请设计一个**时间上尽可能高效的算法**，找出由 `str1` 和 `str2` 所指向
两个链表**共同后缀的起始位置**  
（如图中字符 `i` 所在结点的位置 `p`）。

### 要求

1. 给出算法的**基本设计思想**。
2. 根据设计思想，采用 **C / C++ / Java** 语言描述算法，关键之处给出注释。
3. 说明所设计算法的**时间复杂度**。



## 2、本人胡思乱想环节
实在没招了（因为想不出来一遍遍历就结束的算法，所以这里直接选择看chat给出的提示了）

首先：遍历一遍，得出两个单词的长度len1 len2，随后相减得出单词的长度差
其次：根据第一步给出的长度差，决定快指针的开始位置，随后先让快指针先出发
再者：快慢指针一起启动并开始遍历，当二者指向结点的data值相同，即为最后的共同链表的起始位置



### 代码实现
```C
LNode* SameSrting(LinkList str1,LinkList str2)
{
    int len1 = 0;
    LNode *Len1 = str1;
    while(Len1 -> next != NULL)
    {
        Len1  = Len1 -> next;
        len1++;
    }
    int len2 = 0;
    LNode *Len2 = str2;
    while(Len2 -> next != NULL)
    {
        Len2  = Len2 -> next;
        len2++;
    }
    if(len1 == 0 || len2 ==0)
    {
        return NULL;
    }
    LNode *fast,*slow;
    if(len1 >= len2)
    {
        fast = str1;
        slow = str2;
        for(int i = 0;i< len1-len2; i++)
        {
        fast = fast -> next;
        }
    }
    else
    {
        fast = str2;
        slow = str1;
        for(int i = 0;i< len2-len1; i++)
        {
        fast = fast -> next;
        }
        
    }
    while(fast != NULL && fast != slow )
    {
            fast = fast -> next;
            slow = slow -> next;
    }
    if(fast == NULL || slow == NULL)
    {
        return NULL;
    }
    char c = fast -> data;
    return c;
}
```
### 效率分析

**时间复杂度**：$O(n)$
**空间复杂度**：$O(1)$


### 问题
> 对于尾部同结点的判断有问题。。。应该是地址相同而不是内容相同

## chat大人的解法

链表结点结构：

```

[data | next]

```

设 `str1` 和 `str2` 分别指向两个带头结点的单链表，若两个单词具有相同后缀，则共享同一段链表结点。要求找出两个链表共同后缀的起始结点位置。

---

### 一、算法设计思想

由于两个单词的相同后缀是**共享存储空间**的，因此从某个结点开始，两条链表将**完全重合**。

因此问题可以转化为：

> **寻找两个单链表的第一个公共结点。**

算法思路如下：

1. 分别遍历两个链表，计算其长度 `len1` 和 `len2`。
2. 计算长度差 `d = |len1 - len2|`。
3. 让较长链表的指针先向后移动 `d` 步，使两个指针到链表尾部的距离相同。
4. 然后两个指针同步向后移动：
   - 若 `p == q`，说明找到公共结点，即为共同后缀的起始位置。
   - 若遍历到 `NULL` 仍未相遇，则说明不存在共同后缀。

该方法保证两个指针在**同一位置开始比较尾部结构**，从而在线性时间内找到公共结点。

---

### 二、算法实现（C语言）

```c
typedef struct LNode
{
    char data;
    struct LNode *next;
}LNode,*LinkList;

LNode* SameString(LinkList str1, LinkList str2)
{
    int len1 = 0, len2 = 0;

    LNode *p = str1->next;
    LNode *q = str2->next;

    /* 计算链表 str1 的长度 */
    while(p != NULL)
    {
        len1++;
        p = p->next;
    }

    /* 计算链表 str2 的长度 */
    while(q != NULL)
    {
        len2++;
        q = q->next;
    }

    LNode *fast, *slow;

    /* 让 fast 指向较长链表 */
    if(len1 >= len2)
    {
        fast = str1->next;
        slow = str2->next;

        /* 长链表先走长度差 */
        for(int i = 0; i < len1 - len2; i++)
            fast = fast->next;
    }
    else
    {
        fast = str2->next;
        slow = str1->next;

        for(int i = 0; i < len2 - len1; i++)
            fast = fast->next;
    }

    /* 同步遍历寻找公共结点 */
    while(fast != NULL && slow != NULL)
    {
        if(fast == slow)     // 指针相同说明是同一结点
            return fast;

        fast = fast->next;
        slow = slow->next;
    }

    return NULL;  // 不存在共同后缀
}
```

---

### 三、时间复杂度分析

设：

* 链表 `str1` 长度为 `m`
* 链表 `str2` 长度为 `n`

算法步骤复杂度：

| 步骤     | 复杂度            |     |    |
| ------ | -------------- | --- | -- |
| 计算链表长度 | $O(m+n)$       |     |    |
| 长度差移动  | $O(            | m-n | )$ |
| 同步遍历   | $O(\min(m,n))$ |     |    |

因此总时间复杂度为：

$$
O(m+n)
$$

空间复杂度为：

$$
O(1)
$$

因为算法只使用了常数个辅助指针。

---

### 四、算法特点总结

该算法利用了**共享后缀链表的结构特点**：

* 相同后缀结点的**存储地址完全相同**
* 因此判断公共后缀只需比较**指针是否相等**

通过**长度对齐策略**保证两个指针到链表尾部的距离相同，从而在一次同步遍历中找到公共结点。


---

## 一个考试中的关键细节提醒 ⚠️

很多人会写成：

```c
fast->data == slow->data
```

这是 **错误的**。

必须写成：

```c
fast == slow
```

因为题目强调的是：

> **共享存储空间**

即 **同一结点地址**。




## 4、王道解法

顺序遍历两个链表到尾结点时，并不能保证两个链表同时到达尾结点。这是因为两个链表的长度不同。假设一个链表比另一个链表长 $k$ 个结点，我们先在长链表上遍历 $k$ 个结点，之后同步遍历两个链表，这样就能够保证它们同时到达最后一个结点。因为两个链表从第一个公共结点到链表的尾结点都是重合的，所以它们肯定同时到达第一个公共结点。

### 1) 算法的基本设计思想：

① 分别求出 `str1` 和 `str2` 所指的两个链表的长度 $m$ 和 $n$。

② 将两个链表以表尾对齐：令指针 `p`、`q` 分别指向 `str1` 和 `str2` 的头结点，若 $m \ge n$，则指针 `p` 先走，使 `p` 指向链表中的第 $m - n + 1$ 个结点；若 $m < n$，则使 `q` 指向链表中的第 $n - m + 1$ 个结点，即使指针 `p` 和 `q` 所指的结点到表尾的长度相等。

③ 反复将指针 `p` 和 `q` 同步向后移动，并判断它们是否指向同一结点。当 `p`、`q` 指向同一结点，则该点即所求的共同后缀的起始位置。

---

### 2) 本题代码如下：

```c
typedef struct Node {
    char data;
    struct Node *next;
} SNode;

/* 求链表长度的函数 */
int listlen(SNode *head) {
    int len = 0;
    while (head->next != NULL) {
        len++;
        head = head->next;
    }
    return len;
}

/* 找出共同后缀的起始地址 */
SNode* find_list(SNode *str1, SNode *str2) {
    int m, n;
    SNode *p, *q;
    m = listlen(str1);          // 求 str1 的长度, O(m)
    n = listlen(str2);          // 求 str2 的长度, O(n)
    
    for (p = str1; m > n; m--)  // 若 m > n, 使 p 指向链表中的第 m-n+1 个结点
        p = p->next;
    
    for (q = str2; m < n; n--)  // 若 m < n, 使 q 指向链表中的第 n-m+1 个结点
        q = q->next;
    
    while (p->next != NULL && p->next != q->next) { // 查找共同后缀起始点
        p = p->next;            // 两个指针同步向后移动
        q = q->next;
    }
    
    return p->next;             // 返回共同后缀的起始地址
}

```

---

### 3) 时间复杂度：

时间复杂度为 $O(len1 + len2)$ 或 $O(\max(len1, len2))$，其中 $len1$、$len2$ 分别为两个链表的长度。

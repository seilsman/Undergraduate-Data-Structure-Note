## 1、题目

### 17.【2009 统考真题】

已知一个**带有表头结点的单链表**，结点结构为：

| data | link |
|------|------|

假设该链表只给出了头指针 `list`。在**不改变链表的前提下**，请设计一个**尽可能高效的算法**，查找链表中**倒数第 $k$ 个位置**上的结点。（$k$ 为正整数）

若查找成功，算法输出该结点 `data` 域的值，并返回 `1`；否则返回 `0`。

要求：

1. **描述算法的基本设计思想。**

2. **描述算法的详细实现步骤。**

3. 根据设计思想和实现步骤，采用程序设计语言描述算法  
   （使用 **C、C++ 或 Java** 语言实现），**关键之处给出简要注释**。

## 2、本人胡思乱想环节
个人感觉可以这样：
先放出第一个指针进行遍历，遍历到第K个指针的时候，“放出”第二个指针进行遍历，这样可以少设置一个辅助变量，在前一个指针指向末尾的时候两个指针都停下来，此时两个指针的距离恒定为k，直接取出第二个指针即可



### 代码实现
```C
int SearchNode(Linklist list,int k)
{
  LNode *fast = list;//初始化fast结点，开始遍历链表前n个结点
  for(int i = 0; i < k ; i++)
  {
    if(fast == NULL)//如果fast为空，代表此时fast指针已遍历到链表末尾，链表结点数量小于k
    {
      return 0;
    }
    fast = fast -> link;//判断无误后遍历下一个结点，此时i代表此时的fast指针处在第几个结点
  }
  LNode *slow = list -> link;//初始化slow结点为头结点的下一个结点，代表这个链表的第一个结点，同时与fast结点保持k-1个结点的距离
  while(fast -> link != NULL)//遍历，直到fast到达链表的末尾，此时slow指针指向倒数第k个结点
  {
    fast = fast -> link;
    slow = slow -> link;
  }
  printf("%d",slow->data);
  return 1;
}

```
### 效率分析

**时间复杂度**：$O(n)$
**空间复杂度**：$O(1)$


### 问题
> 算法基本正确，快慢指针是对的  

> printf格式没记起来，return值没看见。。。。

## 3、chat大人给出的算法

### （1）算法的基本设计思想

采用**快慢指针法**。

设两个指针 `fast` 和 `slow`：

1. 先让指针 `fast` 从头结点开始向后移动 `k` 个结点。
2. 然后令 `slow` 指向链表的第一个数据结点。
3. 接着让 `fast` 和 `slow` 同时向后移动，每次各移动一个结点。
4. 当 `fast` 到达链表的最后一个结点时，`slow` 所指向的结点即为链表的**倒数第 $k$ 个结点**。

若在 `fast` 前进 `k` 步的过程中遇到空指针，说明链表长度小于 $k$，查找失败。

该方法只需一次遍历链表即可完成查找。

时间复杂度为：

$$
O(n)
$$

空间复杂度为：

$$
O(1)
$$


### （2）算法的实现步骤

1. 定义两个指针 `fast` 和 `slow`。
2. 初始化 `fast` 指向链表头结点 `list`。
3. 令 `fast` 向后移动 `k` 次：
   - 若在移动过程中 `fast == NULL`，说明链表长度小于 $k$，返回 `0`。
4. 初始化 `slow` 指向链表的第一个数据结点 `list->link`。
5. 当 `fast->link != NULL` 时，重复执行：
   - `fast = fast->link`
   - `slow = slow->link`
6. 循环结束时，`slow` 所指结点即为倒数第 $k$ 个结点。
7. 输出该结点的 `data` 域，并返回 `1`。

### （3）算法描述（C语言）

```c
int SearchNode(LinkList list, int k)
{
    LNode *fast = list;        // fast指针从头结点开始

    // fast先向后移动k个结点
    for(int i = 0; i < k; i++)
    {
        if(fast == NULL)       // 若链表长度小于k，则查找失败
        {
            return 0;
        }
        fast = fast->link;
    }

    LNode *slow = list->link;  // slow指向第一个数据结点

    // fast与slow同步移动
    while(fast->link != NULL)
    {
        fast = fast->link;
        slow = slow->link;
    }

    printf("%d", slow->data);  // 输出倒数第k个结点的数据
    return 1;
}
```


## 4、王道解法
### 1）算法的基本设计思想

问题的关键是设计一个尽可能高效的算法，通过链表的一次遍历，找到倒数第 $k$ 个结点的位置。  
定义两个指针变量 $p$ 和 $q$，初始时均指向头结点的下一个结点（链表的第一个结点），$p$ 指针沿链表移动；当 $p$ 指针移动到第 $k$ 个结点时，$q$ 指针开始与 $p$ 指针同步移动；当 $p$ 指针移动到最后一个结点时，$q$ 指针所指示结点为倒数第 $k$ 个结点。以上过程对链表仅进行一次扫描。

---

### 2）算法的详细实现步骤

1. `count = 0`，$p$ 和 $q$ 指向链表头结点的下一个结点。
2. 若 $p$ 为空，转步骤⑤。
3. 若 `count` 等于 $k$，则 $q$ 指向下一个结点；否则，`count = count + 1`。
4. $p$ 指向下一个结点，转步骤②。
5. 若 `count` 等于 $k$，则查找成功，输出该结点 `data` 域的值，返回 `1`；否则说明 $k$ 值超过了线性表的长度，查找失败，返回 `0`。
6. 算法结束。

---

### 3）算法实现如下

```c
typedef int ElemType;                 // 链表数据的类型定义

typedef struct LNode{                 // 链表结点的结构定义
    ElemType data;                    // 结点数据
    struct LNode *link;               // 结点链接指针
}LNode, *LinkList;

int Search_k(LinkList list, int k){
    LNode *p = list->link, *q = list->link;   // 指针p、q指向第一个结点
    int count = 0;

    while(p != NULL){                 // 遍历链表直到最后一个结点
        if(count < k)
            count++;                  // 计数，若count < k 只移动p
        else
            q = q->link;              // 之后p、q同步移动
        p = p->link;
    }

    if(count < k)
        return 0;                     // 查找失败返回0
    else{
        printf("%d", q->data);        // 否则打印并返回1
        return 1;
    }
}
```
## 1、题目

**16**

设有一个长度为 $n$（$n$ 为偶数）的**不带头结点的单链表**，且结点值都大于 $0$。  
设计一个算法求这个单链表的**最大孪生和**。

孪生和定义为：  
一个结点值与其孪生结点值之和。

对于第 $i$ 个结点（从 $0$ 开始），其孪生结点为第 $n-i-1$ 个结点。

要求：

1. 给出算法的基本设计思想。  
2. 根据设计思想，采用 **C 或 C++ 语言**描述算法，关键之处给出注释。  
3. 说明你的算法的**时间复杂度**和**空间复杂度**。

## 2、本人胡思乱想环节
已经想到了可以尝试将后半部分的链表进行逆置然后双指针遍历,但是没想到可以用快慢指针的思想,这样既可以确定中间的结点,又可以方便进行逆置
Step 1:通过快慢指针最后将slow指针指向后一半链表的头结点,同时将fast指针调整到最后一个结点
Step 2:新建第三个指针,应用尾插法将后半链表进行逆置
Step 3:此时fast指针指向逆置后的链表的第一个结点,直接利用即可,通过遍历求得最大值

### 代码实现
```C
int SumCompany(LinkList L)
{
  LNode *slow = L;
  LNode *fast = L;
  while(fast -> next != NULL && fast -> next -> next != NULL)//快慢指针法,找到前一半链表的尾部结点和整个链表的尾部结点
  {
    slow = slow -> next;
    fast = fast -> next ->next;
  }
  if(fast -> next != NULL)
    fast = fast -> next;//将fast安插在真正的尾部结点

  LNode *cur = slow -> next;//三指针法:将后半部分的链表进行逆置
  LNode *pre = NULL;
  LNode *next = cur -> next;
  while(cur != NULL)
  {
    next = cur -> next;
    cur -> next = pre;
    pre = cur;
    cur = next;
    
  }
  
  LNode *cycle = L;//开始循环:双指针遍历链表,找出最大值
  int sum = L-> data + pre -> data;
  while(pre != NULL)
  {
    if(cycle -> data + pre -> data > sum)
    {
      sum = cycle -> data + pre -> data;
    }
    cycle = cycle -> next;
    pre = pre -> next;
  }
  return sum;

}

```
### 效率分析

**时间复杂度**：
**空间复杂度**：


### 问题
> Congraduationa!这道题做的完全正确（从算法角度上）

> 只不过可以更优

## 3、chat大人给出的算法


### 一、算法基本设计思想

设单链表长度为 (n)（(n) 为偶数），孪生结点满足关系：

$$[
(i,;n-i-1)
]$$

若直接在链表中寻找这两个位置的结点，需要反复遍历链表，效率较低。因此可通过**结构变换使孪生结点在遍历中对齐**。

算法思想如下：

1. **使用快慢指针遍历链表**

   * `fast` 每次前进两步
   * `slow` 每次前进一步

2. 在 `slow` 前进的同时，**利用三指针法原地逆置 slow 之前的链表部分**，使得前半部分链表逐渐变为逆序结构。

3. 当 `fast` 到达链表末尾时：

   * `pre` 指向 **逆序后的前半链表头结点**
   * `slow` 指向 **后半链表头结点**

   此时两个指针所指链表正好构成孪生关系。

4. 之后使用两个指针同步向后遍历：

   * 计算 `pre->data + slow->data`
   * 不断更新最大孪生和

5. 当任一指针到达 `NULL` 时结束遍历，即得到最大孪生和。

该算法在寻找中点的同时完成链表逆置，从而减少了一次单独的逆置遍历。

---

### 二、C语言算法实现

```c
int SumCompany(LinkList L)
{
    LNode *slow = L;
    LNode *fast = L;

    LNode *pre = NULL;   // 保存逆序后的前半链表
    LNode *next = NULL;

    /* 快慢指针遍历，同时逆置前半部分链表 */
    while(fast != NULL && fast->next != NULL)
    {
        fast = fast->next->next;

        next = slow->next;
        slow->next = pre;
        pre = slow;
        slow = next;
    }

    /* 此时：
       pre  指向逆序后的前半链表
       slow 指向后半链表
    */

    int maxSum = 0;

    while(pre != NULL && slow != NULL)
    {
        int cur = pre->data + slow->data;
        if(cur > maxSum)
            maxSum = cur;

        pre = pre->next;
        slow = slow->next;
    }

    return maxSum;
}
```

---

### 三、算法复杂度分析

#### 1 时间复杂度

算法主要包含两个阶段：

1. 快慢指针遍历链表并同步逆置前半部分
   访问每个结点一次
$$
[
O(n)
]
$$
2. 计算孪生和
   需要遍历 (n/2) 个结点
$$
[
O(n)
]
$$
因此算法总时间复杂度为：
$$
[
O(n)
]
$$
---

#### 2 空间复杂度

算法只使用了若干辅助指针变量：

* `slow`
* `fast`
* `pre`
* `next`

未使用额外存储结构，因此空间复杂度为：
$$
[
O(1)
]
$$
---

### 方法论总结（一句话）

> **利用快慢指针寻找链表中点，并在 slow 前进过程中同步逆置前半链表，使前后两部分形成孪生对齐结构，从而在线性遍历中求得最大孪生和。**

---

如果从**数据结构题型角度**来看，这道题其实是一个非常典型的组合模型：

```text
快慢指针找中点
        +
链表原地逆置
        +
双指针扫描
```



## 4、王道解法

### 16.【解答】

#### 1）算法的基本设计思想

设置快、慢两个指针分别为 `fast` 和 `slow`。  
初始时 `slow` 指向 `L`（第一个结点），`fast` 指向 `L->next`（第二个结点）。之后 `slow` 每次走一步，`fast` 每次走两步。

当 `fast` 指向表尾（第 $n$ 个结点）时，`slow` 正好指向链表的中点（第 $n/2$ 个结点）。  
然后，将链表的**后半部分逆置**，再设置两个指针分别指向链表前半部分和后半部分的首结点，在遍历过程中计算两个指针所指结点的元素之和，并维护最大值。

---

#### 2）本题代码如下

```c
int PairSum(LinkList L){
    LNode *fast = L->next, *slow = L;     // 利用快慢双指针找到链表的中间点
    while(fast != NULL && fast->next != NULL){
        fast = fast->next->next;          // 快指针每次走两步
        slow = slow->next;                // 慢指针每次走一步
    }

    LNode *newHead = NULL, *p = slow->next, *tmp;
    while(p != NULL){                     // 反转链表后一半部分的元素，采用头插法
        tmp = p->next;                    // p 指向当前待插入结点
        p->next = newHead;                // 将 p 所指结点插入到新链表的首结点之前
        newHead = p;                      // newHead 指向刚才新插入的结点，作为新的首结点
        p = tmp;                          // 当前处理结点变为下一个结点
    }

    int mx = 0;
    p = L;
    LNode *q = newHead;

    while(q != NULL){                     // 用 p 和 q 分别遍历两个链表
        if((p->data + q->data) > mx)      // mx 记录最大值
            mx = p->data + q->data;

        p = p->next;
        q = q->next;
    }

    return mx;
}
````

---

#### 3）复杂度分析

* **时间复杂度**

  * 快慢指针寻找中点：$$O(n)$$
  * 逆置后半部分链表：$$O(n)$$
  * 计算孪生和：$$O(n)$$

  因此总体时间复杂度为：

  $$
  O(n)
  $$

* **空间复杂度**

  算法只使用了若干辅助指针变量，没有使用额外存储结构，因此空间复杂度为：

  $$
  O(1)
  $$


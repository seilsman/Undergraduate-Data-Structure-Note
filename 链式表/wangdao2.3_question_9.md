## 1、题目
### 算法题目：求两个递增有序链表的交集（结果存入 A）


已知两个链表 $A$ 和 $B$ 分别表示两个集合，其元素递增排列。  
编制函数，求 $A$ 与 $B$ 的交集，并存放于 $A$ 链表中。

## 2、本人胡思乱想环节
这道题与第八题最大的区别就是:要求改变原来一个表中的内容,并且输出


### 代码实现
```C
void RemainSame(LinkList &A,LinkList B)
{
    LNode *preA = A;
    LNode *preB = B;
    while(preA -> next != NULL && preB -> next != NULL)
    {
        if(preA -> next -> data == preB -> next -> data)
        {
            preA = preA -> next;
            preB = preB -> next;
        }
        else if(preA -> next -> data < preB -> next -> data)
        {
            LNode *curA = preA -> next;
            preA -> next = curA -> next;
            free(curA);
        }
        else
        {
            preB = preB -> next;
        }
    }
    while(preA -> next != NULL)
    {
        LNode *pa = preA -> next;
        preA -> next = pa -> next;
        free(pa);
    }
}




```
### 问题
> 删除操作逻辑有瑕疵,将A集合中非重复元素删除后不应该后移指针(因为我使用的是前驱指针的模型),这将导致少判断一个指针,类型有误
> 进行A和B交集操作后,未对A元素剩余未比较的结点进行删除操作
> 进行删除操作的时候,误将"删除"仅仅理解成"断链",实际上需要释放冗余结点的内存空间,保证内存管理的完整性和算法逻辑的完备性

## 3、chat大人给出的算法
### 算法：求两个递增有序链表的交集（结果存入 A）

### 算法思想

链表 $A$ 与 $B$ 均为递增有序表，因此可以采用 **双指针同步扫描** 的方法：

1. 设两个指针分别扫描 $A$ 和 $B$。
2. 若当前元素相等，则该元素属于交集，两个指针同时后移。
3. 若 $A$ 的当前元素小于 $B$ 的当前元素，则该元素不可能属于交集，应从 $A$ 中删除。
4. 若 $A$ 的当前元素大于 $B$ 的当前元素，则 $B$ 指针后移。
5. 若 $B$ 遍历结束而 $A$ 仍有剩余元素，则这些元素不可能再属于交集，需要全部删除。

时间复杂度：

$$
O(m+n)
$$

其中 $m,n$ 分别为两个链表的长度。

空间复杂度：

$$
O(1)
$$

因为算法是在原链表上进行操作，没有额外申请辅助存储。

---

### C语言实现

```c
void RemainSame(LinkList &A, LinkList B)
{
    // preA 指向 A 中当前处理结点的前驱结点
    // preB 指向 B 中当前处理结点的前驱结点
    LNode *preA = A;
    LNode *preB = B;

    // 同时扫描两个链表
    while (preA->next != NULL && preB->next != NULL)
    {
        // 当前结点元素相等，属于交集，两个链表同时后移
        if (preA->next->data == preB->next->data)
        {
            preA = preA->next;
            preB = preB->next;
        }
        // A 的当前元素较小，不可能属于交集，需要删除
        else if (preA->next->data < preB->next->data)
        {
            LNode *curA = preA->next;   // 保存待删除结点
            preA->next = curA->next;    // 从链表中断开
            free(curA);                 // 释放结点空间，防止内存泄漏
        }
        // B 的当前元素较小，仅移动 B 的指针
        else
        {
            preB = preB->next;
        }
    }

    // 若 B 已遍历结束而 A 仍有剩余元素
    // 由于链表递增有序，这些元素不可能属于交集
    // 因此需要全部删除
    while (preA->next != NULL)
    {
        LNode *pa = preA->next;     // 保存待删除结点
        preA->next = pa->next;      // 从链表中断开
        free(pa);                   // 释放结点空间
    }
}
```
## 4、王道解法
### 算法思想

采用归并的思想，设置两个工作指针 pa 和 pb，对两个链表进行归并扫描，只有同时出现在两个集合中的元素才链接到结果表中且仅保留一个，其他的结点全部释放。当一个链表遍历完毕后，释放另一个表中剩下的全部结点。
---

### C语言代码

```c
LinkList Union(LinkList &la, LinkList &lb) {
    LNode *pa = la->next;           // 设工作指针分别为 pa 和 pb
    LNode *pb = lb->next;
    LNode *u, *pc = la;             // 结果表中当前合并结点的前驱指针 pc
    while (pa && pb) {
        if (pa->data == pb->data) { // 交集并入结果表中
            pc->next = pa;          // A 中结点链接到结果表
            pc = pa;
            pa = pa->next;
            u = pb;                 // B 中结点释放
            pb = pb->next;
            free(u);
        }
        else if (pa->data < pb->data) { // 若 A 中当前结点值小于 B 中当前结点值
            u = pa;
            pa = pa->next;          // 后移指针
            free(u);                // 释放 A 中当前结点
        }
        else {                      // 若 B 中当前结点值小于 A 中当前结点值
            u = pb;
            pb = pb->next;          // 后移指针
            free(u);                // 释放 B 中当前结点
        }
    } // while 结束
    
    while (pa) {                    // B 已遍历完，A 未完
        u = pa;
        pa = pa->next;
        free(u);                    // 释放 A 中剩余结点
    }
    
    while (pb) {                    // A 已遍历完，B 未完
        u = pb;
        pb = pb->next;
        free(u);                    // 释放 B 中剩余结点
    }
    
    pc->next = NULL;                // 置结果链表尾指针为 NULL
    free(lb);                       // 释放 B 表的头结点
    return la;
}
```
### 复杂度分析
链表归并类型的试题在各学校历年真题中出现的频率很高，故应扎实掌握解决此类问题的思想。该算法的时间复杂度为 $O(len1 + len2)$，空间复杂度为 $O(1)$。
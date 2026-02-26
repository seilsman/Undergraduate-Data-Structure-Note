## 1、题目
### 算法题目：删除递增单链表中的重复元素

**题目描述：**
在一个递增有序的单链表中，存在重复的元素。设计算法删除重复的元素。

**示例：**
* **输入：** `(7, 10, 10, 21, 30, 42, 42, 42, 51, 70)`
* **输出：** `(7, 10, 21, 30, 42, 51, 70)`

## 2、本人胡思乱想环节
已知递增表，那么直接使用删除操作即可，注意边界判定

### 代码实现
```C
void DeleteMore(LinkList L)
{
    LNode *pre = L -> next;
    while(pre -> next != NULL)
    {
        if(pre -> data == pre -> next -> data)
        {
            LNode *cur = pre -> next;
            pre -> next = cur -> next;
            free(cur);
        }
        else
        pre = pre -> next;
    }
}
```
### 问题
只有一个：没有注意pre指针的初始化应该为指向L之后第一个结点，因为这个结点才是有效的结点
## 3、chat大人给出的算法

### 算法实现（C 语言）
```C
void DeleteMore(LinkList L) {
    if (L == NULL || L->next == NULL) return; // 容错处理

    LNode *pre = L->next; // pre 指向第一个有效的“首元结点”
    while (pre->next != NULL) {
        if (pre->data == pre->next->data) {
            LNode *cur = pre->next;
            pre->next = cur->next;
            free(cur); // 释放重复节点的内存
            // 注意：这里不需要移动 pre，因为要继续比较新的 next
        } else {
            pre = pre->next; // 只有不相等时才向后移动
        }
    }
}
```
## 4、王道解法
### 算法思想

循环遍历链表 C：
由于题目中的链表是有序表，因此所有相同值域的结点都是相邻的。使用指针 p 扫描递增单链表 L，若 *p 结点的值域等于其后继结点的值域，则删除后者，否则 p 移向下一个结点。

### 本题代码如下

```c
void Del_Same(LinkList &L) {
    LNode *p = L->next, *q;      // p 为扫描工作指针
    if (p == NULL)
        return;
    while (p->next != NULL) {
        q = p->next;            // q 指向 *p 的后继结点
        if (p->data == q->data) { // 找到重复值的结点
            p->next = q->next;  // 将 *q 结点从链表中断开
            free(q);            // 释放相同元素值的结点
        }
        else
            p = p->next;        // 继续扫描下一个结点
    }
}
```

### 复杂度分析 
 - 时间复杂度：O（n）
 - 空间复杂度：O（1）
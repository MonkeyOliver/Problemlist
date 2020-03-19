# xjb模板

## 二分诀窍

- 求下界，即求满足x>=val或x>val的最**小**x，左闭右开返回下界
- 求上界，即求满足x<=val或x<val的最**大**x，左闭右开返回下界-1

代码：

    LL l = 0, r = n + 1;
    while (l < r) {
        int m = (l + r) / 2;
        if (judge(m))l = m + 1;//上下界judge()略有不同
        else r = m;
    }
    return l;//下界
    return l - 1;//上界

## 取数x的第i位

    (x >> i) & 1

## 查找单向链表中间节点

    ListNode *ptr = head, *tmp = head, tail = nullptr;
    while( tmp != tail && tmp->next != tail )ptr = ptr->next, tmp = tmp->next->next;

## 反转链表（照代码画个图自然而然就出来了）

    ListNode n(0);
    n.next = head;
    ListNode *pre = &n, *cur = head;
    while(cur->next) {
        ListNode *tmp = pre->next;//这句老写错
        pre->next = cur->next;
        cur->next = cur->next->next;
        pre->next->next = tmp;
    }
    return n.next;

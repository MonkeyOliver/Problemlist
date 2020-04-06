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

关于C++的lower_bound()和upper_bound()：

设一个数k，想象一个升序数组a0，a1，a2……k，k，k，ak……an-1，low所指向的是第一个k的位置，up指向的是ak的位置。

如果该数组中没出现k，即a0，a1，a2……ak-1，ak……an-1（ak-1 < k < ak），那么low和up都指向ak的位置。

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

## 快慢指针法判链表环

    ListNode *slow = head, *fast = head, *ans = head;
    while (fast->next && fast->next->next) {
        slow = slow->next, fast = fast->next->next;
        if (slow == fast) {
            while(slow != ans) {
                slow  = slow->next;
                ans = ans->next;
            }
            return ans;
        }
    }
    return nullptr;

## O(n)求数组第k大

    int partition(vector<int>& nums, int l, int r) {
        if (l == r) return l;
        int pivot = nums[l];
        swap(nums[l], nums[r]);
        int cur = l;
        for (int k = l; k < r; k++)
            if (nums[k] <= pivot) swap(nums[k], nums[cur++]);
        swap(nums[cur], nums[r]);
        return cur;
    }

    int findKthLargest(vector<int>& nums, int k) {
        int len = nums.size(), pos = 0;
        int tgt = len - k;//求第k小把这句改了就行
        int l = 0, r = len - 1;
        while ((pos = partition(nums, l, r)) != tgt) {
            if (pos < tgt)l = pos + 1;
            else if (pos > tgt)r = pos - 1;
        }
        return nums[pos];
    }

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

设一个数k，想象一个升序数组a0，a1，a2……**k**，k，k，**ak**……an-1，low所指向的是第一个k的位置，up指向的是ak的位置。

如果该数组中没出现k，即a0，a1，a2……ak-1，**ak**……an-1（ak-1 < k < ak），那么low和up都指向ak的位置。

Python中起同样作用的函数是bisect.bisect_left()和bisect.bisect()（或者bisect.bisect_right()）

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

## 链表插入排序

    ListNode* insertionSortList(ListNode* head) {
        ListNode *dummy = new ListNode(0);
        dummy->next = head;
        ListNode *pre = dummy, *cur = head;
        while(cur) {
            if(cur->next && cur->next->val < cur->val) {
                while(pre->next && pre->next->val < cur->next->val)pre = pre->next;//找插入点
                ListNode *tmp = pre->next;//从插入点断开
                pre->next = cur->next;//将cur->next插到pre后面
                cur->next = cur->next->next;//删掉原先的cur->next
                pre->next->next = tmp;//接上
                pre = dummy;//回到链表头部
            }
            else cur = cur->next;
        }
        return dummy->next;
    }

## 数组插入排序

    void insertionSortArray(vector<int>& nums) {
        for(int i = 1; i < nums.size(); i++) {
            int k = nums[i], j = i - 1;
            while(j >= 0 && nums[j] > k)nums[j + 1] = nums[j], j--;
            nums[j + 1] = k;
        }
    }

## 数组快速排序

    void quickSort(vector<int>& nums, int l, int r){
        if(l >= r) return;
        int i = l;
        for(int j = l; j <= r - 1; j++) {
            if(nums[j] <= nums[r]){
                swap(nums[i], nums[j]);
                i++;
            }
        }
        swap(nums[i], nums[r]);
        quickSort(nums, l, i - 1);
        quickSort(nums, i + 1, r);
    }

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

## 二叉树相关

    template <typename T = int>
    class Node {
    public:
        T val;
        Node<T> *left, *right;
        void print() { std::cout << val << std::endl; }
    };

### 前序遍历

    void preOrderRecursive(Node<> *rt) {
        rt->print();
        if (rt->left) preOrderRecursive(rt->left);
        if (rt->right) preOrderRecursive(rt->right);
    }

    void preOrder(Node<> *rt) {
        std::stack<Node<> *> stk;
        stk.push(rt);
        while (!stk.empty()) {
            auto cur = stk.top();
            stk.pop();
            cur->print();
            //注意顺序
            if (cur->right) stk.push(cur->right);
            if (cur->left) stk.push(cur->left);
        }
    }

### 中序遍历

    void inOrderRecursive(Node<> *rt) {
        if (rt->left) inOrderRecursive(rt->left);
        rt->print();
        if (rt->right) inOrderRecursive(rt->right);
    }

    void inOrder(Node<> *rt) {
        std::stack<Node<> *> stk;
        while (rt || !stk.empty()) {
            while (rt) {
                stk.push(rt);
                rt = rt->left;
            }
            auto cur = stk.top();
            stk.pop();
            cur->print();
            rt = cur->right;
        }
    }

### 后序遍历

    void postOrderRecursive(Node<> *rt) {
        if (rt->left) postOrderRecursive(rt->left);
        if (rt->right) postOrderRecursive(rt->right);
        rt->print();
    }

    void postOrder(Node<> *rt) {
        std::stack<Node<> *> stk;
        Node<> *pre = nullptr;
        while (rt || !stk.empty()) {
            while (rt) {
                stk.push(rt);
                rt = rt->left;
            }
            auto cur = stk.top();
            if (cur->right && pre != cur->right)rt = cur->right;
            else {
                cur->print();
                pre = cur;
                stk.pop();
            }
        }
    }

### 已知前序中序重建

    Node<>* buildTree(vector<int>& pre, int rt, int n, vector<int>& inorder, int l, int r) {
        if (rt >= n || l >= n)return nullptr;
        int pos = -1;
        for (int i = l; i < r; i++)if (inorder[i] == pre[rt]) { pos = i; break; }
        int lcnt = pos - l;
        Node<>* node = new Node<>(pre[rt]);
        node->left = buildTree(pre, rt + 1, rt + 1 + lcnt, inorder, l, pos);
        node->right = buildTree(pre, rt + 1 + lcnt, n, inorder, pos + 1, r);
        return node;
    }

### 已知后序中序重建

    Node<>* buildTree(vector<int>& post, int o, int rt, vector<int>& inorder, int l, int r) {
        if (o > rt)return nullptr;
        int pos = -1;
        for (int i = l; i < r; i++)if (inorder[i] == post[rt]) { pos = i; break; }
        int rcnt = r - 1 - pos - 1;
        Node<>* node = new Node<>(post[rt]);
        node->left = buildTree(post, o, rt - 1 - rcnt - 1, inorder, l, pos);
        node->right = buildTree(post, rt - 1 - rcnt, rt - 1, inorder, pos, r);
        return node;
    }

### 已知前序后序重建

    int preIndex = 0, posIndex = 0;
    Node<>* buildTree(vector<int>& pre, vector<int>& post) {
        Node<>* root = new Node<>(pre[preIndex++]);
        if (root->val != post[posIndex])root->left = buildTree(pre, post);
        if (root->val != post[posIndex])root->right = buildTree(pre, post);
        posIndex++;
        return root;
    }

### 已知有序链表重建二叉搜索树（BST）

    BSTNode* buildTree(SortedListNode *head, SortedListNode *tail){
        if(head == tail)return nullptr;
        if(head->next == tail)return new BSTNode(head->val);
        SortedListNode *ptr = head, *tmp = head;
        while(tmp != tail && tmp->next != tail)ptr = ptr->next, tmp = tmp->next->next;
        BSTNode *cur = new BSTNode(ptr->val);
        cur->left = buildTree(head, ptr);
        cur->right = buildTree(ptr->next, tail);
        return cur;
    }

### 已知有序数组重建二叉搜索树（BST）

    BSTNode* buildTree(vector<int>& SortedArray, int l, int r){
        if(l > r)return nullptr;
        int m = (l + r) / 2;
        BSTNode* cur = new BSTNode(SortedArray[m]);
        cur->left = buildTree(SortedArray, l, m - 1);
        cur->right = buildTree(SortedArray, m + 1, r);
        return cur;
    }

## 链表

### 链表定义：

单链表的节点

```cpp
struct ListNode{
	int val;
    ListNode *next;
    ListNode(int x): val(x), next(nullptr){}
};
```

### 链表初始化

方式1：

```cpp
ListNode dummy(-1);
dummy.next = head;

ListNode* prev = &dummy;
return dummy.next;
```

方式2：

```cpp
ListNode* dummy = new ListNode(-1);
dummy->next = head;

ListNode* prev = dummy;
return dummy->next;
```

链表

```cpp
#include <iostream>
#include <vector>

struct ListNode {
     int val;
     ListNode *next;
     ListNode(int x) : val(x), next(nullptr) {}
};

class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        // 使用prenode而不需要单独考虑头节点，以简化代码
        ListNode *prenode = new ListNode(0); //dummyNode
        ListNode *lastnode = prenode;
        int val = 0;
        while(val || l1 || l2) {
            val = val + (l1?l1->val:0) + (l2?l2->val:0);
            lastnode->next = new ListNode(val % 10);
            lastnode = lastnode->next;
            val = val / 10;
            l1 = l1?l1->next:nullptr;
            l2 = l2?l2->next:nullptr;
        }
        ListNode *res = prenode->next;
        delete prenode; // 释放额外引入的prenode
        return res;
    }
};

ListNode* generateListNode(std::vector<int> vals);
void freeListNode(ListNode* head);
void printListNode(ListNode* head);

int main()
{
    auto list1 = generateListNode({1, 4, 6});
    auto list2 = generateListNode({9, 4, 6, 9});
    printListNode(list1);
    printListNode(list2);
    Solution s;
    auto sum = s.addTwoNumbers(list1, list2);
    printListNode(sum);
    freeListNode(list1);
    freeListNode(list2);
    freeListNode(sum);
    return 0;
}

ListNode* generateListNode(std::vector<int> vals) 
{
    ListNode *res = nullptr;
    ListNode *last = nullptr;
    for(auto val : vals) {
        if(last) {
            last->next = new ListNode(val);
            last = last->next;
        }
        else {
            res = new ListNode(val);
            last = res;
        }
    }
    return res;
}

/*
ListNode* creatList(vector<int> myVec)
{
    ListNode* pHead=new ListNode(myVec[0]);
    ListNode* prev = pHead;
    for(int i=1;i<myVec.size()-1;i++)
    {
        pHead->next = new ListNode(myVec[i]);
        pHead=pHead->next;
    }
    return prev;
}
*/

void freeListNode(ListNode* head)
{
    ListNode* node = head;
    while(node) {
        auto temp = node->next;
        delete node;
        node = temp;
    }
}

void printListNode(ListNode* head)
{
    ListNode* node = head;
    while(node) {
        std::cout << node->val << ", ";
        node = node->next;
    }
    std::cout << std::endl;
}

//链接：https://leetcode-cn.com/problems/add-two-numbers/solution/cjie-ti-de-wan-zheng-dai-ma-bao-gua-sheng-cheng-ce/
```



### 链表环

### 链表相交

 https://blog.csdn.net/u010983881/article/details/78896293 

### 2. 两数相加

#### **题目描述：**

给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。将这两个数相加起来，则返回一个新的链表来表示它们的和。

可以假设除了数字 0 之外，这两个数都不会以 0 开头。

#### **示例：**

```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

#### 题解

- 方法1：转换为整数

     把两个链表转化成两个整数相加，得一个整数后，再把它转化成listnode 。

     事实上，这样做不安全，因为数值会溢出，官方测试数据中有一组测试数据超过100位 

- 方法2：

     使用变量来跟踪进位，并从包含最低有效位的表头开始模拟逐位相加的过程。 

    ![](assets/2_add_two_numbers.svg)

    ```cpp
    class Solution {
    public:
        ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
            
            ListNode* dummyNode = new ListNode(-1);
            ListNode* cur = dummyNode;
            
            int carry = 0; //进位
            while(l1 || l2){
                
                int val1 = l1 ? l1->val : 0;
                int val2 = l2 ? l2->val : 0;
                
                int sum = val1 + val2 + carry;
                carry = sum / 10;
                
                cur->next = new ListNode(sum % 10);
                cur = cur->next;
                
                if(l1)
                    l1 = l1->next;
                if(l2)
                    l2 = l2->next;
            }
            
            if(carry)
                cur->next = new ListNode(carry);
            
            return dummyNode->next;
        }
    };
    ```

    **复杂度分析**

    时间复杂度：O(max(m,n))，假设 m 和 n 分别表示 l1和 l2 的长度，上面的算法最多重复 max(m,n) 次。

    空间复杂度：O(max(m, n))， 新列表的长度最多为max(m,n)+1。

#### **拓展**

如果链表中的数字不是按逆序存储的呢？例如：

```
(3 -> 4 -> 2) + (4 -> 6 -> 5) = (8 -> 0 -> 7) 
```

思路：

使用栈，将两个链表的值分别存入栈中，然后计算每一位的和，将和也保存到栈里。



### 19. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

     第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

    设置两个指针，第一个指针先走n步，然后两个指针一起走

    ![](assets/19.png)

    ```cpp
    class Solution {
    public:
    	ListNode* removeNthFromEnd(ListNode* head, int n) {
    		ListNode* dummyHead = new ListNode(-1);
            dummyHead->next = head;
            
            ListNode* p1  = dummyHead;
            ListNode* p2  = dummyHead;
            for(int i=1; i <= n+1; i++)
                p1 = p1->next;
            
            while(p1 != nullptr){
                p1 = p1->next;
                p2 = p2->next;
            }
            
            p2->next = p2->next->next;
            
            return dummyHead->next; //注意使用new带来的内存泄漏
            
            //ListNode* newHead = dummyHead->next;
            //delete dummyNode;
            //return newHead;
        }
    };
    ```



### 21. 合并两个有序链表

#### **题目描述：**

 将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。  

#### **示例：**

```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

#### 题解

- 方法1：递归

     ```cpp
     //执行用时：12ms，在所有 cpp 提交中击败了78.17%的用户
     class Solution{
     public:
         ListNode* mergeTwoLists(ListNode* l1, ListNode* l2){
             if(l1 ==nullptr)
                 return l2;
             if(l2 == nullptr)
                 return l1;
             
             ListNode* pMergeHead = nullptr;
             if(l1->val < l2->val){
                 pMergeHead = l1;
                 pMergeHead->next = mergeTwoLists(l1->next, l2);
             }
             else{
                 pMergeHead = l2;
                 pMergeHead->next = mergeTwoLists(l1, l2->next);
             }
             
             return pMergeHead;
         }
     };
     ```

     **复杂度分析**

     - 时间复杂度：O(n + m)。 因为每次调用递归都会去掉 l1 或者 l2 的头元素（直到至少有一个链表为空），函数 mergeTwoList 中只会遍历每个元素一次；


     - 空间复杂度：O(n + m)。调用 mergeTwoLists 退出时 l1 和 l2 中每个元素都一定已经被遍历过了，所以 n + m 个栈帧会消耗 O(n+m) 的空间；

- 方法2：迭代

     ```cpp
     //使用new分配空间，在堆中给对象分配空间
     //执行用时：4ms，在所有 cpp 提交中击败了99.99%的用户
     class Solution {
     public:
         ListNode* mergeTwoLists(ListNode* l1, ListNode* l2){
             ListNode* dummyNode = new ListNode(-1);
             ListNode* node = dummyNode;
             
             while(l1 != nullptr && l2 != nullptr){
                 if(l1->val < l2->val){
                     node->next = l1;
                     l1 = l1->next;
                 }
                 else{
                     node->next = l2;
                     l2 = l2->next;
                 }
                 node = node->next;
             }
             node->next = l1 ? l1 : l2;
             
             //return dummy->next //使用new分配的空间，不删除会造成内存泄漏
             ListNode* MergeHead = dummyNode->next;
             delete dummyNode;
             return MergeHead;
         }
     };
     
     //在栈中给对象分配空间(运行最慢的一种方式)
     //执行用时：20ms，在所有 cpp 提交中击败了23.84%的用户
     class Solution{
     public:
         ListNode* mergeTwoLists(ListNode* l1, ListNode* l2){
             ListNode dummyNode = ListNode(-1); //创建栈空间对象
             ListNode* node = & dummyNode;
             
             while(l1 && l2){
                 if(l1->val < l2->val){
                     node->next = l1;
                     l1 = l1->next;
                 }
                 else{
                     node->next = l2;
                     l2 = l2->next;
                 }
                 
                 node = node->next;
             }
             
             node->next = l1 ? l1 : l2;
             return dummyNode.next;
         }
     };
     ```

     **复杂度分析**

     - 时间复杂度：O(n + m) 。因为每次循环迭代中，l1 和 l2 只有一个元素会被放进合并链表中， while 循环的次数等于两个链表的总长度；
     - 空间复杂度：O(1)；

#### 小结

- 方法1和方法2比对可以发现：基于迭代的方法，执行速度明显快于递归；
- 方法2中，使用new创建对象和类的初始化对象，明显使用new的执行速度更快；

### 23. 合并K个排序链表

#### **题目描述：**

  合并 *k* 个排序链表，返回合并后的排序链表。请分析和描述算法的复杂度。 

#### **示例：**

```
输入:
[
  1->4->5,
  1->3->4,
  2->6
]
输出: 1->1->2->3->4->4->5->6
```

#### 题解

- 方法1：暴力法

    - 遍历所有链表，将所有节点的值放到一个数组中
    - 将这个数组排序，然后遍历所有元素得到正确顺序的值
    - 用遍历得到的值，创建一个新的有序链表

    **复杂度分析**

    - 时间复杂度：O(NlogN) ，其中 N 是节点的总数目

        1. 遍历所有的值需花费 O(N) 的时间
        2. 一个稳定的排序算法花费 O(NlogN) 的时间
        3. 遍历同时创建新的有序链表花费 O(N) 的时间
    - 空间复杂度：O(N) 
        1. 排序花费 O(N) 空间（这取决于排序算法）
        2. 创建一个新的链表花费 O(N) 的空间

- 方法2：逐一比较

    - 比较k 个节点（每个链表的首节点），获得最小值的节点。
    - 将选中的节点接在最终有序链表的后面。

    **复杂度分析**

    - 时间复杂度：O(NlogN) ，其中 N 是节点的总数目

        1.  几乎最终有序链表中每个节点的时间开销都为 O(k)（k-1 次比较） 
        2.  总共有 N个节点在最后的链表中
    - 空间复杂度：O(N) 
        1. O(n) 。创建一个新的链表空间开销为 O(n)。
        2. O(1) 。重复利用原来的链表节点，每次选择节点时将它直接接在最后返回的链表后面，而不是创建一个新的节点。

- 方法3：用优先队列优化方法 2

      几乎与上述方法一样，除了将 **比较环节** 用 **优先队列** 进行了优化。 利用

     - 使用mutimap代替优先队列，mutimap会自动对key从小到大排序，可以免去编写比较函数的麻烦。 


     ```cpp
     class Solution {
     public:
         ListNode* mergeKLists(vector<ListNode*>& lists) {
             multimap<int, ListNode*> headMap;
             for (ListNode* l : lists)
             {
                 if (l != NULL)
                     headMap.insert(pair<int, ListNode*>(l->val, l));
             }
             ListNode dummy(0);
             ListNode* tail = &dummy;
             while (!headMap.empty())
             {
                 multimap<int, ListNode*>::iterator iterator = headMap.begin();
                 tail->next = iterator->second;
                 tail = tail->next;
                 if (iterator->second->next != NULL) headMap.insert(pair<int, ListNode*>(iterator->second->next->val, iterator->second->next));
                 headMap.erase(iterator);
             }
             tail->next = NULL;
             return dummy.next;
         }
     };


​     
​     //链接：https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/cshi-yong-mutimaphuo-qu-zui-xiao-zhi-jin-xing-pai-/
​     ```
​    
     -  自定义ListNode * 的比较函数，使得ListNode* 按照val的大小排序，priority_queue在push时排序。 


     ```
     class Solution {
     public:
         struct cmp {
             bool operator()(ListNode* l1, ListNode* l2) {
                 return l1->val > l2->val;
             }
         };
         ListNode* mergeKLists(vector<ListNode*>& lists) {
             ListNode* res = new ListNode(0);
             ListNode* resTmp = res;
             priority_queue<ListNode*, vector<ListNode*>, cmp> pq;
             for(int i = 0; i < lists.size(); ++i) {
                 if(lists[i] != NULL) {
                     pq.push(lists[i]);
                 }
             }
             while(!pq.empty()) {
                 resTmp->next = new ListNode(pq.top()->val);
                 resTmp = resTmp->next;
                 ListNode* topNext = pq.top()->next;
                 pq.pop();
                 if(topNext != NULL) {
                     pq.push(topNext);
                 }
             }
             return res->next;
         }
     };
     
     作者：sunjiayu
     链接：https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/shi-yong-priority_queuewan-cheng-kge-lian-biao-pai/
     来源：力扣（LeetCode）
     著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
     ```
    
     时间复杂度：O(Nlogk) ，其中k 是链表的数目
    
     - 弹出操作时，比较操作的代价会被优化到 O(logk) 。同时，找到最小值节点的时间开销仅仅为 O(1)。
         最后的链表中总共有 N 个节点
    
     空间复杂度：
    
     - O(n) 。创造一个新的链表需要 O(n) 的开销
    
     - O(k) 。以上代码采用了重复利用原有节点，所以只要 O(1) 的空间。同时优先队列（通常用堆实现）需要 O(k) 的空间（远比大多数情况的 N要小）

- 方法4：逐一两两合并链表

    两两合并，基于21题的方法，实现起来最简单。

     基于合并两个链表，取第一个链表与第二个链表合并，然后将合并的链表再与第三个链表合并，依次类型。 

     将合并k 个链表的问题转化成合并 2 个链表k-1 次 

    ```cpp
    class Solution {
    public:
        ListNode* mergeKLists(vector<ListNode*>& lists) {
            int size = lists.size();
            if (size == 0) {
                return nullptr;
            }
            if (size == 1) {
                return lists[0];
            }
            ListNode *p = lists[0];
            for (int i = 1; i < size; ++i) {
                p = merge2(p, lists[i]);
            } 
            return p;
        }
        ListNode* merge2(ListNode *l1, ListNode *l2) {
            ListNode *head = new ListNode(0);
            ListNode *p = head;
            while (l1 && l2) {
                if (l1->val < l2->val) {
                    p->next = l1;
                    l1 = l1->next;
                } else {
                    p->next = l2;
                    l2 = l2->next;
                }
                p = p->next;
            }
            
            p->next = l1 ? l1 : l2; //直接挂在链表
            return head->next;
        }
    };
    ```

    

- 方法5：分治+归并

     C++ https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/liang-liang-he-bing-by-dongzhong/ 

      ![](assets/23.png)

     ```CPP
     class Solution {
     public:
         ListNode* mergeKLists(vector<ListNode*>& lists) {
             if(lists.size() == 0) return nullptr;
             else{
                 //采用两两归并的方法比逐个合并要快很多
                 int interval = 1;
                 while(interval < lists.size()){
                     //优化:将此处改为两两合并,分治思路
                     for(int i = 0;i+interval < lists.size();i += 2*interval){
                         lists[i] = merge(lists[i],lists[i+interval]);
                     }
                     interval *= 2;
                 }
                 return lists[0];
             }
         }
     private:
         //迭代法实现上比递归法效率要高一些
         ListNode *merge(ListNode *left,ListNode *right){
             auto dummy = ListNode(-1);
             auto cur = &dummy;                     
             while(left && right){
                 if(left->val < right->val){
                     cur->next = left;
                     cur = left;
                     left = left->next;
                 }
                 else{
                     cur->next = right;
                     cur = right;
                     right = right->next;
                 }
             }
             if(left)
                 cur->next = left;
             if(right)
                 cur->next = right;
             return dummy.next;
         }
     };
     ```

     补充：

     下面的方法也是基于分治+归并，但是利用了队列，需要额外的空间，因此不推荐，但是队列的使用是值得参考学习的，比第一种方法的for循环更容易。

     ```cpp
     //https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/23-he-bing-kge-pai-xu-lian-biao-yi-bu-bu-you-hua-d/
     
     //基于merge思想，将k个链表放入队列，取出两个进行合并，合并后在放入队尾，直到只有队列中只有一个元素。
     //注意：vector转队列需要先转双端队列。
     
     //击败99.05%
     class Solution {
     public:
         ListNode* mergeKLists(vector<ListNode*>& lists) {
             int size = lists.size();
             if (size == 0) {
                 return nullptr;
             }
             if (size == 1) {
                 return lists[0];
             }
             queue<ListNode*> waiting(deque<ListNode*>(lists.begin(), lists.end())); //将vector转为队列
             //如果队列元素大于1，则取出两个进行合并，合并后的链表继续添加到链表尾部
             while (waiting.size() > 1) {
                 ListNode *l1 = waiting.front();
                 waiting.pop();
                 ListNode *l2 = waiting.front();
                 waiting.pop();
                 ListNode *p = merge2(l1, l2);//可以取消生成变量
                 waiting.push(p);
             } 
             return waiting.front();
         }
         ListNode* merge2(ListNode *l1, ListNode *l2) {
             ListNode *head = new ListNode(0);
             ListNode *p = head;
             while (l1 && l2) {
                 if (l1->val < l2->val) {
                     p->next = l1;
                     l1 = l1->next;
                 } else {
                     p->next = l2;
                     l2 = l2->next;
                 }
                 p = p->next;
             }
             p->next = l1 ? l1 : l2;
             return head->next;
         }
     };
     
     
     //version2：减少生成中间变量。
     //击败100%
         
     class Solution {
     public:
         ListNode* mergeKLists(vector<ListNode*>& lists) {
             int size = lists.size();
             if (size == 0) {
                 return nullptr;
             }
             if (size == 1) {
                 return lists[0];
             }
             queue<ListNode*> waiting(deque<ListNode*>(lists.begin(), lists.end())); //将vector转为队列
             //如果队列元素大于1，则取出两个进行合并，合并后的链表继续添加到链表尾部
             while (waiting.size() > 1) {
                 ListNode *l1 = waiting.front();
                 waiting.pop();
                 ListNode *l2 = waiting.front();
                 waiting.pop();
                 waiting.push(merge2(l1, l2));
             } 
             return waiting.front();
         }
         ListNode* merge2(ListNode *l1, ListNode *l2) {
     	//TO DO
     };
     ```

     

     

       

### 24. 两两交换链表中的节点

#### **题目描述：**

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

**不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

#### **示例：**

```
输入: 1->2->3->4

输出：2->1->4->3.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：递归

    ```cpp
    //执行用时 :8 ms, 在所有 cpp 提交中击败了52.31%的用户
    //内存消耗 :8.6 MB, 在所有 cpp 提交中击败了76.44%的用户
    
    class Solution{
    public:
        ListNode* swapPairs(ListNode* head){
            //if(head || head->next)   //报错
                //return head;
            
            if(head == nullptr || head->next == nullptr)  //使用nullptr
                return head;
            
            ListNode* first = head;			  //第一个节点
            ListNode* second = head->next;	  //第二个节点
            
            ListNode* next = second->next;	  //保存记录第二个节点后的节点引用
            second->next = first;			  //翻转，第二个节点指向第一个节点
            
            first->next = swapPairs(next);
            
            return second;
        }
    };
    ```

- 方法2：非递归循环

    ```cpp
    //执行用时 :8 ms, 在所有 cpp 提交中击败了52.31%的用户
    //内存消耗 :8.6 MB, 在所有 cpp 提交中击败了76.44%的用户
    class Solution{
    public:
        ListNode* swapPairs(ListNode* head){
            if(head == nullptr || head->next == nullptr)
                return head;
            
            ListNode* dummy = new ListNode(-1);
            dummy->next = head;
            
            ListNode* tmp = dummy;
            while(tmp->next != nullptr && tmp->next->next != nullptr){
                ListNode* first = tmp->next;		//频繁创建对象，导致运行时间和递归没区别
                ListNode* second = tmp->next->next;	//频繁创建对象，导致运行时间和递归没区别
                tmp->next = second;
                
                first->next = second->next;
                second->next = first;
                
                tmp = first;
            }
            
            //return dummy->next;
            ListNode* newHead = dummy->next;
            delete dummy;
            return newHead;
        }
    };
    
    
    //执行用时 :4 ms, 在所有 cpp 提交中击败了89.52%的用户
    //内存消耗 :8.6 MB, 在所有 cpp 提交中击败了76.44%的用户
    class Solution {
    public:
        ListNode* swapPairs(ListNode* head) {
            if(head == nullptr || head->next == nullptr)
                return head;
            
            ListNode* dummy = new ListNode(-1);
            dummy->next = head;
            
            ListNode* tmp = dummy;
            ListNode* first = tmp->next;			//避免频繁创建对象，节省运行时间
            ListNode* second = tmp->next->next;		//避免频繁创建对象，节省运行时间
            while(tmp->next != nullptr && tmp->next->next != nullptr){
                first = tmp->next;
                second = tmp->next->next;
                tmp->next = second;
                
                first->next = second->next;
                second->next = first;
                
                tmp = first;
            }
            
            //return dummy->next;
            ListNode* newHead = dummy->next;
            delete dummy;
            return newHead;
        }
    };
    ```

### 25. k个一组翻转链表

#### **题目描述：**

给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。

k 是一个正整数，它的值小于或等于链表的长度。

如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

#### **示例：**

```
给定这个链表：1->2->3->4->5

当 k = 2 时，应当返回: 2->1->4->3->5

当 k = 3 时，应当返回: 3->2->1->4->5
```

**说明：**

- 算法只能使用常数的额外空间。
- **不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

#### 题解

不需要设置dummy节点

- 方法1：遍历+栈

    满足使用常数的额外空间O(K)的要求

    ```cpp
    //执行用时 :20 ms, 在所有 cpp 提交中击败了98.14%的用户
    //内存消耗 :10 MB, 在所有 cpp 提交中击败了64.81%的用户
    class Solution {
    public:
        ListNode* reverseKGroup(ListNode* head, int k) {
            stack<ListNode*> sta;
    
            ListNode* dummy = new ListNode(-1);
            ListNode* prev = dummy;
    
            int count = 0;
            ListNode* tmp = head;
            ListNode* unMatch = head; //用于保存剩余不足K个节点的头节点
            while (true) {
                //每k个节点入栈
                while (tmp != nullptr && count < k) {
                    sta.push(tmp);
                    tmp = tmp->next;
                    count++;
                }
    
                //剩余不足K个节点
                if (count != k) {
                    prev->next = unMatch;
                    break;
                }
    
                while (!sta.empty()) {
                    prev->next = sta.top();
                    prev = prev->next;
                    sta.pop();
                }
                
                count = 0;		//重新计数
                unMatch = tmp;	//更新未匹配部分头节点
            }
    
            //prev->next->next = nullptr;
            return dummy->next;
        }
    };
    ```
    
- 方法2：遍历+头插法

    ```cpp
    class Solution {
    public:
        ListNode* reverseKGroup(ListNode* head, int k) {
            ListNode* dummy = new ListNode(-1);
            ListNode* pre = dummy;
            ListNode* cur = pre;
            
            dummy->next = head;
            int num = 0;
            while (cur = cur->next) 
                ++num;//链表长度
            
            while (num >= k) {
                cur = pre->next;
                
                //依次交换节点元素
                for (int i = 1; i < k; ++i) {
                    ListNode* tmp = cur->next;
                    cur->next = tmp->next;
                    tmp->next = pre->next;
                    pre->next = tmp;
                }
                pre = cur;
                num -= k;
            }
            return dummy->next;
        }
    };
    ```

    

- 方法3：递归

    ```cpp
    class Solution{
    public:
        ListNode* reverseKGroup(ListNode* head, int k){
    		if(head == nullptr || k<=1)
                return head;
            
            ListNode* tail = head;
            for(int i=0; i<k; i++){
                if(tail != nullptr)  //不足k个节点，直接返回头节点
                    tail = tail->next;	
                else
                    return head;            
            }
            
            ListNode* newHead = reverseList(head, tail);//也可用迭代实现
            
            head->next = reverseKGroup(tail, k);
            
            return newHead;
        }
        
        ListNode* reverseList(ListNode* head, ListNode* tail){
            if(head == nullptr || head->next == nullptr)
                return head;
            
            ListNode* next=nullptr;
            ListNode* prev=nullptr;
            while(head != tail){
                next = head->next;
            head->next = prev;
                prev = head;
            head = next;
            }
            return prev;
        }
    };
    ```
    
    写法2：
    
    ```cpp
    class Solution {
    public:
        ListNode* reverseKGroup(ListNode* head, int k) {
    
            //to do 
        }
    };
    
    
    
    链接：https://www.nowcoder.com/questionTerminal/a632ec91a4524773b8af8694a51109e7?orderByHotValue=1&page=1&onlyReference=false
    来源：牛客网
    
        ListNode* right = pHead;
        ListNode* left = pHead;
        ListNode* prev = pHead;
        for(int i=0;i<k;i++)
        {
            if(right!=nullptr)
                right = right->next;
            else
                return left;
        }
        ListNode* head = left;
        while(left!=right)
        {
            ListNode* pNext = left->next;
            left ->next = prev;
            prev = left;
            left = pNext;
        }
        head->next = reverseList(right,k);
        return prev;
    ```
    
    
    
    补充：C++版完整实现
    
    ```cpp
    //链接：https://www.nowcoder.com/questionTerminal/a632ec91a4524773b8af8694a51109e7?orderByHotValue=1&page=1&onlyReference=false
    //来源：牛客网
    
    #include "iostream"
    #include "vector"
    using namespace std;
     
    struct ListNode
    {
        int val;
        ListNode* next;
        ListNode(int x):val(x),next(nullptr){}
    };
     
    ListNode* creatList(vector<int> myVec)
    {
        ListNode* pHead=new ListNode(myVec[0]);
        ListNode* prev = pHead;
        for(int i=1;i<myVec.size()-1;i++)
        {
            pHead->next = new ListNode(myVec[i]);
        pHead=pHead->next;
        }
        return prev;
    }
     
    ListNode* reverseList(ListNode* pHead,int k)
    {
    	//to do 
    }
     
    int main()
    {
        vector<int> myVec;
        int temp=0;
        while(cin>>temp)
            myVec.push_back(temp);
        int k=myVec[myVec.size()-1];
        ListNode* pHead = creatList(myVec);
        pHead = reverseList(pHead, k);
        while(pHead!=nullptr)
        {
            cout<<pHead->val<<" ";
            pHead=pHead->next;
        }
             
        return 0;
    }
    ```
    
    

### 61. 旋转链表

#### **题目描述：**

 给定一个链表，旋转链表，将链表每个节点向右移动 *k* 个位置，其中 *k* 是非负数。 

#### **示例：**

```
示例1：
输入: 1->2->3->4->5->NULL, k = 2
输出: 4->5->1->2->3->NULL
解释:
向右旋转 1 步: 5->1->2->3->4->NULL
向右旋转 2 步: 4->5->1->2->3->NULL

示例2：
输入: 0->1->2->NULL, k = 4
输出: 2->0->1->NULL
解释:
向右旋转 1 步: 2->0->1->NULL
向右旋转 2 步: 1->2->0->NULL
向右旋转 3 步: 0->1->2->NULL
向右旋转 4 步: 2->0->1->NULL
```

#### 题解

注意K可能大于链表长度

- 方法1：遍历

    1.  求出链表的长度len ；
    2.  右移的距离  = K % len ；
    3. 使用双指针，找到倒数第k-1个节点、最后一个节点；
    4. 断开&连接

    ```cpp
    class Solution{
    public:
        ListNode* rotateRight(ListNode* head, int k){
            if(head == nullptr || k<1)
                return head;
            
            int len = 0;
            ListNode* tmp = head;
            while(tmp != nullptr){
                len++;
                tmp = tmp->next;
            }
                
            
            ListNode* left = head;
            ListNode* right = head;
            
            k = k%len;
            if(k == 0)			//以len为周期
                return head;
    
    		while(k>0){
                k--;
                right = right->next;
            }
            
            while(right->next != nullptr){
                left = left->next;
                right =  right->next;
            }
            
            ListNode* newHead = left->next;
            right->next = head;
            left->next = nullptr;
            
            return newHead;
        }
    };
    ```

### 82. 删除排序链表中的重复元素II

#### **题目描述：**

  给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 *没有重复出现* 的数字。 

#### **示例：**

```
输入: 1->2->3->3->4->4->5
输出: 1->2->5

输入: 1->1->1->2->3
输出: 2->3
```

#### 题解

- 方法1：==遍历==

    ```cpp
    class Solution{
    public:
    	ListNode* deleteDuplicates(ListNode* head){
            if(head == nullptr || head->next == nullptr)
                return head;
            
            ListNode* dummy = new ListNode(-1);
            dummy->next = head;
            
            ListNode* pre = dummy;
            ListNode* cur = head;
            while(cur != nullptr && cur->next != nullptr){
                if(cur->val == cur->next->val){
                    while(cur->next != nullptr && (cur->val == cur->next->val))
                        cur = cur->next;
                    
                    pre->next = cur->next; //跳过重复的部分
                    cur = cur->next;       //继续向下遍历
                }
                else{
                    pre = cur;
                    cur = cur->next;
                }
            }
            
            return dummy->next;
        }
    };
    ```

- 方法2：==递归==

    ```cpp
    class Solution{
    public:
        ListNode* deleteDuplicates(ListNode* head){
            if(head == nullptr || head->next == nullptr)
                return head;
            
            ListNode* next = head->next;
            if(head->val == next->val){
                while(next != nullptr && (head->val == next->val))
                    next = next->next;
                head = deleteDuplicates(next);
            }
            else{
                head->next = deleteDuplicates(next);
            }
            
            return head;
        }
    };
    ```

- 方法3：哈希表

    两次遍历：一次遍历用哈希表统计次数，第二次遍历出现次数为1的接在新链表后面。

    该方法适合有序链表和无序链表，而且是==唯一适合无序链表的方法==。

    ```cpp
    class Solution{
    public:
        ListNode* deleteDuplicates(ListNode* head){
            if(head == nullptr || head->next == nullptr)
                return head;
            
            unordered_map<int, int> hashmap;
            ListNode* temp=head;
            while(temp != nullptr){
                hashmap[temp->val]++;
                temp = temp->next;
            }
            
            temp = head;
            ListNode* dummy = new ListNode(-1);
            ListNode* node = dummy;
            while(temp != nullptr){
                if(hashmap[temp->val] == 1){
                    node->next = new ListNode(temp->val);
                    node = node->next;
                }
                temp = temp->next;
            }
            
            return dummy->next;
        }
    };
    ```

    

- 方法4：栈+头插法

    仅仅适用于有序链表

### 83. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 86. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 92. 反转链表II

#### **题目描述：**

 反转从位置 *m* 到 *n* 的链表。请使用一趟扫描完成反转。  

#### **示例：**

```
输入: 1->2->3->4->5->NULL, m = 2, n = 4
输出: 1->4->3->2->5->NULL
```

**说明：**

 1 ≤ *m* ≤ *n* ≤ 链表长度。 

#### 题解

- 方法1：利用栈

    如果题目允许修改节点值，可以利用一个栈保存[m, n]之间的每个节点值。

    ```cpp
    class Solution {
    public:
        ListNode* reverseBetween(ListNode* head, int m, int n) {
            if(m == n || head == nullptr)
                return head;
            
            stack<int> sta;
            int count = 0;
            ListNode* tmp=head;
            
            while(tmp != nullptr){
                count++;
                if(m<= count && count <= n)
                	sta.push(tmp->val);
                tmp = tmp->next;
            }
            
            count = 0;
            tmp = head;
            while(tmp != nullptr){
                count++;
                if(m <= count && count <= n){
                    tmp->val = sta.top();
                    sta.pop();
                }
                tmp = tmp->next;    
            }
            
            return head;
        }
    };
    ```

- 方法2：遍历（头插法）

    把第二个for循环的过程画出来就能够理解了：m = 3,  n=5

    -1 —> 1—> 2—> 3 —> 4 —> 5 —> 6 —> null

    ```cpp
    class Solution{
    public:
        ListNode* reverseBetween(ListNode* head, int m, int n){
           if(m == n || head == nullptr)
               return head;
            
            ListNode* dummy = new ListNode(-1);
            dummy->next = head;
            
            ListNode* pre = dummy;
            ListNode* cur = dummy->next;
            
    		for(int i=1; i<m; i++){
                pre = pre->next;//指向第m-1个节点，不再移动
                cur = cur->next;//指向第m个节点，此后随第m个节点反转
            }
            
            //pre指向第m-1个节点，不再移动
            ////指向第m个节点，此后随第m个节点反转
            ListNode* tmp = nullptr;
            for(int i=0; i<n-m; i++){
                tmp = cur->next;
                cur->next = tmp->next;
                tmp->next = pre->next;
                pre->next = tmp;
            }
            
            return dummy->next;
        }
    };
    ```

    

### 109. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 138. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 141. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 142. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 143. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 147. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 148. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 160. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 203. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 206. 反转链表

#### **题目描述：**

 反转一个单链表。 

#### **示例：**

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

**进阶：**

 你可以迭代或递归地反转链表。能否用两种方法解决这道题？ 

#### 题解

- 方法1：遍历

    [非递归过程（迭代）图解](https://blog.csdn.net/fx677588/article/details/72357389)

    ```cpp
    class Solution{
    public:
        ListNode* reverseList(ListNode* head){
            if(head == nullptr || head->next == nullptr)
                return head;
            
            ListNode* prev = nullptr;
            ListNode* next = nullptr;
            
            while(head != nullptr){
                next = head->next;
                head->next = prev;
                prev = head;
                head = next;
            }
            
            return prev;
        }
    };
    ```

    

- 方法2：递归

    [递归过程图解](https://blog.csdn.net/fx677588/article/details/72357389)

    ```cpp
    class Solution {
    public:
        ListNode* reverseList(ListNode* head) {
            if(head == nullptr || head->next == nullptr) //空链表 || 递归边界
                return head;
            
            ListNode* newHead = reverseList(head->next);
            
            head->next->next = head;
            head->next = nullptr;
            
            return newHead;
        }
    };
    ```

- 方法3：栈

    ```cpp
    class Solution{
    public:
        ListNode* reverseList(ListNode* head){
            if(head == nullptr || head->next == nullptr)
                return head;
            
            stack<ListNode*> sta;
            ListNode* dummy = new ListNode(-1);        
            
            while(head != nullptr){
                sta.push(head);
                head = head->next;
            }
            
            ListNode* tmp=dummy;
            
            while(!sta.empty()){
                tmp->next = sta.top();
                sta.pop();
                tmp = tmp->next;   
            }
            
            tmp->next = nullptr; //非常重要
            return dummy->next;  //还需要考虑删除dummy节点
        }
    };
    ```

    

### 234. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 237. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 328. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 369. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 379. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 426. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 430. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 445. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 707. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 708. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 725. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 817. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 876. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 1019. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）

### 1171. 删除倒数第k个节点

#### **题目描述：**

 给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。 

#### **示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 k 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

#### 题解

- 方法1：两次遍历

    第一次遍历计算出链表节点数，第二次遍历找到第k个节点

- 方法2：一次遍历（双指针）



## 字符串

## 二叉树

## 图

##  矩阵最短路径

 [1091. 二进制矩阵中的最短路径](https://leetcode-cn.com/problems/shortest-path-in-binary-matrix/solution/1091-er-jin-zhi-ju-zhen-zhong-de-zui-duan-lu-jing-/) 
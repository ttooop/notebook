## 链表
### 61-Rotate List
**Example 1:**
**Input:** 1->2->3->4->5->NULL, k = 2
**Output:** 4->5->1->2->3->NULL
**Explanation:**
rotate 1 steps to the right: 5->1->2->3->4->NULL
rotate 2 steps to the right: 4->5->1->2->3->NULL
#### 解题
第一种方法是使用快慢指针，快指针先走k步，然后两个指针一起走，当快指针走到末尾时，慢指针的下一个位置就是新的顺序的头结点，这样就可以旋转链表了。
```
class Solution{
public:
	ListNode *rotateRight(ListNode *head,int k){
		if(!head) return NULL;
		int n=0;
		ListNode *cur=head;
		while(cur){
			++n;
			cur=cur->next;
		}
		k%=n;
		ListNode *fast=head,*slow=head;
		for(int i=0;i<k;++i){
			fast=fast->next;
		}
		if(!fast)
			return head;
		while(fast->next){
			fast=fast->next;
			slow=slow->next;
		}
		fast->next=head;
		fast=slow->next;
		slow->next=NULL;
		return fast;
	}
};
```
第二种方法只是用一个指针，先遍历链表长度，再将链表头和链表尾连起来，再往后走n-k%n步即到达新链表的头结点的前一个点，然后断开链表即可。
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        if(!head)
            return NULL;
        int n=1;
        ListNode *cur=head;
        while(cur->next){
            n++;
            cur=cur->next;
        }
        cur->next=head;
        int m=n-k%n;
        for(int i=0;i<m;i++){
            cur=cur->next;
        }
        ListNode *newhead=cur->next;
        cur->next=NULL;
        return newhead;
    }
};
```
### 83-Remove Duplicates from Sorted List
删除链表中重复数字，保证每个数字只出现一次
**Example 2:**
**Input:** 1->1->2->3->3
**Output:** 1->2->3
解：
不用创建新链表，直接通过一次遍历，进行链表next的转换即可
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode *cur=head;
        while(cur){
            while(cur->next&&cur->next->val==cur->val){
                cur->next=cur->next->next;
            }
            cur=cur->next;
        }
        return head;
    }
};
```
### 83-Remove Duplicates from Sorted List II
将所有重复的数字都删除掉，只留下不重复的数字。
**Example 1:**
**Input:** 1->2->3->3->4->4->5
**Output:** 1->2->5
解：
需要通过创建新的链表来改变pre->next的指向，即直接跳过重复数字指向后面的数字。
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if(!head||!head->next)
            return head;
        ListNode *dummy=new ListNode(-1),*pre=dummy;
        dummy->next=head;
        while(pre->next){
            ListNode *cur=pre->next;
            while(cur->next&&cur->val==cur->next->val){
                cur=cur->next;
            }
            if(cur!=pre->next)
                pre->next=cur->next;
            else
                pre=pre->next;
        }
        return dummy->next;
    }
};
```
### 86-Partition List
将链表中小于x的移到前面，其余的按顺序不变在后面。
**Example:**
**Input:** head = 1->4->3->2->5->2, _x_ = 3
**Output:** 1->2->2->4->3->5
解：
通过创建两个新的链表，一个newdummy通过p指针实现小于x的数的先链接，剩下的按dummy中的顺序更改指向，最后将p和dummy连起来即可。
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* partition(ListNode* head, int x) {
        if(!head)
            return NULL;
        ListNode *dummy=new ListNode(-1),*cur=dummy;
        dummy->next=head;
        ListNode *newdummy=new ListNode(-1),*p=newdummy;
        while(cur->next){
            if(cur->next->val<x){
                p->next=cur->next;
                p=p->next;
                cur->next=cur->next->next;
                p->next=NULL;
            }else{
                cur=cur->next;
            }
        }
        p->next=dummy->next;
        return newdummy->next;
    }
};
```
### 92-Reverse Linked List Ⅱ
将链表m到n之间的数字翻转
**Note:** 1 ≤  _m_  ≤  _n_  ≤ length of list.
**Example:**
**Input:** 1->2->3->4->5->NULL, _m_ = 2, _n_ = 4
**Output:** 1->4->3->2->5->NULL
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        ListNode* dummy=new ListNode(-1),*pre=dummy;
        dummy->next=head;
        for(int i=0;i<m-1;++i)
            pre=pre->next;
        ListNode *cur=pre->next;
        for(int i=m;i<n;++i){
            ListNode *t=cur->next;
            cur->next=t->next;
            t->next=pre->next;
            pre->next=t;
        }
        return dummy->next;
    }
};
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTkzNTA4NDYwMCwtNjAyODE4NjIwXX0=
-->
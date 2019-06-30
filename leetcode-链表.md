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
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDM4NjI3MjQ1XX0=
-->
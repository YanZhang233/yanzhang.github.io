---
layout: post
title:  Reverse LinkedList
date:   2018-05-01 09:30
description: Iteration and Recursion on Reversing LinkedList. # Add post description (optional)
img: reverselinkedlist.jpg # Add image post (optional)
tags: [Algorithm, Leetcode, LinkedList]
author: Yan Zhang # Add name author (optional)
---
## Reverse LinkedList

The first problem([Leetcode 206](https://leetcode.com/problems/reverse-linked-list/description/)) is to reverse a singly linked list. We can do it either iteratively or recursively.

* Iteration

Iteration is pretty intuitive and simple, which means we just iterate the whole list, and make the next pointer of each node point to its previous node, repeating the process one by one. **This would take O(n) time complexity as iterating the whole n nodes, and O(1) space complexity.**

```java
//Iteration
public ListNode reverseList(ListNode head) {
      ListNode pre = null;
      ListNode cur = head;
      ListNode next;
      while(cur != null) {
          next = cur.next;
          cur.next = pre;
          pre = cur;
          cur = next;
      }
      return pre;
}
```

* Recursion

The recursive way is a little tricky. The key concept is, we always assume the whole list after the current node we are processing has already been reversed. So all we need to consider is just reversing the next pointer of the current node back to its previous node. It actually works backwords. **The time complexity is still O(n), but the space complexity is O(n) since there is an implicit stack, which could go up to n levels deep.**

```java
//Recursion
public ListNode reverseList(ListNode head) {
      if(head == null||head.next == null) {
          return head;
      }
      ListNode pre = reverseList(head.next);
      head.next.next = head;
      head.next = null;
      return pre;
}
```

In addition, the ListNode pre here is to save the last node of the original list, which can be returned as the head of the new reversed list later.

## Reverse LinkedList II

The second problem([Leetcode 92](https://leetcode.com/problems/reverse-linked-list-ii/description/)) is to reverse a part of a linked list, instead of the whole list. At first glance, I was also thinking about using the two methods: iteration and recursion naturally. By iteration, the problem is after we reversing the required part, we still have to spend some time to connect the head, middle and tail part. That is not efficient. By recursion, we can start reversing from the middle node, then expanding the reversion until covering the left-th and right-th nodes. We solve the problem by progressing (l, r) into (l + 1, r - 1)...until l == r, where we can directly return the single node. But the same problem of taking time to connect appears again, we cannot do this in one-pass.

So, what should I do...? I checked the answer. 😂

The solution is inserting the nodes of the required reversing part, into the start position of the reversing part one by one. I know that is not neat. It is also kind of like, iteration.

*For example, the original list is 1->2->3->4->5->6. (we want to reverse 2->3->4->5)*

*1-st inserting 2: 1-> **2->3->4->5** ->6*

*2-nd inserting 3: 1-> **3->2->4->5** ->6*

*3-rd inserting 4: 1-> **4->3->2->5** ->6*

*4-th inserting 5: 1-> **5->4->3->2** ->6*

*Done!*

**The time complexity is up to O(n) as we may processing the whole n nodes. The space complexity is just O(1).**

```java
public ListNode reverseBetween(ListNode head, int m, int n) {
      if(head == null || head.next == null || m == n) {
          return head;
      }
      ListNode dummy = new ListNode(0);
      dummy.next = head;
      ListNode pre = dummy;
      for(int i = 0; i < m - 1; i++) {
          pre = pre.next;
      }
      ListNode start = pre.next;
      ListNode then = start.next;
      for(int i = 0; i < n - m; i++) {
          start.next = then.next;
          then.next = pre.next;
          pre.next = then;
          then = start.next;
      }
      return dummy.next;
}
```

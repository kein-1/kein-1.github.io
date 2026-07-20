---
title: "21. Merge Two Sorted Lists"
date: 2026-07-20
---

Two-pointer merge — the foundational building block. Whenever merging/linked list problems, I like to use a dummy node so I don't have issues with edge cases



```swift
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     public var val: Int
 *     public var next: ListNode?
 *     public init() { self.val = 0; self.next = nil; }
 *     public init(_ val: Int) { self.val = val; self.next = nil; }
 *     public init(_ val: Int, _ next: ListNode?) { self.val = val; self.next = next; }
 * }
 */
class Solution {
    func mergeTwoLists(_ list1: ListNode?, _ list2: ListNode?) -> ListNode? {
        // we just need two pointers: one in each list, and then a third one to build out the new list
        // when either pointer is nil, just append the other pointer to the end

        if list1 == nil { return list2 }
        if list2 == nil { return list1 }

        var dummy = ListNode()
        var curr = dummy
        var ptr1 = list1, ptr2 = list2

        while ptr1 != nil && ptr2 != nil {
           if let v1 = ptr1?.val, let v2 = ptr2?.val {
                if v1 < v2 {
                    curr.next = ptr1
                    ptr1 = ptr1?.next
                } else {
                    curr.next = ptr2
                    ptr2 = ptr2?.next
                }
                curr = curr.next!
           }
        }

        if ptr1 == nil {
            curr.next = ptr2
        }
        if ptr2 == nil {
            curr.next = ptr1
        }

        return dummy.next
    }
}
```

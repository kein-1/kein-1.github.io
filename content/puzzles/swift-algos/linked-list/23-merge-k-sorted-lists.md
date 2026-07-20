---
title: "23. Merge k Sorted Lists"
date: 2026-07-20
---

Interesting problem that can teach you a lot about divide and conquer. Solutions online often use a heap but I found solving this with divide and conquer to be easier. 

Useful to solve [21. Merge Two Sorted Lists](/puzzles/swift-algos/linked-list/21-merge-two-sorted-lists/) and [148. Sort List](/puzzles/swift-algos/linked-list/148-sort-list/) before solving this. 

In this problem, the technique is a combination of those two: merge sort on linked lists which involves combining two sorted lists. 

In Sort Lists, we constantly divide the list until we are a list of 1 node; then recursively apply Merge Sort by combining two sorted lists.

Here, we do the same, except we already have *k* sorted lists to start with so this is even easier. All we have to do is split the k sort lists until we have a list of 1 left, and we know this when start == end index. Afterwards the problem is the same as merging two sorted lists


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
    func mergeKLists(_ lists: [ListNode?]) -> ListNode? {

        if lists.count == 0 { return nil }

        return helper(lists, 0, lists.count - 1)
    }

    func helper(_ lists: [ListNode?], _ start: Int, _ end: Int) -> ListNode? {

        if start >= end {
            return lists[start]
        }

        let middle = start + (end - start) / 2

        let left = helper(lists, start, middle)
        let right = helper(lists, middle + 1, end)

        return merge(left, right)
    }

    func merge(_ node1: ListNode?, _ node2: ListNode?) -> ListNode? {

        var dummy = ListNode()
        var curr: ListNode? = dummy

        var ptr1 = node1, ptr2 = node2

        while ptr1 != nil && ptr2 != nil {
            let v1 = ptr1!.val, v2 = ptr2!.val
            if v1 < v2 {
                curr?.next = ptr1
                ptr1 = ptr1?.next
            } else {
                curr?.next = ptr2
                ptr2 = ptr2?.next
            }
            curr = curr?.next
        }

        if ptr1 != nil {
            curr?.next = ptr1
        }

        if ptr2 != nil {
            curr?.next = ptr2
        }

        return dummy.next
    }
}
```

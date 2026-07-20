---
title: "148. Sort List"
date: 2026-07-20
---

Whenever sorting is required, I like to use Merge Sort. 

Merge sort on Linked Lists is the same as arrays: split the array in half, recursively sort each half, then merge the two sorted halves. To get middle, we use fast and slow pointer; then make sure to use a prev pointer, which is one trailing the slow (the midpoint which is also the new head of the second half) 'disconnect' so we get the halves properly and we don't end up with cycles

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
    func sortList(_ head: ListNode?) -> ListNode? {
        // merge sort: at each iteration, find the middle, split the list into two, and then apply merge sort
        // then merge the two lists
        return helper(head)
    }

    func helper(_ head: ListNode?) -> ListNode? {

        // last node
        if head == nil || head?.next == nil {
            return head
        }

        // get middle
        var slow = head, fast = head
        var prev = slow
        while fast != nil && fast?.next != nil {
            prev = slow
            slow = slow?.next
            fast = fast?.next?.next
        }
        prev?.next = nil

        let left = helper(head)
        let right = helper(slow)

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

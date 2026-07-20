---
title: "56. Merge Intervals"
date: 2026-07-20
---

Sort by start; then keep a prev and compare with curr: if there is an overlap, we want to merge these two intervals and set it as the new prev so we can compare with future intervals. If no overlap, push this prev to the answer array and then curr becomes the new prev 


We sort by start, rather than end, because there is a chance that there could be overlapping intervals later on. 

Take `[1,3], [4,5], [1,10]` : when we iterate we will see that there are no issues with `[1,3], [4,5]`; then when we get to `[1,10]`,  there is an overlap with the earlier `[1,3]`; if we didn't sort by start first, we would have already pushed `[1,3]` to the answer array, and we would miss the fact that `[1,10]` overlaps with `[1,3]` (and that they should be merged)




```swift
class Solution {
    func merge(_ intervals: [[Int]]) -> [[Int]] {

        var intervals = intervals.sorted { $0[0] < $1[0] }
        var ans = [[Int]]()
        var prev = intervals[0]

        for i in 1..<intervals.count {
            if isOverlap(prev, intervals[i]) {
                let (s1, e1) = (prev[0], prev[1])
                let (s2, e2) = (intervals[i][0], intervals[i][1])
                prev = [min(s1, s2), max(e1, e2)]
            } else {
                ans.append(prev)
                prev = intervals[i]
            }
        }
        ans.append(prev)
        return ans
    }

    func isOverlap(_ intervalOne: [Int], _ intervalTwo: [Int]) -> Bool {
        let (s1, e1) = (intervalOne[0], intervalOne[1])
        let (s2, e2) = (intervalTwo[0], intervalTwo[1])

        if e1 >= s2 { return true }
        return false
    }
}
```

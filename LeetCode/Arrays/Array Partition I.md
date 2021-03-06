# Array Partition I

Link to the problem: https://leetcode.com/problems/array-partition-i/

## Description
Given an integer array nums of 2n integers, group these integers into n pairs (a1, b1), (a2, b2), ..., (an, bn) such that the sum of min(ai, bi) for all i is maximized. Return the maximized sum.

Example 1:

Input: nums = [1,4,3,2]
Output: 4
Explanation: All possible pairings (ignoring the ordering of elements) are:
1. (1, 4), (2, 3) -> min(1, 4) + min(2, 3) = 1 + 2 = 3
2. (1, 3), (2, 4) -> min(1, 3) + min(2, 4) = 1 + 2 = 3
3. (1, 2), (3, 4) -> min(1, 2) + min(3, 4) = 1 + 3 = 4
So the maximum possible sum is 4.

## Approach
So, the problem involves finding max and min values, I think it is helpful to think about how a sorted array would help.

To begin, if our input array is [1, 2, 4, 3] and the answer is 4 as a result of `min(1, 2)` and `min(3,4)`, we can see that it is impossible to have the largest number in the array to be part of our solution since we are using `min` function. So, it is evident that if we sort the array, we can just treat every two numbers as a pair, and sum the first one number of each pair.

I think the question prompt may be misleading as it enumerates all possible pairs of an array. My initial thought was natually to find a way to get all pairs but that would be very unnecessary. 

## Implementation

```java
public int arrayPairSum(int[] nums) {
    Arrays.sort(nums);
    int result = 0;
    for (int i = 0; i < nums.length-1; i += 2)
        result += nums[i];
    return result
}
```

## Complexity Analysis

Since we don't have any external data structures, our space complexity is constant. For runtime, it's linearithmic because of the sorting operation at the beginning. In Java, `Arrays.sort()` is implemented by dual-pivot quicksort, which runs in *n*log*n*. Then the for-loop runs in linear time. Asymptotically, this algorithm's runtime is proportional to *n*log*n*.
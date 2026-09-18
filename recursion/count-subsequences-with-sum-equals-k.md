# Count Subsequences With Sum Equals K

Given an array and a value `k`, count how many subsequences have a sum equal to `k`.

For example:

```text
Input:
arr = [1, 2, 3]
k = 3

Output:
2
```

Valid subsequences:

```text
[3]       → 3
[1, 2]    → 3
```

So the answer is `2`.

---

## Approach

Just like the previous subsequence problems, every element gives us two choices:

```text
1. Include the element
2. Exclude the element
```

The difference here is that we need to **count all valid subsequences**.

So we cannot stop when we find one valid subsequence. Both branches need to be explored.

```text
                    Element
                   /       \
              Include     Exclude
                 |            |
            sum + element      sum
                 |            |
              countA         countB

                    ↓

              countA + countB
```

---

## Java Solution

```java
class Solution {

    public int countSubsequencesWithSumEqualsK(
            int idx,
            int[] nums,
            int sum,
            int k) {

        // Base Case
        if (idx >= nums.length) {
            return sum == k ? 1 : 0;
        }

        // Include current element
        int countA = countSubsequencesWithSumEqualsK(
                idx + 1,
                nums,
                sum + nums[idx],
                k
        );

        // Exclude current element
        int countB = countSubsequencesWithSumEqualsK(
                idx + 1,
                nums,
                sum,
                k
        );

        // Total valid subsequences
        return countA + countB;
    }

    public int countSubsequenceWithTargetSum(int[] nums, int k) {
        return countSubsequencesWithSumEqualsK(
                0,
                nums,
                0,
                k
        );
    }
}
```

---

## How the Counting Works

Consider:

```text
nums = [1, 2, 3]
k = 3
```

For every element, recursion creates two branches.

At the end of each branch:

```java
return sum == k ? 1 : 0;
```

This means:

```text
sum == k
    ↓
   1    → valid subsequence

sum != k
    ↓
   0    → invalid subsequence
```

Then the parent call adds the results from both branches:

```java
return countA + countB;
```

So valid branches contribute `1`, while invalid branches contribute `0`.

For example:

```text
[1, 2] → 1
[3]    → 1
Others → 0

Total = 1 + 1 = 2
```

---

## Why Both Recursive Calls Are Required?

In the previous problem, we only needed to know:

> Does at least one valid subsequence exist?

So we could stop as soon as we found one.

Here, the question is:

> How many valid subsequences exist?

Therefore, finding one is not enough.

We need to explore:

```text
Include
+
Exclude
```

and add their counts.

This is the important difference between **checking** and **counting**.

---

## Recursion Pattern

This problem follows a simple pattern:

```text
                Current Element
                 /            \
             Include         Exclude
                |               |
          Recursive Call   Recursive Call
                |               |
             countA           countB
                 \             /
                  countA + countB
```

At the base case:

```text
Valid → 1
Invalid → 0
```

The `1` and `0` values are then propagated back through the recursion tree.

---

## Complexity

For `n` elements, every element has two choices:

```text
Include / Exclude
```

So there can be up to:

```text
2^n
```

recursive branches.

### Time Complexity

```text
O(2^n)
```

### Space Complexity

```text
O(n)
```

The maximum recursion depth is `n`.

---

## Key Takeaway

There are three closely related recursion patterns:

```text
Generate → Store valid subsequences

Check   → Return true when a valid one is found

Count   → Return 1/0 and add both branches
```

For counting problems, a useful way to think is:

```text
Valid   → 1
Invalid → 0

Answer = Include Count + Exclude Count
```

The **include/exclude recursion pattern** is one of the most useful patterns to recognize in subsequence, subset, combination, and many backtracking problems.

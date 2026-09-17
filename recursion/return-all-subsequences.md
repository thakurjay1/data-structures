# Return All Subsequences

Given an array, the goal is to return all possible subsequences (subsets).

For example:

```text
Input:  [1, 2, 3]

Output:
[]
[1]
[2]
[3]
[1, 2]
[1, 3]
[2, 3]
[1, 2, 3]
```

For every element, we have **two choices**:

1. Take the element
2. Don't take the element

This naturally leads to recursion.

---

## Approach

At every index:

```text
              element
             /       \
          Take       Don't Take
```

For `[1, 2, 3]`:

```text
                         []
                    /          \
                  [1]           []
                /    \        /    \
             [1,2]  [1]     [2]     []
              ...
```

We keep making these two choices until we reach the end of the array.

When `idx == nums.length`, the current `subRes` represents one complete subsequence, so we add it to the result.

---

## Java Solution

```java
class Solution {

    List<List<Integer>> res = new ArrayList<>();

    public List<List<Integer>> allSubsequences(
            int idx,
            int[] nums,
            List<Integer> subRes) {

        // Base Case
        if (idx >= nums.length) {
            res.add(new ArrayList<>(subRes));
            return res;
        }

        // Take the current element
        subRes.add(nums[idx]);

        res = allSubsequences(idx + 1, nums, subRes);

        // Backtrack
        subRes.remove(subRes.size() - 1);

        // Don't take the current element
        res = allSubsequences(idx + 1, nums, subRes);

        return res;
    }

    public List<List<Integer>> subsets(int[] nums) {
        List<Integer> subRes = new ArrayList<>();
        return allSubsequences(0, nums, subRes);
    }
}
```

---

## Important Part — Backtracking

This line is important:

```java
subRes.remove(subRes.size() - 1);
```

We first **take** the element:

```java
subRes.add(nums[idx]);
```

After exploring that choice, we remove it before exploring the second choice.

```text
Take
 ↓
Explore
 ↓
Remove / Backtrack
 ↓
Don't Take
```

This allows us to reuse the same `subRes` list for different branches.

---

## Why `new ArrayList<>(subRes)`?

At the base case:

```java
res.add(new ArrayList<>(subRes));
```

We need to add a **copy** of the current list.

If we simply did:

```java
res.add(subRes);
```

all entries in `res` would point to the same list, which keeps changing during backtracking.

Creating a new `ArrayList` stores the current state separately.

---

## Recursion Pattern

The important pattern here is:

```text
For every element:

        Take
         |
    Recursive Call
         |
     Backtrack
         |
      Don't Take
         |
    Recursive Call
```

This **take / don't-take pattern** is very common in recursion and later appears in many backtracking and dynamic programming problems.

---

## Complexity

For `n` elements, every element has 2 choices:

```text
2 × 2 × 2 × ... × 2 = 2^n
```

So there are `2^n` subsequences.

### Time Complexity

```text
O(n × 2^n)
```

We have `2^n` subsequences and copying a subsequence can take up to `O(n)`.

### Space Complexity

```text
O(n)
```

for the recursion stack and the current `subRes`, excluding the output.

The output itself requires:

```text
O(n × 2^n)
```

space.

---

## Key Takeaway

Whenever a problem asks us to generate **all possible choices**, try thinking:

```text
                    Choice
                   /      \
                Take     Don't Take
```

Then use recursion to explore both branches and backtrack when necessary.

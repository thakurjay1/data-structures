# Combination Sum

Given an array of candidates and a target, find all combinations where the selected numbers add up to the target.

Each candidate can be used **any number of times**.

For example:

```text
Input:
candidates = [2, 3, 6, 7]
target = 7

Output:
[2, 2, 3]
[7]
```

---

## Approach

At every candidate, we have two choices:

```text
1. Include the current candidate
2. Exclude the current candidate
```

But there is one important difference.

If we **include** the candidate, we stay at the same index because the same number can be selected again.

If we **exclude** it, we move to the next index.

```text
                    candidate
                   /          \
              Include       Exclude
                 |              |
             same index      next index
```

For example, if the current candidate is `2`:

```text
Include 2 → [2]
Include 2 → [2, 2]
Include 2 → [2, 2, 2]
...
```

---

## Java Solution

```java
class Solution {

    private void combinationSumAlgo(
            int idx,
            int[] candidates,
            int target,
            List<Integer> subRes,
            List<List<Integer>> res) {

        // Base Case
        if (idx == candidates.length) {
            if (target == 0) {
                res.add(new ArrayList<>(subRes));
            }
            return;
        }

        // Include current candidate
        if (candidates[idx] <= target) {

            subRes.add(candidates[idx]);

            // Same index because the candidate can be reused
            combinationSumAlgo(
                    idx,
                    candidates,
                    target - candidates[idx],
                    subRes,
                    res
            );

            // Backtrack
            subRes.remove(subRes.size() - 1);
        }

        // Exclude current candidate
        combinationSumAlgo(
                idx + 1,
                candidates,
                target,
                subRes,
                res
        );
    }

    public List<List<Integer>> combinationSum(
            int[] candidates,
            int target) {

        List<List<Integer>> res = new ArrayList<>();

        combinationSumAlgo(
                0,
                candidates,
                target,
                new ArrayList<>(),
                res
        );

        return res;
    }
}
```

---

# The Important Part

The most important line in this solution is:

```java
combinationSumAlgo(
    idx,
    candidates,
    target - candidates[idx],
    subRes,
    res
);
```

Notice that the index is still:

```text
idx
```

and not:

```text
idx + 1
```

Why?

Because the same candidate can be used again.

For example:

```text
candidates = [2, 3]
target = 7
```

The recursion can do:

```text
2
↓
2 + 2
↓
2 + 2 + 2
↓
2 + 2 + 2 + 2
```

and then backtrack to try other combinations.

---

# Include vs Exclude

The recursion can be visualized as:

```text
                    [current state]
                     /           \
                  Include       Exclude
                     |             |
                 target - x     target
                     |             |
                 same index      idx + 1
```

### Include

```java
subRes.add(candidates[idx]);
```

Then:

```java
combinationSumAlgo(idx, ...);
```

The index stays the same because reuse is allowed.

### Exclude

```java
combinationSumAlgo(idx + 1, ...);
```

We skip the current candidate and move forward.

---

# Backtracking

After exploring the include branch:

```java
subRes.remove(subRes.size() - 1);
```

This removes the candidate that we just added.

The flow is:

```text
Add candidate
     ↓
Explore
     ↓
Remove candidate
     ↓
Try another choice
```

For example:

```text
subRes = [2, 2]

Add 3
→ [2, 2, 3]

Explore

Remove 3
→ [2, 2]
```

This allows the same list to be reused for different combinations.

---

# Base Case

The recursion stops when:

```java
if (idx == candidates.length)
```

At this point, there are no more candidates to process.

We only add the current combination if:

```java
target == 0
```

For example:

```text
[2, 2, 3] → target = 0 → valid
```

But:

```text
[2, 3] → target = 2 → not valid
```

So only combinations that exactly reach the target are stored.

---

# Why Check `candidates[idx] <= target`?

Before including a candidate:

```java
if (candidates[idx] <= target)
```

we make sure that selecting it does not make the remaining target negative.

For example:

```text
target = 3
candidate = 5
```

There is no reason to include `5`, because it has already exceeded the target.

---

# Example Recursion

For:

```text
candidates = [2, 3]
target = 7
```

A part of the recursion looks like:

```text
                     target = 7
                    /          \
                 Take 2       Skip 2
                   |             |
              target = 5      target = 7
                   |
                 Take 2
                   |
              target = 3
                /     \
            Take 2   Skip 2
               |        |
          target = 1  target = 3
```

The same index is used while taking `2`.

Once we skip `2`, we move to the next candidate.

---

# Recursion Pattern

This problem is a useful variation of the **include/exclude pattern**:

```text
                    Element
                   /       \
              Include     Exclude
                 |            |
            Same Index     Next Index
                 |            |
             target-x       target
```

The key difference is:

```text
Include → idx
Exclude → idx + 1
```

because candidates can be reused.

---

# Complexity

The exact time complexity depends on the candidate values and target because the recursion can generate many possible combinations.

In the worst case, the number of combinations can grow exponentially.

A common upper-bound way to describe the recursion is:

```text
O(2^n)
```

but for this particular problem, the repeated use of candidates means the actual number of recursive states also depends heavily on the target and the smallest candidate.

The output itself can also be large, so the cost of storing all valid combinations needs to be considered separately.

### Space Complexity

The recursion depth can grow based on how many times the smallest candidate can be selected.

If the smallest candidate is `m`, the depth can be roughly:

```text
O(target / m)
```

excluding the space required for storing the result.

---

# Key Takeaway

The main thing to remember from this problem is:

```text
Include → Stay at same index
Exclude → Move to next index
```

This pattern is useful when:

* Elements can be reused
* We need to generate combinations
* We need to explore multiple choices
* Backtracking is involved

The overall flow is:

```text
Choose
  ↓
Explore
  ↓
Backtrack
  ↓
Try another choice
```

Once this pattern becomes clear, many combination and backtracking problems become much easier to approach.

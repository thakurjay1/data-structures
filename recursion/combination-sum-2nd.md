# Combination Sum II

Given an array of candidates and a target, find all unique combinations where the selected numbers add up to the target.

Important difference from **Combination Sum**:

* Each candidate can be used **at most once**.
* The input can contain duplicate values.
* The result should not contain duplicate combinations.

For example:

```text
Input:
candidates = [10, 1, 2, 7, 6, 1, 5]
target = 8
```

Output:

```text
[1, 1, 6]
[1, 2, 5]
[1, 7]
[2, 6]
```

---

# Brute Force Approach

The basic idea is the same include/exclude pattern.

For every element:

```text
Include
   OR
Exclude
```

Since every element can be used only once, after including an element we move to:

```text
idx + 1
```

The problem is that duplicate values can generate the same combination multiple times.

For example:

```text
[1, 1, 2]
```

There are two different `1`s, so different recursion paths can produce the same combination.

To handle this, the brute-force solution stores the combinations in a `HashSet`.

---

## Java Solution — Brute Force

```java
class Solution {

    private void combinationSum2Algo(
            int idx,
            int[] candidates,
            int target,
            HashSet<List<Integer>> hashRes,
            List<Integer> subRes) {

        // Base Case
        if (idx == candidates.length) {

            if (target == 0) {
                hashRes.add(new ArrayList<>(subRes));
            }

            return;
        }

        // Include current element
        if (candidates[idx] <= target) {

            subRes.add(candidates[idx]);

            combinationSum2Algo(
                    idx + 1,
                    candidates,
                    target - candidates[idx],
                    hashRes,
                    subRes
            );

            // Backtrack
            subRes.remove(subRes.size() - 1);
        }

        // Exclude current element
        combinationSum2Algo(
                idx + 1,
                candidates,
                target,
                hashRes,
                subRes);
    }

    public List<List<Integer>> combinationSum2(
            int[] candidates,
            int target) {

        HashSet<List<Integer>> hashRes = new HashSet<>();

        Arrays.sort(candidates);

        combinationSum2Algo(
                0,
                candidates,
                target,
                hashRes,
                new ArrayList<>());

        List<List<Integer>> res = new ArrayList<>();

        for (List<Integer> ls : hashRes) {
            res.add(ls);
        }

        return res;
    }
}
```

---

# Why HashSet?

Suppose the input contains:

```text
[1, 1, 2]
```

The recursion can reach the same combination through different paths.

For example:

```text
[1(first), 2]
[1(second), 2]
```

Both produce:

```text
[1, 2]
```

A `HashSet` removes these duplicate lists.

So the approach is basically:

```text
Generate everything
        ↓
Store combinations in HashSet
        ↓
Duplicates are removed
```

This works, but it means we generate duplicate combinations first and remove them later.

---

# Brute Force Complexity

There are two choices for every element:

```text
Include / Exclude
```

Therefore, there can be up to:

```text
2^n
```

subsets.

However, when a valid combination is found, we create a copy:

```java
new ArrayList<>(subRes)
```

Copying a combination can take up to `O(n)`.

Also, adding a list to the `HashSet` requires hashing its elements, which can also take up to `O(n)`.

Therefore, a useful worst-case bound is:

### Time Complexity

```text
O(n log n)       → sorting
+
O(n × 2^n)       → recursion + storing combinations
```

Overall:

```text
O(n × 2^n)
```

### Space Complexity

The recursion depth is:

```text
O(n)
```

But the `HashSet` can contain many combinations.

In the worst case, the stored output can require:

```text
O(n × 2^n)
```

space.

So:

```text
Auxiliary recursion space = O(n)

HashSet/output space       = O(n × 2^n)
```

---

# Optimized Approach

The optimized solution avoids generating duplicate combinations in the first place.

Instead of:

```text
Generate duplicates → HashSet removes duplicates
```

we do:

```text
Skip duplicate choices → Generate only unique combinations
```

This is the main improvement.

---

## Java Solution — Optimized

```java
class Solution {

    private void combinationSum2Algo(
            int idx,
            int[] candidates,
            int target,
            List<List<Integer>> res,
            List<Integer> subRes) {

        // Valid combination found
        if (target == 0) {
            res.add(new ArrayList<>(subRes));
            return;
        }

        for (int i = idx; i < candidates.length; i++) {

            // Skip duplicate choices at the same level
            if (i > idx && candidates[i] == candidates[i - 1]) {
                continue;
            }

            // No further elements can be used
            if (candidates[i] > target) {
                break;
            }

            subRes.add(candidates[i]);

            // i + 1 because every element can be used only once
            combinationSum2Algo(
                    i + 1,
                    candidates,
                    target - candidates[i],
                    res,
                    subRes
            );

            // Backtrack
            subRes.remove(subRes.size() - 1);
        }
    }

    public List<List<Integer>> combinationSum2(
            int[] candidates,
            int target) {

        List<List<Integer>> res = new ArrayList<>();

        Arrays.sort(candidates);

        combinationSum2Algo(
                0,
                candidates,
                target,
                res,
                new ArrayList<>());

        return res;
    }
}
```

---

# Why Do We Sort First?

The sorting step is important.

Example:

```text
[1, 2, 1, 5]
```

After sorting:

```text
[1, 1, 2, 5]
```

Now duplicate values are next to each other.

This allows us to write:

```java
if (i > idx && candidates[i] == candidates[i - 1]) {
    continue;
}
```

So if we already considered the first `1` at the current recursion level, we skip the second `1`.

---

# Understanding the Duplicate Check

This line is one of the most important parts:

```java
if (i > idx && candidates[i] == candidates[i - 1]) {
    continue;
}
```

The condition:

```text
i > idx
```

means we only skip duplicates when they appear at the **same recursion level**.

For example:

```text
[1, 1, 2]
```

At the same level:

```text
Take first 1
Skip second 1
```

But after choosing the first `1`, we are allowed to choose the second `1` at the next level.

This is necessary to allow valid combinations such as:

```text
[1, 1, 6]
```

So:

```text
Same level       → skip duplicate
Different level  → duplicate can be used
```

This is a very important backtracking pattern.

---

# Why `i + 1`?

In Combination Sum, we used:

```text
idx
```

when including an element because the same candidate could be reused.

Here, every element can only be used once.

Therefore:

```java
combinationSum2Algo(i + 1, ...)
```

moves to the next element.

Example:

```text
[1, 2, 5]
     ↑
   choose 2

Next recursion starts after 2.
```

So the same array position cannot be selected again.

---

# Early Pruning

Another important optimization is:

```java
if (candidates[i] > target) {
    break;
}
```

Because the array is sorted, if:

```text
candidates[i] > target
```

then all elements after `i` will also be greater than the target.

There is no reason to explore them.

For example:

```text
candidates = [1, 2, 4, 7, 9]
target = 5
```

When we reach:

```text
7 > 5
```

we can stop the loop.

There is no need to check `9`.

This is called **pruning**.

---

# Backtracking Flow

The general flow is:

```text
Choose
  ↓
Reduce target
  ↓
Recursive call
  ↓
Remove choice
  ↓
Try next choice
```

For example:

```text
subRes = []

Choose 2
↓
subRes = [2]

Choose 3
↓
subRes = [2, 3]

Backtrack
↓
subRes = [2]

Try another choice
```

The same `subRes` list is reused throughout the recursion.

---

# Brute Force vs Optimized

| Brute Force                                | Optimized                                   |
| ------------------------------------------ | ------------------------------------------- |
| Uses Include/Exclude                       | Uses a loop + backtracking                  |
| Generates duplicate combinations           | Skips duplicates during recursion           |
| Uses `HashSet`                             | Directly stores unique results              |
| Duplicate removal happens after generation | Duplicate removal happens during generation |
| No early duplicate pruning                 | Skips duplicates at the same level          |
| Limited pruning                            | Uses sorting + `target` pruning             |
| Simpler to understand initially            | More efficient in practice                  |

The biggest conceptual difference is:

```text
Brute Force:

Generate → Duplicate → HashSet removes duplicate


Optimized:

Detect duplicate → Skip → Continue
```

---

# Complexity Comparison

Let `n` be the number of candidates.

## Brute Force

The recursion can explore up to:

```text
2^n
```

subsets.

Since valid combinations are copied and inserted into the `HashSet`:

```text
Time:  O(n × 2^n)
Space: O(n × 2^n)   // HashSet/output
       O(n)         // recursion stack
```

The sorting adds:

```text
O(n log n)
```

but this is dominated by the exponential recursion in the worst case.

---

## Optimized

The optimized solution still has an exponential worst case because the number of possible combinations can itself be exponential.

Therefore, the worst-case bound remains approximately:

```text
Time:  O(n × 2^n)
```

The output itself can contain exponentially many combinations, and copying those combinations costs additional `O(n)` per result.

### Space

Recursion depth:

```text
O(n)
```

excluding the result.

The result can require:

```text
O(n × 2^n)
```

in the worst case.

So:

```text
Auxiliary space = O(n)
Output space     = O(n × 2^n)
```

### Important Point

The optimized solution does **not** change the worst-case Big-O from exponential to polynomial.

Its main advantage is that it avoids unnecessary work:

```text
Duplicate branches
        ↓
Skipped early
        ↓
Fewer recursive calls
```

So it can be significantly faster in practice, especially when the input contains many duplicate values.

---

# Key Takeaway

Combination Sum II is a good example of how a brute-force recursion can be improved using **sorting, duplicate skipping, and pruning**.

The main pattern to remember is:

```text
Sort
 ↓
Loop through choices
 ↓
Skip duplicate choices at same level
 ↓
Choose
 ↓
Recursive call with i + 1
 ↓
Backtrack
```

And the most important line to understand is:

```java
if (i > idx && candidates[i] == candidates[i - 1]) {
    continue;
}
```

It means:

> If the same value has already been chosen as a starting choice at this recursion level, don't choose it again.

This lets us generate unique combinations directly instead of relying on a `HashSet` to clean them up afterward.

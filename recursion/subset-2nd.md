# Subsets II — Generate All Unique Subsets

## Problem

Given an integer array `nums` that may contain duplicates, return **all possible unique subsets**.

The solution should not contain duplicate subsets.

### Example

```text
Input:
nums = [1, 2, 2]

Output:
[
    [],
    [1],
    [1,2],
    [1,2,2],
    [2],
    [2,2]
]
```

Since `2` appears twice in the input, we need to make sure `[2]` is included only once.

---

# 1. Brute Force Solution

## Approach

The brute-force solution follows the standard **include/exclude recursion** pattern.

For every element, we have two choices:

1. Include the current element.
2. Don't include the current element.

So for every element, recursion branches into two paths.

```text
                         []
                      /      \
                   [1]        []
                  /   \      /  \
              [1,2] [1]   [2]  []
```

Because the input can contain duplicate values, different recursion paths can generate the same subset.

For example:

```text
nums = [1, 2, 2]
```

The subset:

```text
[2]
```

can be generated through different paths.

Instead of trying to avoid duplicates during recursion, the brute-force solution generates everything and uses a `HashSet` to remove duplicate lists.

---

## Java Solution

```java
class Solution {
    private void subsetsWithDupAlgo(
            int idx,
            int[] nums,
            Set<List<Integer>> set,
            List<Integer> subRes) {

        if (idx >= nums.length) {
            set.add(new ArrayList<>(subRes));
            return;
        }

        // Include current element
        subRes.add(nums[idx]);
        subsetsWithDupAlgo(idx + 1, nums, set, subRes);

        // Backtrack
        subRes.remove(subRes.size() - 1);

        // Exclude current element
        subsetsWithDupAlgo(idx + 1, nums, set, subRes);
    }

    public List<List<Integer>> subsetsWithDup(int[] nums) {

        Arrays.sort(nums);

        Set<List<Integer>> hashRes = new HashSet<>();

        subsetsWithDupAlgo(
            0,
            nums,
            hashRes,
            new ArrayList<>()
        );

        List<List<Integer>> res = new ArrayList<>();

        for (List<Integer> subRes : hashRes) {
            res.add(subRes);
        }

        return res;
    }
}
```

## Important Points

### 1. Why sort the array?

```java
Arrays.sort(nums);
```

Sorting is not required for the recursion itself, but it makes duplicate values adjacent.

For example:

```text
[2, 1, 2, 1]
```

becomes:

```text
[1, 1, 2, 2]
```

This becomes important in the optimized solution.

In the brute-force solution, the `HashSet` is responsible for removing duplicate subsets.

---

### 2. Why use `Set<List<Integer>>`?

```java
Set<List<Integer>> hashRes = new HashSet<>();
```

Different recursion paths can generate the same subset.

The `Set` automatically keeps only one copy.

For example:

```text
[1,2]
[1,2]
[1,2]
```

will become:

```text
[1,2]
```

---

### 3. Why `new ArrayList<>(subRes)`?

```java
set.add(new ArrayList<>(subRes));
```

`subRes` is continuously modified during backtracking.

Therefore, we must store a **copy** of the current state.

If we stored `subRes` directly, future `add()` and `remove()` operations would modify the stored result as well.

---

## Recursion Pattern

For every element:

```text
             Current
             /     \
         Include   Exclude
            |         |
          idx+1     idx+1
```

For `n` elements, there can be approximately:

```text
2^n
```

subsets/recursion paths.

---

## Backtracking

The important part is:

```java
subRes.add(nums[idx]);

subsetsWithDupAlgo(...);

subRes.remove(subRes.size() - 1);
```

Think of it as:

```text
Choose
  ↓
Explore
  ↓
Undo choice
  ↓
Explore next choice
```

Example:

```text
subRes = []

add 1
subRes = [1]

    explore...

remove 1
subRes = []

    explore without 1
```

---

## Time Complexity

There are approximately:

```text
2^n
```

possible subsets.

For every complete subset, we copy the current list:

```java
new ArrayList<>(subRes)
```

which can take up to `O(n)`.

Therefore:

```text
Time = O(n × 2^n)
```

Sorting also takes:

```text
O(n log n)
```

So overall:

```text
O(n log n + n × 2^n)
```

Since `n × 2^n` dominates `n log n`:

```text
Final Time Complexity = O(n × 2^n)
```

### Space Complexity

There are two important parts.

#### Recursion + current subset

The recursion depth can reach `n`:

```text
O(n)
```

The current subset can also contain up to `n` elements:

```text
O(n)
```

#### HashSet

The result can contain up to `2^n` subsets, and each subset can contain up to `n` elements:

```text
O(n × 2^n)
```

Therefore, considering the stored results:

```text
Space Complexity = O(n × 2^n)
```

Auxiliary recursion space excluding the result:

```text
O(n)
```

---

# 2. Optimized Recursive Solution

The brute-force solution generates duplicate subsets and removes them afterward.

The optimized solution takes a different approach:

> **Don't generate duplicate subsets in the first place.**

This is the key improvement.

```java
class Solution {
    private void subsetsWithDupAlgo(
            int idx,
            int[] nums,
            List<List<Integer>> res,
            List<Integer> subRes) {

        // Store current subset
        res.add(new ArrayList<>(subRes));

        for (int i = idx; i < nums.length; i++) {

            // Skip duplicate choices at the same recursion level
            if (i > idx && nums[i] == nums[i - 1]) {
                continue;
            }

            subRes.add(nums[i]);

            subsetsWithDupAlgo(
                i + 1,
                nums,
                res,
                subRes
            );

            // Backtrack
            subRes.remove(subRes.size() - 1);
        }
    }

    public List<List<Integer>> subsetsWithDup(int[] nums) {

        List<List<Integer>> res = new ArrayList<>();

        Arrays.sort(nums);

        subsetsWithDupAlgo(
            0,
            nums,
            res,
            new ArrayList<>()
        );

        return res;
    }
}
```

---

# How the Optimized Solution Thinks

Instead of thinking:

```text
Take current element
OR
Don't take current element
```

we now think:

> "From the current position, what elements can I choose next?"

That's why we use a `for` loop.

```java
for (int i = idx; i < nums.length; i++)
```

Each iteration represents one possible choice at the current recursion level.

---

# The Most Important Line

```java
if (i > idx && nums[i] == nums[i - 1]) {
    continue;
}
```

This means:

> If the current value is the same as the previous value **at the same recursion level**, don't make another identical choice.

For:

```text
nums = [1, 2, 2]
```

At the same level:

```text
        []
      / | \
     1  2  2
```

The two `2`s would create the same branch.

So we keep only the first `2`:

```text
        []
      /   \
     1     2
```

But this does **not** mean that we can never choose `2` again.

For example:

```text
[2, 2]
```

must still be generated.

Therefore:

```text
Skip duplicate at SAME LEVEL
        ↓
Allow duplicate at DEEPER LEVEL
```

This distinction is extremely important.

---

# Why `i > idx`?

Consider:

```java
if (i > idx && nums[i] == nums[i - 1])
```

The condition:

```java
i > idx
```

means that we only skip a duplicate if it is **not the first choice of the current recursion level**.

For example:

```text
nums = [1, 2, 2]
```

At `idx = 0`:

```text
i = 0 → 1
i = 1 → 2
i = 2 → 2
```

When:

```text
i = 2
```

we see another `2` at the same level, so we skip it.

But after choosing the first `2`, recursion moves to:

```text
idx = 2
```

Now the second `2` can be selected.

This allows:

```text
[2]
[2,2]
```

while avoiding duplicate branches.

---

# The Mental Shift Needed for This Type of Recursion

This is one of the most useful recursion concepts to understand.

When you see:

```java
for (...) {
    recursiveCall(...);
}
```

you might initially think:

> "There are multiple recursive calls, so do I need multiple lists to store the state of every recursive call?"

**No.**

The recursive calls do **not** execute simultaneously.

They execute one after another.

For example:

```text
                 []
                 |
          choose 1
                 |
                [1]
              /     \
        choose 2    choose 3
           |           |
         [1,2]       [1,3]
```

The program first goes deep into:

```text
[1,2]
```

and completes that recursive path.

Then it returns.

After returning, backtracking happens:

```java
subRes.remove(subRes.size() - 1);
```

Now `subRes` becomes:

```text
[1]
```

Then the next choice is explored:

```text
[1,3]
```

So we don't need:

```text
list1
list2
list3
...
```

for every recursive branch.

We can reuse **one mutable list**:

```java
List<Integer> subRes
```

and use backtracking to modify it.

---

# One List + Backtracking

Think of `subRes` as the **current path**.

```text
subRes = []
```

Choose `1`:

```text
subRes = [1]
```

Choose `2`:

```text
subRes = [1,2]
```

Save it:

```java
res.add(new ArrayList<>(subRes));
```

Then backtrack:

```text
subRes = [1]
```

Choose another element:

```text
subRes = [1,3]
```

Save it:

```java
res.add(new ArrayList<>(subRes));
```

Again backtrack.

The same list is continuously reused.

---

# Why Do We Create a New List Only When Storing the Result?

This line is extremely important:

```java
res.add(new ArrayList<>(subRes));
```

We use:

```text
One list → for current recursion state
```

but:

```text
New list → when permanently storing a result
```

Why?

Because `subRes` will continue changing.

Suppose:

```text
subRes = [1,2]
```

We store:

```java
res.add(new ArrayList<>(subRes));
```

Now `res` contains an independent copy:

```text
res = [[1,2]]
```

Then we backtrack:

```text
subRes = [1]
```

The stored result remains:

```text
res = [[1,2]]
```

This gives us a very useful general recursion/backtracking pattern:

```text
One mutable list
       ↓
Make a choice
       ↓
Recursive call
       ↓
Save a COPY if needed
       ↓
Undo the choice
       ↓
Make next choice
```

---

# Recursion Tree

For:

```text
nums = [1,2,2]
```

the optimized recursion can be visualized as:

```text
                         []
                   /     |      \
                 [1]     [2]     skip 2
                /   \
            [1,2]  [1,2,2]
               |
          [1,2,2]

                 [2]
                  |
                [2,2]
```

The second `2` at the root level is skipped:

```java
if (i > idx && nums[i] == nums[i - 1])
```

But the second `2` can still be selected deeper in the recursion.

---

# Why Is This Better Than the Brute Force Solution?

### Brute Force

```text
Generate everything
       ↓
Generate duplicate subsets
       ↓
HashSet removes duplicates
```

### Optimized

```text
Detect duplicate choices
       ↓
Skip duplicate branches
       ↓
Generate only unique subsets
```

So the optimized solution avoids the extra `HashSet` completely.

---

# Time Complexity

There can still be up to:

```text
2^n
```

unique subsets in the worst case when all elements are different.

For every generated subset:

```java
new ArrayList<>(subRes)
```

may take up to `O(n)`.

Therefore, worst-case:

```text
Time = O(n × 2^n)
```

Sorting takes:

```text
O(n log n)
```

So:

```text
O(n log n + n × 2^n)
```

which simplifies to:

```text
Final Time Complexity = O(n × 2^n)
```

### Important

The optimized solution is **much better in practice** when the input contains duplicates because it avoids generating duplicate branches.

However, its worst-case Big-O time complexity is still:

```text
O(n × 2^n)
```

because if all elements are unique, there are still `2^n` unique subsets.

---

# Space Complexity

### Recursion Stack

Maximum recursion depth:

```text
O(n)
```

### Current Subset

`subRes` can contain at most `n` elements:

```text
O(n)
```

### Result

In the worst case, there can be `2^n` unique subsets, each containing up to `n` elements:

```text
O(n × 2^n)
```

Therefore:

```text
Space Complexity = O(n × 2^n)
```

for the result storage.

Auxiliary space excluding the result:

```text
O(n)
```

---

# Brute Force vs Optimized

| Feature               | Brute Force                 | Optimized                    |
| --------------------- | --------------------------- | ---------------------------- |
| Recursion pattern     | Include / Exclude           | For-loop + recursion         |
| Duplicate handling    | `HashSet`                   | Skip duplicate choices       |
| Generates duplicates? | Yes                         | No                           |
| Result storage        | `HashSet` + `List`          | `List`                       |
| Sorting               | Yes                         | Yes                          |
| Worst-case Time       | `O(n × 2^n)`                | `O(n × 2^n)`                 |
| Result Space          | `O(n × 2^n)`                | `O(n × 2^n)`                 |
| Auxiliary Space       | `O(n)`                      | `O(n)`                       |
| Practical performance | More work due to duplicates | Better when duplicates exist |

---

# Key Takeaways

1. **Brute force** can generate duplicate results and use a `HashSet` to remove them.

2. **Optimized recursion** avoids duplicate branches before generating them.

3. In the optimized solution:

```java
if (i > idx && nums[i] == nums[i - 1])
```

means:

> Skip duplicate values at the same recursion level.

4. Duplicate values can still be selected at deeper levels, allowing subsets such as:

```text
[2,2]
```

5. Multiple recursive choices do **not** mean multiple lists are required. Recursive calls execute one at a time.

6. A single mutable list can represent the **current path**:

```text
choose → recurse → undo
```

7. When storing a result, create a copy:

```java
res.add(new ArrayList<>(subRes));
```

8. This is a very common backtracking pattern and appears in:

```text
Subsets
Subsets II
Permutations
Combination Sum
Combination Sum II
N-Queens
Maze / Path problems
```

### The Mental Model

```text
One list = current path

Make choice
    ↓
Go deeper
    ↓
Store a COPY if needed
    ↓
Backtrack
    ↓
Make another choice
```

That mental model is often more useful than thinking of recursion as creating separate lists for every branch.

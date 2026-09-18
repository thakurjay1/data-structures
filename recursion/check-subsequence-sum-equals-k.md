# Check Subsequence Sum Equals K

Given an array and a value `k`, check whether there exists a subsequence whose sum is equal to `k`.

For example:

```text
Input:
arr = [1, 2, 3]
k = 5

Output:
true

Because:
[2, 3] → 2 + 3 = 5
```

The important part is that for every element, we have two choices:

```text
Take the element
Don't take the element
```

So this problem can be solved using the same **take / don't-take recursion pattern** used for generating subsequences.

---

## Approach

At every index, we explore two possibilities.

```text
                    Current Element
                    /              \
                 Include          Exclude
                   |                 |
             sum + arr[idx]          sum
```

We continue until we reach the end of the array.

At that point, we check:

```java
sum == k
```

If it is true, we have found a valid subsequence.

---

## Java Solution

```java
class Solution {

    public boolean checkSubsequencesSumEqualsK(
            int idx,
            int[] arr,
            int k,
            int sum) {

        // Base Case
        if (idx >= arr.length) {
            return sum == k;
        }

        // Include current element
        boolean include = checkSubsequencesSumEqualsK(
                idx + 1,
                arr,
                k,
                sum + arr[idx]
        );

        // If a valid subsequence is already found,
        // no need to explore the remaining branch
        if (include) {
            return true;
        }

        // Exclude current element
        boolean exclude = checkSubsequencesSumEqualsK(
                idx + 1,
                arr,
                k,
                sum
        );

        return include || exclude;
    }

    public boolean checkSubsequenceSum(int[] arr, int k) {
        return checkSubsequencesSumEqualsK(0, arr, k, 0);
    }
}
```

---

## How the Recursion Works

For:

```text
arr = [1, 2, 3]
k = 5
```

The recursion starts with:

```text
index = 0
sum = 0
```

For `1`, there are two choices:

```text
                    sum = 0
                   /       \
              Include     Exclude
              sum = 1     sum = 0
```

Then the same process continues for `2` and `3`.

One branch eventually reaches:

```text
1 + 2 + 3 = 6
```

while another reaches:

```text
2 + 3 = 5
```

When that branch reaches the end:

```java
sum == k
```

becomes:

```text
5 == 5 → true
```

The `true` value is then returned back through the recursive calls.

---

## Early Return

This part is an important optimization:

```java
if (include) {
    return true;
}
```

Once the include branch finds a valid subsequence, there is no reason to explore the exclude branch.

For a problem that only asks:

> "Does at least one valid subsequence exist?"

we can stop as soon as we find one.

This can save unnecessary recursive calls in many cases.

---

## Why `sum` is Passed as a Parameter?

Instead of creating a separate list to store the current subsequence, we only need its sum.

For example:

```text
[1, 3] → sum = 4
```

So the recursive function carries:

```text
idx → current position
sum → sum of selected elements
k   → target sum
```

This makes the solution simpler because we don't actually need to store the subsequence itself.

---

## Recursion Pattern

The main pattern to remember is:

```text
                 Element
                /       \
            Include    Exclude
               |          |
          sum + element    sum
               |          |
           Recursive    Recursive
             Call         Call
```

This **include/exclude pattern** is useful whenever every element gives us two choices.

---

## Complexity

For `n` elements, every element can either be included or excluded.

Therefore, in the worst case:

```text
2^n
```

different choices are explored.

### Time Complexity

```text
O(2^n)
```

### Space Complexity

```text
O(n)
```

The recursion can go as deep as `n`.

---

## Key Takeaway

The main thing to learn from this problem is not just the code.

Whenever you see a problem involving:

* Choose an element
* Don't choose an element
* Find a possible combination
* Check whether some subsequence satisfies a condition

try thinking in terms of:

```text
Include / Exclude
```

Then use recursion to explore both choices and stop early when the required answer is found.

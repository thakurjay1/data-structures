# Merge Sort

Merge Sort is a **divide and conquer** sorting algorithm.

The main idea is:

```text
Divide → Sort → Merge
```

Instead of trying to sort the complete array at once, we keep dividing it into smaller parts until each part contains only one element.

A single element is already sorted.

Then we start merging the smaller sorted parts back together.

---

## Example

Consider:

```text
[5, 2, 8, 1, 3, 7]
```

The array is divided recursively:

```text
                 [5, 2, 8, 1, 3, 7]
                    /           \
              [5, 2, 8]       [1, 3, 7]
               /    \           /    \
            [5]    [2, 8]     [1]   [3, 7]
                    / \              / \
                  [2] [8]          [3] [7]
```

Now every part has one element.

Then the merging starts:

```text
[2] + [8] → [2, 8]

[5] + [2, 8] → [2, 5, 8]

[3] + [7] → [3, 7]

[1] + [3, 7] → [1, 3, 7]

[2, 5, 8] + [1, 3, 7]
            ↓
     [1, 2, 3, 5, 7, 8]
```

The important thing is that **we only merge already sorted parts**.

---

# Recursive Approach

The recursive function is responsible for dividing the array.

```java
public void mergeSortAlgo(int[] nums, int low, int high) {

    if (low >= high) {
        return;
    }

    int mid = low + (high - low) / 2;

    mergeSortAlgo(nums, low, mid);
    mergeSortAlgo(nums, mid + 1, high);

    mergeArray(nums, low, mid, high);
}
```

There are three important steps:

```text
1. Sort Left Half
2. Sort Right Half
3. Merge Both Halves
```

```java
mergeSortAlgo(nums, low, mid);
mergeSortAlgo(nums, mid + 1, high);
mergeArray(nums, low, mid, high);
```

---

## Base Case

```java
if (low >= high) return;
```

When `low == high`, there is only one element.

For example:

```text
[5]
```

There is nothing to sort, so recursion stops.

This is the **base case**.

---

# Merge Two Sorted Arrays

Once both halves are sorted, we merge them.

For example:

```text
Left  = [2, 5, 8]
Right = [1, 3, 7]
```

We compare the first elements:

```text
2 vs 1 → take 1
2 vs 3 → take 2
5 vs 3 → take 3
5 vs 7 → take 5
8 vs 7 → take 7
```

The remaining `8` is then added.

Result:

```text
[1, 2, 3, 5, 7, 8]
```

---

## Merge Function

```java
public void mergeArray(int[] nums, int low, int mid, int high) {

    int[] res = new int[high - low + 1];

    int i = low;
    int j = mid + 1;
    int k = 0;

    // Compare elements from both halves
    while (i <= mid && j <= high) {

        if (nums[i] <= nums[j]) {
            res[k] = nums[i];
            i++;
        } else {
            res[k] = nums[j];
            j++;
        }

        k++;
    }

    // Remaining elements from left half
    while (i <= mid) {
        res[k] = nums[i];
        k++;
        i++;
    }

    // Remaining elements from right half
    while (j <= high) {
        res[k] = nums[j];
        k++;
        j++;
    }

    // Copy sorted elements back into original array
    for (int x = 0; x < res.length; x++) {
        nums[low + x] = res[x];
    }
}
```

---

# Complete Solution

```java
class Solution {

    public void mergeArray(int[] nums, int low, int mid, int high) {

        int[] res = new int[high - low + 1];

        int i = low;
        int j = mid + 1;
        int k = 0;

        while (i <= mid && j <= high) {

            if (nums[i] <= nums[j]) {
                res[k] = nums[i];
                i++;
            } else {
                res[k] = nums[j];
                j++;
            }

            k++;
        }

        while (i <= mid) {
            res[k] = nums[i];
            k++;
            i++;
        }

        while (j <= high) {
            res[k] = nums[j];
            k++;
            j++;
        }

        for (int x = 0; x < res.length; x++) {
            nums[low + x] = res[x];
        }
    }

    public void mergeSortAlgo(int[] nums, int low, int high) {

        // Base Case
        if (low >= high) {
            return;
        }

        int mid = low + (high - low) / 2;

        // Sort Left Half
        mergeSortAlgo(nums, low, mid);

        // Sort Right Half
        mergeSortAlgo(nums, mid + 1, high);

        // Merge Both Sorted Halves
        mergeArray(nums, low, mid, high);
    }

    public int[] mergeSort(int[] nums) {

        int n = nums.length;

        mergeSortAlgo(nums, 0, n - 1);

        return nums;
    }
}
```

---

# Why Is Merge Sort `O(n log n)`?

The array keeps getting divided into half:

```text
n
n/2
n/4
n/8
...
1
```

This takes approximately:

```text
log n
```

levels.

At every level, the merging process touches all `n` elements.

Therefore:

```text
O(n) × O(log n)
```

### Time Complexity

```text
O(n log n)
```

This applies to the best, average, and worst cases.

### Space Complexity

The temporary array used during merging requires:

```text
O(n)
```

The recursion stack requires:

```text
O(log n)
```

So the overall auxiliary space is:

```text
O(n)
```

---

# Important Points

### 1. Merge Sort uses recursion

The array is repeatedly divided into smaller parts.

### 2. It follows Divide and Conquer

```text
Divide → Solve → Combine
```

### 3. Single element = Base Case

A single element is already sorted.

### 4. Merge is the key operation

Two sorted halves are combined into one sorted array.

### 5. Stable Sorting

Because the code uses:

```java
if (nums[i] <= nums[j])
```

equal elements from the left side are chosen first, which preserves their relative order.

---

# Key Takeaway

The main recursion pattern in Merge Sort is:

```text
                 Array
                /     \
          Left Half   Right Half
              ↓          ↓
           Recursion  Recursion
                \       /
                  Merge
                    ↓
              Sorted Array
```

The recursion itself does not directly sort the elements.

It **breaks the problem into smaller problems**, and the `mergeArray()` function combines their results.

This is the main idea behind **Divide and Conquer**.

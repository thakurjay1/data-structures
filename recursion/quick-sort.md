# Quick Sort

Quick Sort is a **divide and conquer** sorting algorithm.

The main idea is:

```text
Choose Pivot → Partition → Recursively Sort Left & Right
```

Instead of directly sorting the entire array, we choose a pivot and rearrange the elements around it.

After partitioning:

```text
Elements smaller/equal to pivot | Pivot | Elements greater than pivot
```

The pivot reaches its correct position.

---

## Example

Consider:

```text
[5, 3, 8, 4, 2, 7, 1, 6]
```

In this implementation, the first element is chosen as the pivot:

```text
Pivot = 5
```

After partitioning, the array can become:

```text
[1, 3, 2, 4, 5, 7, 8, 6]
             ↑
           Pivot
```

Now `5` is in its correct position.

We don't need to move it again.

The remaining work is:

```text
[1, 3, 2, 4]    5    [7, 8, 6]
     Left             Right
```

Both parts are then sorted recursively.

---

# Partitioning

The most important part of this implementation is the partition function.

```java
public int findPartitionIndex(int[] nums, int low, int high)
```

It uses:

```text
pivot = nums[low]
i = low
j = high
```

Two pointers move towards each other.

```text
i →                 ← j
[ ... elements ... ]
       pivot
```

### Pointer `i`

`i` moves from the left and looks for an element that is:

```text
> pivot
```

### Pointer `j`

`j` moves from the right and looks for an element that is:

```text
<= pivot
```

When both are found, they are swapped.

---

## Partition Example

Suppose:

```text
[5, 3, 8, 4, 2, 7, 1, 6]
 ↑
pivot = 5
```

`i` moves from the left:

```text
3 <= 5 → move
8 > 5  → stop
```

`j` moves from the right:

```text
6 > 5 → move
1 <= 5 → stop
```

Now:

```text
i → 8
j → 1
```

Swap them:

```text
[5, 3, 1, 4, 2, 7, 8, 6]
```

The process continues until the two pointers meet/cross.

Finally, the pivot is swapped with `nums[j]`.

This places the pivot at its final position.

---

# Java Solution

```java
class Solution {

    public int findPartitionIndex(int[] nums, int low, int high) {

        int pivot = nums[low];

        int i = low;
        int j = high;

        while (i < j) {

            // Find element greater than pivot
            while (nums[i] <= pivot && i <= high - 1) {
                i++;
            }

            // Find element smaller/equal to pivot
            while (nums[j] > pivot && j >= low + 1) {
                j--;
            }

            // Swap if pointers have not crossed
            if (i < j) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;
            }
        }

        // Put pivot in its correct position
        int temp = nums[low];
        nums[low] = nums[j];
        nums[j] = temp;

        return j;
    }

    public void quickSortAlgo(int[] nums, int low, int high) {

        if (low < high) {

            int partitionIndex =
                    findPartitionIndex(nums, low, high);

            // Sort left part
            quickSortAlgo(
                    nums,
                    low,
                    partitionIndex - 1
            );

            // Sort right part
            quickSortAlgo(
                    nums,
                    partitionIndex + 1,
                    high
            );
        }
    }

    public int[] quickSort(int[] nums) {

        quickSortAlgo(
                nums,
                0,
                nums.length - 1
        );

        return nums;
    }
}
```

---

# Recursive Flow

After partitioning, suppose the pivot ends up at index `p`.

The array is now divided into:

```text
[low ... p-1]   pivot   [p+1 ... high]
```

So we recursively sort both sides:

```java
quickSortAlgo(nums, low, p - 1);

quickSortAlgo(nums, p + 1, high);
```

Notice that the pivot itself is not included in either recursive call.

Why?

Because after partitioning, the pivot is already in its correct position.

---

# Base Case

The recursion stops when:

```java
if (low >= high)
```

This means the current section contains either:

```text
0 elements
or
1 element
```

A section containing one element is already sorted.

---

# Quick Sort Pattern

The complete idea can be remembered as:

```text
                    Array
                      |
                   Pivot
                      |
                 Partition
                /         \
        Left Part       Right Part
            |               |
        Recursion       Recursion
            \               /
             Sorted Array
```

Unlike Merge Sort, Quick Sort does most of its work **before the recursive calls**, during partitioning.

---

# Quick Sort vs Merge Sort

Both use **Divide and Conquer**, but the way they divide the problem is different.

| Merge Sort                    | Quick Sort                         |
| ----------------------------- | ---------------------------------- |
| Divides array into halves     | Divides around a pivot             |
| Merge happens after recursion | Partition happens before recursion |
| Uses extra array for merging  | Usually works in-place             |
| `O(n log n)` in all cases     | Average `O(n log n)`               |
| Worst case `O(n log n)`       | Worst case `O(n²)`                 |
| Stable                        | Usually not stable                 |

---

# Time Complexity

The performance depends heavily on how the pivot divides the array.

### Average Case

If the pivot divides the array reasonably well:

```text
O(n log n)
```

### Worst Case

If the pivot repeatedly becomes the smallest or largest element:

```text
O(n²)
```

For example, choosing the first element as the pivot for an already sorted array can lead to very unbalanced partitions.

### Space Complexity

Quick Sort uses recursion.

For balanced partitions:

```text
O(log n)
```

recursion stack space.

In the worst case:

```text
O(n)
```

recursion stack space.

The partitioning itself is done in-place, so no separate `O(n)` array is required like the basic Merge Sort implementation.

---

# Important Point About Pivot Selection

This implementation always chooses:

```java
int pivot = nums[low];
```

So the first element becomes the pivot.

This is simple, but the choice of pivot can have a major impact on Quick Sort's performance.

Other approaches include:

* Last element as pivot
* Random pivot
* Median-of-three
* Other pivot selection strategies

The basic idea remains the same:

```text
Choose Pivot
     ↓
Partition
     ↓
Pivot reaches correct position
     ↓
Recursively sort both sides
```

---

# Key Takeaway

The main thing to remember about Quick Sort is:

```text
Pivot → Partition → Recursion
```

The partition step puts the pivot in its final position and divides the remaining problem into two smaller parts.

Then recursion takes care of those two parts independently.

Quick Sort is a good example of how **recursion and divide-and-conquer work together to solve a sorting problem**.

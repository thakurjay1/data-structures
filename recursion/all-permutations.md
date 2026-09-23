# All Permutations

Given an array `nums` containing distinct integers, return all possible permutations.

### Example

```text
Input:  [1, 2, 3]

Output:
[
  [1, 2, 3],
  [1, 3, 2],
  [2, 1, 3],
  [2, 3, 1],
  [3, 1, 2],
  [3, 2, 1]
]
```

For `n` elements, there are:

```text
n! permutations
```

The main idea is:

> At every position, choose one of the elements that has not been used yet.

---

# 1. Recursion + Extra Space

### Idea

In this approach, we maintain a separate `boolean[] visited` array.

For every position:

1. Try every element.
2. If the element is already used, skip it.
3. Add the element to `subRes`.
4. Mark it as visited.
5. Recursively build the remaining permutation.
6. Remove the element and mark it unvisited.

This is a classic **choose → recurse → backtrack** pattern.

### Code

```java
class Solution {
    private void permuteAlgo(int[] nums, boolean[] visited,
                             List<List<Integer>> res,
                             List<Integer> subRes) {

        if(subRes.size() == nums.length){
            res.add(new ArrayList<>(subRes));
            return;
        }

        for(int i=0; i<nums.length; i++){
            if(visited[i] == true) continue;

            subRes.add(nums[i]);
            visited[i] = true;

            permuteAlgo(nums, visited, res, subRes);

            subRes.remove(subRes.size()-1);
            visited[i] = false;
        }
    }

    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();

        int n = nums.length;
        boolean[] visited = new boolean[n];

        permuteAlgo(nums, visited, res, new ArrayList<>());

        return res;
    }
}
```

## How recursion works

For:

```text
nums = [1, 2, 3]
```

At the first level, we can choose:

```text
        []
      /  |  \
     1   2   3
```

If we choose `1`:

```text
        []
        |
        1
       / \
      2   3
```

Then:

```text
1 → 2 → 3
1 → 3 → 2
```

Similarly, starting with `2`:

```text
2 → 1 → 3
2 → 3 → 1
```

And starting with `3`:

```text
3 → 1 → 2
3 → 2 → 1
```

So we get:

```text
1 2 3
1 3 2

2 1 3
2 3 1

3 1 2
3 2 1
```

---

## Important Lines

### 1. Checking whether an element is already used

```java
if(visited[i] == true) continue;
```

If an element has already been selected in the current permutation, we cannot select it again.

For example:

```text
nums = [1,2,3]

subRes = [1,2]

visited = [true,true,false]
```

So only `3` can be selected next.

---

### 2. Choose the element

```java
subRes.add(nums[i]);
visited[i] = true;
```

We do two things:

```text
Add element to current permutation
        +
Mark element as used
```

---

### 3. Recursive call

```java
permuteAlgo(nums, visited, res, subRes);
```

Now we try to fill the next position.

---

### 4. Backtracking

```java
subRes.remove(subRes.size()-1);
visited[i] = false;
```

This is the most important part.

After exploring one choice, we undo that choice so that another permutation can be generated.

For example:

```text
[1,2,3]
```

After finishing this permutation:

```text
remove 3
```

Now:

```text
[1,2]
```

Then we can try another choice:

```text
[1,3,2]
```

So the pattern is:

```text
Choose
   ↓
Recurse
   ↓
Undo choice
```

---

## Base Case

```java
if(subRes.size() == nums.length){
    res.add(new ArrayList<>(subRes));
    return;
}
```

When the current permutation contains all elements, we have found one complete permutation.

### Why create a new ArrayList?

```java
new ArrayList<>(subRes)
```

Because `subRes` is continuously modified during backtracking.

If we directly stored `subRes`, all entries could refer to the same changing list.

---

## Complexity

There are:

```text
n!
```

different permutations.

For every complete permutation, we copy `n` elements into `res`.

Therefore:

```text
Time = O(n × n!)
```

The recursion itself explores roughly `n!` leaves, but generating/storing each permutation requires `O(n)` work.

### Space

Extra space used:

```text
visited[]       → O(n)
subRes          → O(n)
recursion stack → O(n)
```

So auxiliary space is:

```text
O(n)
```

But the output itself contains `n!` permutations, each of size `n`:

```text
Output space = O(n × n!)
```

Therefore:

```text
Auxiliary Space = O(n)
Output Space    = O(n × n!)
Total Space     = O(n × n!)
```

---

# 2. Recursion + No Extra Space

### Idea

The second approach does not use a separate `visited[]` array.

Instead, we modify the original array itself using **swapping**.

At index `idx`, we try every element from `idx` to `n-1`.

For every choice:

```text
swap(idx, i)
    ↓
recurse(idx + 1)
    ↓
swap(idx, i) again
```

The second swap restores the original array.

This is another form of backtracking.

---

### Code

```java
class Solution {

    private void swap(int i, int j, int[] nums){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }

    private void permuteAlgo(int idx, int[] nums,
                             List<List<Integer>> res){

        if(idx >= nums.length){
            List<Integer> subRes = new ArrayList<>();

            for(int i=0; i<nums.length; i++){
                subRes.add(nums[i]);
            }

            res.add(new ArrayList<>(subRes));
            return;
        }

        for(int i=idx; i<nums.length; i++){

            swap(idx, i, nums);

            permuteAlgo(idx+1, nums, res);

            swap(idx, i, nums);
        }
    }

    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();

        permuteAlgo(0, nums, res);

        return res;
    }
}
```

---

## How it works

Suppose:

```text
nums = [1,2,3]
```

Initially:

```text
idx = 0
```

We can put any element at position `0`.

### Choose 1

```text
[1,2,3]
 ↑
idx = 0
```

Now solve the remaining part:

```text
[1,2,3]
    ↑
   idx=1
```

At index `1`, we can choose `2` or `3`.

```text
[1,2,3]
[1,3,2]
```

### Choose 2 at index 0

Swap:

```text
[1,2,3]
 ↓
[2,1,3]
```

Now recursively generate:

```text
[2,1,3]
[2,3,1]
```

### Choose 3 at index 0

Swap:

```text
[1,2,3]
 ↓
[3,2,1]
```

Generate:

```text
[3,2,1]
[3,1,2]
```

---

## The Important Backtracking Trick

This is the key line:

```java
swap(idx, i, nums);
```

Before recursion:

```text
Choose
```

After recursion:

```java
swap(idx, i, nums);
```

Undo the choice.

So the pattern is:

```text
swap
  ↓
recurse
  ↓
swap back
```

For example:

```text
nums = [1,2,3]

swap(0,1)

[2,1,3]

recurse...

After recursion:

swap(0,1)

[1,2,3]
```

The array is restored before trying the next possibility.

---

# Why This Doesn't Need `visited[]`

In the first approach, we need:

```java
boolean[] visited
```

to remember which elements have already been selected.

In the second approach, the array itself represents that information.

At:

```text
idx = 0
```

the first position is being decided.

At:

```text
idx = 1
```

the first position is already fixed, so we only work with positions `1...n-1`.

At:

```text
idx = 2
```

the first two positions are fixed.

So:

```text
[ fixed | choices ]
         ↑
        idx
```

This is why no separate `visited[]` array is required.

---

## Base Case

```java
if(idx >= nums.length)
```

When `idx` reaches the end:

```text
idx == n
```

all positions have been fixed.

Therefore, the current array represents one complete permutation.

We copy it into the result:

```java
List<Integer> subRes = new ArrayList<>();

for(int i=0; i<nums.length; i++){
    subRes.add(nums[i]);
}

res.add(new ArrayList<>(subRes));
```

---

# Complexity

There are still:

```text
n!
```

permutations.

At every completed permutation, we copy `n` elements:

```text
O(n)
```

Therefore:

```text
Time = O(n × n!)
```

The `swap()` operation itself takes:

```text
O(1)
```

but copying each final permutation costs `O(n)`.

### Space

There is no `visited[]` array.

We only use:

```text
nums[]             → O(n) input array
recursion stack    → O(n)
temporary variable  → O(1)
```

So auxiliary space is:

```text
O(n)
```

The output still requires:

```text
O(n × n!)
```

because we must store all permutations.

Therefore:

```text
Auxiliary Space = O(n)
Output Space    = O(n × n!)
Total Space     = O(n × n!)
```

---

# Difference Between Both Approaches

| Feature             | Extra Space                       | No Extra Space                  |
| ------------------- | --------------------------------- | ------------------------------- |
| Main idea           | `visited[]` tracks used elements  | Swap elements in original array |
| Extra array         | `boolean[] visited`               | No `visited[]`                  |
| Current permutation | `subRes`                          | `nums` itself                   |
| Choose              | `subRes.add(nums[i])`             | `swap(idx, i)`                  |
| Undo                | `remove()` + `visited[i] = false` | `swap(idx, i)` again            |
| Auxiliary Space     | O(n)                              | O(n)                            |
| Output Space        | O(n × n!)                         | O(n × n!)                       |
| Time                | O(n × n!)                         | O(n × n!)                       |

---

# The Main Difference in Thinking

### Approach 1 — Used/Unused

Think:

```text
Which elements have I already used?
```

So we maintain:

```java
boolean[] visited
```

Pattern:

```text
Choose element
    ↓
mark visited
    ↓
recurse
    ↓
unmark
```

---

### Approach 2 — Fixed/Unfixed

Think:

```text
Which position am I currently fixing?
```

So we use:

```java
idx
```

Pattern:

```text
Choose element for idx
        ↓
      swap
        ↓
     recurse
        ↓
    swap back
```

---

# Complexity Takeaway

Both approaches have the same asymptotic complexity:

```text
Time:
O(n × n!)

Auxiliary Space:
O(n)

Output Space:
O(n × n!)
```

The second approach is generally called **in-place backtracking** because it avoids the separate `visited[]` array and uses swapping to represent the current state.

But it is important to remember:

> "No extra space" does **not** mean O(1) space overall.

The recursion stack still takes `O(n)` space, and storing all `n!` permutations takes `O(n × n!)` output space.

---

# Key Takeaway

Permutation problems are a very common backtracking pattern.

### Using `visited[]`

```text
Choose → Mark → Recurse → Unmark
```

### Using swapping

```text
Swap → Recurse → Swap Back
```

The second approach removes the need for a separate `visited[]` array, but both approaches still need `O(n)` recursion depth and `O(n × n!)` space for the output.

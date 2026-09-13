# Climbing Stairs

The **Climbing Stairs** problem is another good problem for understanding the basic idea of Dynamic Programming.

The problem is simple: given `n` stairs, you can climb either **1 step or 2 steps at a time**. The goal is to find the total number of distinct ways to reach the top.

For example, if there are `3` stairs, the possible ways are:

```text
1 → 1 → 1
1 → 2
2 → 1
```

So the answer is:

```text
3 ways
```

---

# Understanding the Problem

At any point, there are only two choices:

```text
Take 1 step
Take 2 steps
```

Suppose we are standing at stair `n`.

To reach stair `n`, we could have come from:

```text
n - 1
```

by taking 1 step, or from:

```text
n - 2
```

by taking 2 steps.

Therefore:

```text
ways(n) = ways(n-1) + ways(n-2)
```

This is very similar to Fibonacci.

The important difference is the **base case**.

For this problem:

```text
ways(0) = 1
ways(1) = 1
```

`ways(0) = 1` can initially feel a little strange. It means there is exactly one way to reach the ground level: **do nothing**. This gives us the correct recurrence when building the solution.

---

# 1. Recursion

The most straightforward approach is to directly represent the choices using recursion.

```java
class Solution {
    public int climbStairs(int n) {
        if(n==0 || n==1){
            return 1;
        }

        int left = climbStairs(n-1);
        int right = climbStairs(n-2);

        return left + right;
    }
}
```

For every stair, we consider both possibilities:

```text
climb 1 step → solve(n-1)

climb 2 steps → solve(n-2)
```

Then we add the number of ways from both choices.

For example:

```text
        ways(4)
      /        \
 ways(3)      ways(2)
  /    \       /   \
w(2)   w(1)  w(1)  w(0)
```

The problem is that the same subproblems are calculated multiple times.

For example, `ways(2)` appears more than once.

As `n` increases, this repeated calculation becomes expensive.

### Complexity

**Time Complexity:** `O(2^n)`

The recursion tree grows exponentially because each state can generate two more recursive calls.

**Space Complexity:** `O(n)`

The maximum recursion depth can reach `n`, so the call stack requires `O(n)` space.

### Trade-off

The recursive solution is easy to understand and closely follows the actual decision-making process of the problem.

However, it performs a lot of unnecessary work because the same subproblems are solved repeatedly.

---

# 2. Memoization — Top Down

The recursive solution already gives us the correct recurrence:

```text
ways(n) = ways(n-1) + ways(n-2)
```

Instead of calculating the same state again, we can store its result.

This is **Memoization**.

```java
class Solution {
    public int climbStairsRec(int n, int[] dp){
        if(n==0 || n==1){
            return 1;
        }

        if(dp[n]!=-1)return dp[n];
        
        int left = climbStairsRec(n-1, dp);
        int right = climbStairsRec(n-2, dp);

        return dp[n] = left + right;
    }

    public int climbStairs(int n) {
        int[] dp = new int[n+1];
        Arrays.fill(dp, -1);

        return climbStairsRec(n, dp);
    }
}
```

Here, `dp[n]` represents the number of ways to reach the top when there are `n` steps remaining.

Initially, every position is set to `-1`, which means that the state has not been calculated yet.

When we calculate a state, we store the answer:

```java
dp[n] = left + right;
```

If the same state is encountered again:

```java
if(dp[n] != -1)return dp[n];
```

we simply return the stored result.

This prevents repeated calculations.

### Complexity

There are only `n + 1` possible states:

```text
0, 1, 2, ..., n
```

Each state is calculated only once.

**Time Complexity:** `O(n)`

**Space Complexity:** `O(n)`

The DP array takes `O(n)` space, and the recursive call stack can also reach `O(n)`.

### Trade-off

Memoization reduces the time complexity from exponential to linear while keeping the recursive structure.

It is often a convenient first step when converting a recursive solution into DP.

The main drawback is that it still relies on recursion and therefore requires recursion stack space.

A simple way to remember Memoization is:

> **Recursion + storing already calculated states.**

---

# 3. Tabulation — Bottom Up

The same recurrence can also be solved without recursion.

Instead of starting from `n` and moving down, we start from the base cases and build the answer upwards.

```java
class Solution {
    public int climbStairs(int n) {
        if(n==0 || n==1)return 1;

        int[] dp = new int[n+1];
        dp[0] = 1;
        dp[1] = 1;

        for(int i=2; i<=n; i++){
            dp[i] = dp[i-1] + dp[i-2];
        }

        return dp[n];
    }
}
```

Here:

```text
dp[0] = 1
dp[1] = 1
```

Then each subsequent state is calculated using the previous two states:

```text
dp[2] = dp[1] + dp[0]
dp[3] = dp[2] + dp[1]
dp[4] = dp[3] + dp[2]
...
dp[n] = dp[n-1] + dp[n-2]
```

For example, for `n = 5`:

```text
Index:  0   1   2   3   4   5
        -----------------------
dp:     1   1   2   3   5   8
```

So there are `8` different ways to climb `5` stairs.

### Complexity

**Time Complexity:** `O(n)`

Every state from `2` to `n` is calculated exactly once.

**Space Complexity:** `O(n)`

The DP array stores the result for every state.

### Trade-off

Tabulation removes recursion and therefore avoids recursion stack overhead.

It also makes the order in which states are calculated explicit, which can make the solution easier to reason about once the recurrence is known.

However, we are storing the entire DP array even though each state only depends on the previous two states.

So there is still an opportunity to reduce the space.

---

# 4. Space Optimization

Looking at the Tabulation solution, the transition is:

```text
dp[i] = dp[i-1] + dp[i-2]
```

This tells us that to calculate the current state, we only need the previous two states.

There is no need to keep the complete DP array.

We can therefore keep only two values:

```text
previous previous
previous
```

and calculate the current value from them.

```java
class Solution {
    public int climbStairs(int n) {
        if(n==0 || n==1)return 1;

        int[] dp = new int[2];
        dp[0] = 1;
        dp[1] = 1;

        for(int i=2; i<=n; i++){
            int curr = dp[0] + dp[1];

            dp[0] = dp[1];
            dp[1] = curr;
        }

        return dp[1];
    }
}
```

The important part is the update:

```java
int curr = dp[0] + dp[1];

dp[0] = dp[1];
dp[1] = curr;
```

After every iteration:

```text
dp[0] → previous state
dp[1] → current state
```

So instead of maintaining `n + 1` values, we only maintain the two values required for the next calculation.

### Complexity

**Time Complexity:** `O(n)`

We still calculate every state once.

**Space Complexity:** `O(1)`

Only a fixed amount of extra space is used regardless of `n`.

### Trade-off

This is the most space-efficient solution among the four approaches.

The only thing we lose is the complete DP table. If we needed the value of every intermediate state later, we would need to keep the array.

For this particular problem, however, we only need the final answer, so keeping the entire table is unnecessary.

---

# Comparing All Four Approaches

| Approach           |     Time |  Space | Main Idea                           |
| ------------------ | -------: | -----: | ----------------------------------- |
| Recursion          | `O(2^n)` | `O(n)` | Directly explore both choices       |
| Memoization        |   `O(n)` | `O(n)` | Recursion + cache calculated states |
| Tabulation         |   `O(n)` | `O(n)` | Build states from bottom to top     |
| Space Optimization |   `O(n)` | `O(1)` | Keep only the previous two states   |

---

# Recursion → Memoization → Tabulation → Space Optimization

The important part of this problem is understanding how one solution can gradually be improved.

### Step 1 — Think Recursively

At stair `n`, there are two possible previous positions:

```text
n - 1
n - 2
```

Therefore:

```text
ways(n) = ways(n-1) + ways(n-2)
```

### Step 2 — Identify Overlapping Subproblems

The recursive tree contains the same states multiple times.

```text
ways(4)
├── ways(3)
│   ├── ways(2)
│   └── ways(1)
└── ways(2)
    ├── ways(1)
    └── ways(0)
```

Here, `ways(2)` is calculated more than once.

### Step 3 — Apply Memoization

Store the answer for each state the first time it is calculated.

```text
Recursive solution
        +
Cache
        ↓
No repeated state calculations
```

This changes the time complexity from `O(2^n)` to `O(n)`.

### Step 4 — Convert to Tabulation

Once the states and transition are clear, we can remove recursion and calculate the states iteratively.

```text
ways(0)
   ↓
ways(1)
   ↓
ways(2)
   ↓
ways(3)
   ↓
...
ways(n)
```

### Step 5 — Optimize Space

The transition only depends on:

```text
ways(i-1)
ways(i-2)
```

So there is no need to store the complete DP table.

We can keep only the last two values.

```text
O(n) Space
    ↓
Keep required states only
    ↓
O(1) Space
```

---

# Important Takeaways

This problem is useful because it demonstrates one of the most common DP patterns:

```text
Current State = Combination of Previous States
```

Here, the current state depends on the previous two states:

```text
dp[i] = dp[i-1] + dp[i-2]
```

The main things to identify in similar problems are:

* What does the state represent?
* What choices can be made?
* Which smaller states lead to the current state?
* What are the base cases?
* Are the same states being calculated repeatedly?
* Can the results be stored?
* Does the current state depend on all previous states or only a few of them?

Once these questions become familiar, many DP problems become easier to approach.

---

# Final Summary

```text
Recursion
O(2^n) Time
O(n) Space
        ↓
Repeated subproblems
        ↓
Memoization
O(n) Time
O(n) Space
        ↓
Remove recursion
        ↓
Tabulation
O(n) Time
O(n) Space
        ↓
Only previous two states are needed
        ↓
Space Optimization
O(n) Time
O(1) Space
```

The final space-optimized solution is the most efficient among these four for this problem, but the progression is more important than simply knowing the optimized code.

The general learning pattern is:

> **First understand the recursion, then remove repeated work with Memoization, convert it to Tabulation, and finally check whether the DP table can be reduced to constant space.**

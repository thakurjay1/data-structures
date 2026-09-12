# Fibonacci Series

The Fibonacci problem is one of the simplest problems to understand Dynamic Programming because the basic recursive solution naturally contains **overlapping subproblems**.

The Fibonacci sequence is defined as:

```text
F(0) = 0
F(1) = 1

F(n) = F(n-1) + F(n-2)
```

So the sequence looks like:

```text
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, ...
```

For example:

```text
F(5) = F(4) + F(3)
     = 3 + 2
     = 5
```

The interesting part of this problem is not really Fibonacci itself. It is understanding how we can start with a straightforward recursive solution and progressively improve it using **Memoization, Tabulation, and Space Optimization**.

---

# 1. Recursion

The most direct way to implement the Fibonacci formula is recursion.

```java
class Solution {
    public int fib(int n) {
        if(n<=1)return n;

        return fib(n-1) + fib(n-2);
    }
}
```

The base cases are `F(0) = 0` and `F(1) = 1`. For every other value, we recursively calculate `F(n-1)` and `F(n-2)` and add them.

For example, calling `fib(5)` creates a recursion tree similar to this:

```text
                fib(5)
               /      \
          fib(4)      fib(3)
          /   \        /   \
      fib(3) fib(2) fib(2) fib(1)
       /  \
   fib(2) fib(1)
```

The problem is that the same values are calculated repeatedly.

For example, `fib(3)` is calculated more than once, and each `fib(3)` again calculates `fib(2)`.

This repeated calculation is what makes the naive recursive solution inefficient.

### Complexity

**Time Complexity:** `O(2^n)`

The recursion tree grows exponentially because each call can generate two more recursive calls.

**Space Complexity:** `O(n)`

Although the total number of calls is exponential, the maximum depth of the recursion stack is `n`.

### Trade-off

The biggest advantage of this approach is simplicity. It represents the Fibonacci definition almost exactly, so it is very useful for understanding the recursive relationship.

The problem is performance. We repeatedly solve the same subproblems, which makes this approach impractical for larger values of `n`.

---

# 2. Memoization — Top Down

The recursive solution already gives us the correct recurrence. Instead of changing that logic completely, we can simply remember the results that we have already calculated.

This approach is called **Memoization**.

```java
class Solution {

    public int fibonacci(int n, int[]dp){
        if(n<=1)return n;

        if(dp[n]!=-1)return dp[n];

        return dp[n] = fibonacci(n-1, dp) + fibonacci(n-2, dp);
    }
    
    public int fib(int n) {
        int[] dp = new int[n+1];
        Arrays.fill(dp, -1);
        return fibonacci(n, dp);
    }
}
```

Here, `dp[n]` represents the Fibonacci value for `n`.

Initially, all values are set to `-1`, which means that the value has not been calculated yet.

When we calculate a value for the first time, we store it:

```java
dp[n] = fibonacci(n-1, dp) + fibonacci(n-2, dp);
```

If we encounter the same `n` again, we don't calculate it again:

```java
if(dp[n] != -1)return dp[n];
```

For example, if `fib(3)` has already been calculated, the next time we need `fib(3)`, we simply return `dp[3]`.

So the recursion tree effectively becomes a collection of calculated states:

```text
First time:
fib(3) → calculate → store in dp[3]

Next time:
fib(3) → already present → return dp[3]
```

This removes the repeated work from the original recursive solution.

### Complexity

There are only `n + 1` possible states:

```text
0, 1, 2, 3, ..., n
```

Each state is calculated only once.

**Time Complexity:** `O(n)`

**Space Complexity:** `O(n)`

The `dp` array requires `O(n)` space, and the recursion stack can also go up to `O(n)`.

### Trade-off

Memoization gives a huge improvement in time complexity while keeping the recursive structure of the original solution.

The main drawback is that it still uses recursion, so there is recursion stack overhead. We also need an additional `dp` array to store the results.

A useful way to think about Memoization is:

> **Start with recursion and cache the answers of the subproblems.**

---

# 3. Tabulation — Bottom Up

With Tabulation, we remove recursion completely.

Instead of starting from `F(n)` and recursively going down, we start with the smallest known values and build the answer step by step.

```java
class Solution {
    public int fib(int n) {
        if(n<=1)return n;

        int dp[] = new int[n+1];
        dp[0] = 0;
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
dp[0] = 0
dp[1] = 1
```

Then we calculate:

```text
dp[2] = dp[1] + dp[0]
dp[3] = dp[2] + dp[1]
dp[4] = dp[3] + dp[2]
...
dp[n] = dp[n-1] + dp[n-2]
```

For `n = 5`, the table develops like this:

```text
Index:  0   1   2   3   4   5
        -----------------------
dp:     0   1   1   2   3   5
```

Once the table is built, `dp[n]` contains the answer.

### Complexity

**Time Complexity:** `O(n)`

We calculate every Fibonacci value from `2` to `n` exactly once.

**Space Complexity:** `O(n)`

We maintain an array of size `n + 1`.

### Trade-off

Tabulation has the same time complexity as Memoization but does not use recursion.

This makes the solution simpler from a runtime perspective because there is no recursion stack involved.

The downside is that we are storing the complete DP table even though, for Fibonacci, we don't actually need all previous values at the same time.

To calculate the next Fibonacci number, we only need the previous two numbers.

That leads us to the next optimization.

---

# 4. Space Optimization

Looking at the Tabulation solution, we can notice something important.

To calculate:

```text
dp[i] = dp[i-1] + dp[i-2]
```

we only need:

```text
dp[i-1]
dp[i-2]
```

We don't need the entire array.

For example:

```text
Before calculating next value:

previous previous    previous
      0                 1

After calculation:

previous previous    previous
      1                 1
```

So instead of maintaining `n + 1` elements, we can maintain only two values.

The given implementation uses a two-element array:

```java
class Solution {
    public int fib(int n) {
        if(n<=1)return n;

        int dp[] = new int[2];
        dp[0] = 0;
        dp[1] = 1;

        int res = -1;

        for(int i=0; i<=n-2; i++){
            res = dp[0] + dp[1];
            dp[0] = dp[1];
            dp[1] = res;
        }

        return res;
    }
}
```

The important part is the update:

```java
res = dp[0] + dp[1];

dp[0] = dp[1];
dp[1] = res;
```

After every iteration:

```text
dp[0] → previous Fibonacci number
dp[1] → current Fibonacci number
```

So we keep moving these two values forward instead of storing the complete sequence.

### Complexity

**Time Complexity:** `O(n)`

We still calculate each required Fibonacci number once.

**Space Complexity:** `O(1)`

Only a fixed number of variables/elements are maintained regardless of the value of `n`.

---

# Comparing All Four Approaches

| Approach           |     Time |  Space | Main Idea                                        |
| ------------------ | -------: | -----: | ------------------------------------------------ |
| Recursion          | `O(2^n)` | `O(n)` | Directly follow the recursive formula            |
| Memoization        |   `O(n)` | `O(n)` | Recursion + store already calculated results     |
| Tabulation         |   `O(n)` | `O(n)` | Build the solution iteratively                   |
| Space Optimization |   `O(n)` | `O(1)` | Keep only the values required for the next state |

---

# Recursion → Memoization → Tabulation → Optimization

The progression is important to understand because this is how many DP problems can be approached.

### Step 1 — Start with Recursion

First, try to identify the recursive relationship.

```text
F(n) = F(n-1) + F(n-2)
```

### Step 2 — Identify Repeated Work

Notice that the same states are being calculated multiple times.

```text
F(3)
F(2)
F(1)
...
```

### Step 3 — Apply Memoization

Keep the recursive approach but store the results of already solved states.

```text
Recursion + Cache
      ↓
O(2^n) → O(n)
```

### Step 4 — Convert to Tabulation

Once the states and transitions are clear, we can remove recursion and build the result from the bottom up.

```text
Base Cases
    ↓
F(2)
    ↓
F(3)
    ↓
F(4)
    ↓
...
    ↓
F(n)
```

### Step 5 — Look for Space Optimization

Finally, ask:

> Do I really need to store every previously calculated state?

For Fibonacci, the answer is no. Only the previous two values are required.

```text
O(n) Space
    ↓
Keep only required states
    ↓
O(1) Space
```

---

# Important DP Takeaways

Fibonacci is a very simple problem, but it demonstrates the basic thought process behind Dynamic Programming.

The most important thing to take away is not the Fibonacci code itself. It is the progression:

```text
Recursion
    ↓
Find overlapping subproblems
    ↓
Memoization
    ↓
Tabulation
    ↓
Space Optimization
```

Memoization and Tabulation solve the repeated-subproblem issue in different ways. Memoization is generally easier to derive from a recursive solution, while Tabulation removes recursion and builds the answer iteratively.

Space optimization is a separate improvement. Once the transition is understood, we should check whether the complete DP table is actually required. If the current state depends only on a few previous states, the DP array can often be reduced to a few variables.

---

# One More Practical Point

The examples above use Java `int`. Fibonacci numbers grow very quickly, so an `int` cannot represent arbitrarily large Fibonacci values.

For the standard Java `int` range, overflow occurs once the Fibonacci value goes beyond the maximum value that an `int` can store. If larger values are required, a different numeric type such as `long` or `BigInteger` should be considered depending on the problem constraints.

---

## Final Summary

```text
Recursion
O(2^n) Time
O(n) Space
        ↓
Repeated calculations
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
Only previous two values are required
        ↓
Space Optimization
O(n) Time
O(1) Space
```

For this problem, the **space-optimized solution is the most efficient of the four in terms of both time and auxiliary space**. However, the other three approaches are equally important from a learning perspective because they show how a basic recursive solution can gradually be improved into an optimized DP solution.

# Dynamic Programming (DP)

Dynamic Programming is a problem-solving technique used when a problem can be broken down into smaller subproblems, where the same subproblems may be solved multiple times.

Instead of solving the same subproblem again and again, DP stores the result of a subproblem and reuses it whenever required.

---

## Why Dynamic Programming?

A common issue with recursive solutions is **repeated calculation**.

For example, consider Fibonacci:

```text
fib(5)
├── fib(4)
│   ├── fib(3)
│   └── fib(2)
└── fib(3)
    ├── fib(2)
    └── fib(1)
```

Here, `fib(3)` and `fib(2)` are calculated more than once.

DP solves this problem by storing already calculated results.

```text
Without DP
Recursive Calls
      ↓
Repeated Subproblems
      ↓
More Computation
      ↓
Higher Time Complexity
```

With DP:

```text
Solve Subproblem
      ↓
Store Result
      ↓
Reuse Result
      ↓
Avoid Recalculation
```

The main idea is:

> **Solve each subproblem once and reuse its result.**

---

## When Should We Think About DP?

A problem is generally a good candidate for Dynamic Programming when it contains:

### 1. Overlapping Subproblems

The same smaller problem appears multiple times while solving the larger problem.

### 2. Optimal Substructure

The solution to a larger problem can be constructed using solutions of its smaller subproblems.

---

# Two Approaches to Dynamic Programming

There are two common ways to implement DP:

1. **Memoization — Top Down**
2. **Tabulation — Bottom Up**

---

## 1. Memoization — Top Down

Memoization starts with a **recursive solution**.

Whenever a subproblem is solved, its result is stored. If the same subproblem is encountered again, the stored result is returned instead of calculating it again.

```text
Recursive Solution
       ↓
Solve a Subproblem
       ↓
Store the Result
       ↓
Subproblem Appears Again?
       ↓
Return Stored Result
```

### General Structure

```text
solve(state):

    if state is already calculated:
        return stored result

    if base case:
        return base value

    result = solve(smaller state)

    store result

    return result
```

### Key Points

* Uses recursion.
* Uses an additional DP array/table for storing results.
* Usually starts from the final problem and recursively moves towards smaller problems.
* Easy to derive when a recursive solution is already available.

---

## 2. Tabulation — Bottom Up

Tabulation solves the problem **iteratively**.

Instead of starting with recursion, we start from the smallest known states and gradually build the solution until we reach the required answer.

```text
Base Cases
    ↓
Small Subproblems
    ↓
Larger Subproblems
    ↓
...
    ↓
Final Answer
```

### General Structure

```text
Initialize base cases

for each state:
    calculate result
    using previously calculated states

return final state
```

### Key Points

* Uses loops instead of recursion.
* Builds the DP table from smaller states to larger states.
* Avoids recursion stack overhead.
* Often provides better control over the order in which states are calculated.

---

# Memoization vs Tabulation

| Memoization                          | Tabulation                           |
| ------------------------------------ | ------------------------------------ |
| Top Down                             | Bottom Up                            |
| Uses recursion                       | Uses iteration                       |
| Starts from the required/final state | Starts from base cases               |
| Calculates states when needed        | Usually builds states systematically |
| Uses DP array/table                  | Uses DP array/table                  |
| Easier to derive from recursion      | Avoids recursion overhead            |

---

# How DP Optimizes Recursion

Consider a recursive problem where the same state is reached multiple times.

```text
             Problem
            /       \
        State A    State B
         /  \       /  \
    State C State D State C State E
                       ↑
                 Repeated State
```

Normal recursion calculates `State C` again.

With DP:

```text
First time:
State C → Calculate → Store

Next time:
State C → Already stored → Reuse
```

So DP does not necessarily change the original recursive logic. Instead, it **removes unnecessary repeated work by remembering previously calculated states**.

---

# Basic DP Thought Process

When solving a new DP problem, try to think in this order:

```text
1. Identify the state
        ↓
2. Identify the choices
        ↓
3. Define the recurrence / transition
        ↓
4. Identify the base cases
        ↓
5. Check for overlapping subproblems
        ↓
6. Store the results
        ↓
7. Implement using Memoization or Tabulation
```

The most important part is not writing the `dp[]` array.

The important part is understanding:

> **What does each DP state represent, and how is the next state calculated from previous states?**

---

# Common DP Pattern

A simple way to visualize many DP problems is:

```text
Problem
   ↓
Break into smaller states
   ↓
Find repeated states
   ↓
Define state
   ↓
Define transition
   ↓
Define base cases
   ↓
Store results
   ↓
Build final answer
```

Depending on the problem, the DP structure may be:

```text
1D DP       → dp[i]

2D DP       → dp[i][j]

Multiple states → dp[i][j][k]

Other forms  → Depends on the problem
```

---

# Time and Space Optimization

The biggest advantage of DP is usually the reduction in **time complexity** by avoiding repeated calculations.

For example, a naive recursive solution may have exponential time complexity:

```text
O(2^n)
```

After storing overlapping subproblems, the same problem may be solved in:

```text
O(n)
```

The exact improvement depends on the problem and the number of unique states.

There is usually a trade-off:

```text
Less Computation
      ↓
Store More Results
      ↓
Additional Space
```

So while solving DP problems, both **time complexity and space complexity** should be considered.

---

# DP Learning Approach

For each problem in this repository, the goal is to understand the problem first and then solve it using both approaches wherever applicable:

```text
Problem
   ↓
Recursive Thinking
   ↓
Memoization
   ↓
Tabulation
   ↓
Time & Space Complexity
   ↓
Optimization / Alternative Approach
```

The focus is not just on memorizing DP patterns, but on developing the ability to recognize **states, transitions, overlapping subproblems, and optimal substructure**.

---

## Final Takeaway

Dynamic Programming is mainly about **avoiding repeated work**.

If recursion repeatedly solves the same subproblem, we can store the result and reuse it.

The two standard approaches are:

* **Memoization → Top Down + Recursion + Caching**
* **Tabulation → Bottom Up + Iteration + DP Table**

The core idea remains the same:

> **Calculate a subproblem once, store its result, and reuse it whenever needed.**

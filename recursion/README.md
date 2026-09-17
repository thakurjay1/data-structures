# Recursion

Recursion is a technique where a function calls itself to solve a smaller version of the same problem.

A recursive solution usually has two important parts:

1. **Base Case** – tells the function when to stop.
2. **Recursive Case** – breaks the problem into a smaller problem and calls the function again.

### Simple Example

```java
void print(int n) {
    if (n == 0) {          // Base Case
        return;
    }

    System.out.println(n);
    print(n - 1);          // Recursive Call
}
```

For `print(3)`:

```text
3 → 2 → 1 → 0
```

The function keeps calling itself until the base case is reached.

---

## Why Recursion?

Recursion is useful when a problem can naturally be divided into smaller versions of itself.

Common examples:

* Tree and graph traversal
* Divide and conquer
* Backtracking
* Dynamic Programming
* Searching and sorting
* Generating subsequences/permutations
* Problems involving nested structures

The main idea is:

> Solve a smaller problem first, then use that result to solve the bigger problem.

---

# Types of Recursion

## 1. Functional Recursion

In functional recursion, the recursive function **returns a value** and the returned value is used to build the final answer.

### Example: Factorial

```java
int factorial(int n) {
    if (n == 0) {
        return 1;
    }

    return n * factorial(n - 1);
}
```

For `factorial(4)`:

```text
4 * factorial(3)
    3 * factorial(2)
        2 * factorial(1)
            1 * factorial(0)
                1
```

While returning:

```text
1 → 1 × 1 → 2 × 1 → 3 × 2 → 4 × 6

Answer = 24
```

Here, each recursive call returns something to its previous call.

---

## 2. Parameterized Recursion

In parameterized recursion, the required result is generally **carried through the function parameters**.

### Example: Sum of numbers from 1 to N

```java
void sum(int n, int result) {
    if (n == 0) {
        System.out.println(result);
        return;
    }

    sum(n - 1, result + n);
}
```

Calling:

```java
sum(5, 0);
```

The value of `result` keeps changing:

```text
sum(5, 0)
sum(4, 5)
sum(3, 9)
sum(2, 12)
sum(1, 14)
sum(0, 15)
```

Final answer:

```text
15
```

The important idea is that the **parameter carries the current state/result** through recursive calls.

---

# Multiple Recursive Calls

Sometimes one function call creates **more than one recursive call**.

A standard example is Fibonacci.

```java
int fibonacci(int n) {
    if (n <= 1) {
        return n;
    }

    return fibonacci(n - 1) + fibonacci(n - 2);
}
```

For `fibonacci(5)`:

```text
                 fib(5)
                /      \
            fib(4)     fib(3)
            /   \       /   \
        fib(3) fib(2) fib(2) fib(1)
          ...
```

Each call can create two more calls.

This creates a **recursion tree**.

### Why is this important?

Multiple recursive calls are common in problems where there are multiple choices at each step.

For example:

* Fibonacci
* Generate all subsequences
* Generate permutations
* Maze/path problems
* Backtracking
* Divide and conquer

The number of calls can grow very quickly. For basic recursive Fibonacci, the time complexity is approximately:

```text
O(2^n)
```

This is also why recursion is often combined with **Memoization / Dynamic Programming** to avoid solving the same subproblem repeatedly.

---

# Recursion and the Call Stack

Every recursive call gets its own stack frame.

For:

```java
print(3);
```

The calls look like:

```text
print(3)
   ↓
print(2)
   ↓
print(1)
   ↓
print(0)
```

Once the base case is reached, the calls start returning in reverse order.

Understanding the **call stack** is one of the most important things when learning recursion.

---

# General Recursion Pattern

Most recursive problems can be approached using these steps:

```text
1. Identify the smaller problem
2. Define the base case
3. Make the recursive call
4. Decide what happens before/after the recursive call
5. Check how the calls return
```

A simple template:

```java
returnType function(parameters) {

    // Base Case
    if (condition) {
        return result;
    }

    // Recursive Case
    return function(smallerProblem);
}
```

Recursion is less about memorizing patterns and more about understanding **how a problem becomes a smaller version of itself and how the calls return back**.

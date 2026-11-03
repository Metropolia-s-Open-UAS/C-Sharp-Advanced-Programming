#### Lambda Expressions in C#
- A Lambda Expression is a concise way to define an anonymous function without explicitly declaring a named method. It uses the lambda operator (=>) to separate input parameters from the expression or statements to be executed.
    - Commonly used with delegates, LINQ, and collection operations.
    - Supports both single-expression and multi-statement bodies.
    - Can be converted to compatible delegate types or expression tree types.

- Explanation: The lambda expression `x => x * x` takes an integer x and returns its square.
`Syntax`
> (parameters) => expression
1. parameters: Input values accepted by the lambda expression.
2. =>: Lambda operator that separates parameters from the body.
3. expression: Operation performed using the input parameters.
For example:
```CSharp
Func<int, int> square = x => x * x;
```
#### Types of Lambda Expressions in C#

| Feature | Expression Lambda | Statement Lambda |
|---|---|---|
| **Definition** | Contains a single expression whose result is returned to the caller | Contains multiple statements enclosed in curly braces `{}` |
| **Syntax** | `(parameters) => expression` | `(parameters) => { // statements }` |
| **Body** | Single expression (no braces) | Block of statements (with braces) |
| **Return** | Implicit — the expression's result is returned automatically | Explicit — requires a `return` statement (if a value is returned) |
| **Complexity** | Simple, one-liner operations | Complex operations: variable declarations, loops, conditionals |
| **Braces `{}`** | Not used | Required |
| **`return` keyword** | Not needed | Needed when returning a value |
| **Example** | `Func<int, int> square = x => x * x;` | `Func<int, int> square = x => { int result = x * x; return result; };` |
| **Best for** | Short, concise logic (e.g., LINQ queries) | Multi-step logic requiring multiple operations |

#### Quick Comparison Example

**Expression Lambda:**
```csharp
// Returns the square of a number
Func<int, int> square = x => x * x;
```

**Statement Lambda:**
```csharp
Func<int, int> square = x =>
{
    int result = x * x;
    Console.WriteLine($"Square computed: {result}");
    return result;
};
```
#### Advantages and Limitations of Lambda Expressions in C#

#### Advantages

| # | Advantage | Explanation |
|---|---|---|
| 1 | **Concise Syntax** | Reduces the amount of code required for simple functions — no need for a full method declaration with a name, return type, and access modifier. |
| 2 | **Improved Readability** | Keeps short operations written close to where they are used, making intent clearer in context (e.g., inline in LINQ queries). |
| 3 | **LINQ Integration** | Simplifies filtering, sorting, and transforming collections using methods like `Where`, `Select`, and `OrderBy`. |
| 4 | **Flexibility** | Supports anonymous functions and can **capture variables** from the enclosing scope (closures), enabling dynamic, context-aware behavior. |

---

#### Limitations

| # | Limitation | Explanation |
|---|---|---|
| 1 | **Readability (Complexity)** | Complex or deeply nested lambdas can become difficult to understand — hard to trace logic across multiple chained lambdas. |
| 2 | **Debugging** | Large lambda bodies (especially statement lambdas) may be harder to debug than well-named, dedicated methods. Stack traces and breakpoints are less intuitive. |
| 3 | **Variable Capture** | Captured variables (closures) can introduce **unexpected behavior** if their values change after the lambda is defined — a common source of bugs. |
| 4 | **Expression Tree Restrictions** | Not all lambda constructs (e.g., loops, certain statements) can be represented as **expression trees**, which are required by some APIs (e.g., Entity Framework query providers). |

---

#### Side-by-Side Comparison

| Category | Advantage | Corresponding Risk |
|---|---|---|
| **Syntax** | Short, concise | Deeply nested lambdas hurt readability |
| **Location** | Inline near usage | Hard to debug when logic grows |
| **LINQ** | Powerful collection operations | Chained lambdas obscure flow |
| **Closures** | Flexible, context-aware | Stale/changed captured values cause bugs |
| **Expression Trees** | Enables query translation (e.g., SQL) | Limited constructs supported |

---

#### Best Practices (Balancing Both Sides)

1. **Keep lambdas short** — if a lambda exceeds a few lines, extract it into a **named method**.
2. **Avoid deep nesting** — break complex chains into intermediate variables for clarity.
3. **Be cautious with captured variables** — especially inside loops or when values may change before execution.
4. **Know your context** — use **expression lambdas** for `IQueryable` (expression trees); use **statement lambdas** only for in-memory `IEnumerable` operations.
5. **Name intent clearly** — even though lambdas are anonymous, choose meaningful parameter names (e.g., `student => student.Name` instead of `s => s.Name`).

---

#### Quick Reference

```
✅ Use lambda when:  short, single-purpose logic; LINQ queries; callbacks/event handlers
⚠️  Reconsider when: multi-step logic; reused in many places; complex closures; debugging needed
```

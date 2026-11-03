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

### Quick Comparison Example

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

#### using Lambda with LINQ
#### Summary of Lambda Expression Examples

Here's a breakdown of the two example programs demonstrating lambda expressions with LINQ in C#.

---

#### Example 1: LINQ Operations — Square & Filter

| Aspect | Details |
|---|---|
| **Purpose** | Calculate squares of numbers and filter those divisible by 3 |
| **Input List** | `{ 6, 7, 12, 15, 9 }` |
| **Lambda Used (Square)** | `x => x * x` (Expression Lambda) |
| **Lambda Used (Filter)** | `x => x % 3 == 0` (Expression Lambda) |
| **LINQ Methods** | `Select()` and `Where()` |
| **Output** | Squares: `36, 49, 144, 225, 81` <br> Divisible by 3: `6, 12, 15, 9` |

#### Key Points
- **`Select(x => x * x)`** — transforms each element by squaring it.
- **`Where(x => x % 3 == 0)`** — filters elements using a predicate.
- Both operations are **non-destructive** — the original `numbers` list remains unchanged.

---

#### Example 2: Sorting Students by Name

| Aspect | Details |
|---|---|
| **Purpose** | Sort a list of students alphabetically by name |
| **Class Used** | `Student` (with `RollNo` and `Name` properties) |
| **Lambda Used** | `s => s.Name` (Expression Lambda — key selector) |
| **LINQ Method** | `OrderBy()` |
| **Output** | `1 Liza`, `4 Stefani`, `2 Stewart`, `3 Tina` |

#### Key Points
- **`s => s.Name`** — extracts the sorting key (`Name`) from each `Student` object.
- **`OrderBy()`** returns a **new sorted sequence**, leaving the original list untouched.

---

#### Comparison Table

| Feature | Example 1 | Example 2 |
|---|---|---|
| **Data Type** | `List<int>` | `List<Student>` |
| **Lambda Type** | Expression Lambda | Expression Lambda |
| **LINQ Methods** | `Select`, `Where` | `OrderBy` |
| **Lambda Purpose** | Transformation & Filtering | Key Selection for Sorting |
| **Modifies Original?** | No | No |
| **Output Format** | Joined string | `foreach` loop with interpolation |

---

#### Common Takeaways

1. **Expression lambdas** (`x => expression`) are ideal for concise LINQ operations — no braces or `return` needed.
2. **LINQ methods are lazy/immutable** — they return new sequences and never modify the source collection.
3. **Lambda expressions serve different roles** depending on the LINQ method:
   - `Select` → transformation
   - `Where` → filtering predicate
   - `OrderBy` → key selector
4. Both examples rely on **type inference** — the compiler deduces parameter types (`int`, `Student`) from context.

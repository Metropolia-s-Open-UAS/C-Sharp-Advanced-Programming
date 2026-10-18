#### Core language features

| Topic | Key concepts | Why it matters |
|---|---|---|
| Generics | Constraints (`where T : struct`, `notnull`, `INumber<T>`), covariance/contravariance (`out`/`in`), generic math | Type-safe reusable code, with no boxing |
| Delegates & events | `Func`/`Action`/`Predicate`, multicast delegates, event patterns | Callbacks and decoupled design |
| LINQ | Deferred execution, expression trees, `IQueryable` vs `IEnumerable`, custom operators | Querying data, and how EF Core translates to SQL |
| Pattern matching | Property, list, relational, and `switch` expression patterns | Concise, readable branching |
| Records & immutability | `record`, `record struct`, `with` expressions, `init`/`required` | DTOs and value semantics |
| Nullable reference types | Flow analysis, `[NotNull]`, `[MaybeNull]` attributes | Eliminating `NullReferenceException` at compile time |
| Newer C# 13/14 features | `field` keyword, extension members, null-conditional assignment, `params` collections | Less boilerplate |

#### Concurrency & async

| Topic | Key concepts | Common pitfall |
|---|---|---|
| `async`/`await` | State machines, `ConfigureAwait`, `ValueTask` | Deadlocks from `.Result` / `.Wait()` |
| `Task` patterns | `WhenAll`, `WhenAny`, cancellation tokens, `IProgress<T>` | Forgetting to propagate `CancellationToken` |
| `Channel<T>` | Producer/consumer pipelines | Unbounded channels causing memory growth |
| `IAsyncEnumerable<T>` | `await foreach`, streaming results | Buffering everything anyway |
| Parallelism | `Parallel.ForEachAsync`, PLINQ, `Interlocked`, `lock` / `SemaphoreSlim` | Shared mutable state, race conditions |

#### Performance & memory

| Topic | Key concepts | Use when |
|---|---|---|
| `Span<T>` / `Memory<T>` | Slicing without allocation | Parsing and buffers |
| `ref` struct, `ref` returns, `in` params | Stack-only types | Hot paths |
| `stackalloc`, `ArrayPool<T>` | Avoiding GC pressure | High-throughput code |
| GC internals | Generations, LOH, `IDisposable` / finalizers, `GC.SuppressFinalize` | Resource management |
| Benchmarking | BenchmarkDotNet, profilers | Measure before optimizing |
| Source generators | Compile-time code generation (e.g. `System.Text.Json`, `LoggerMessage`) | Removing reflection cost |

#### Architecture & design

| Topic | Key concepts |
|---|---|
| Dependency injection | Lifetimes (transient/scoped/singleton), keyed services, captive dependency problem |
| Design patterns | Repository, Unit of Work, Mediator, Strategy, Decorator, Options pattern |
| Clean/Onion/Vertical Slice architecture | Layering and project boundaries |
| Reflection & attributes | Custom attributes, `Activator`, expression-compiled delegates |
| Interop & unsafe | P/Invoke, `unsafe`, `fixed`, `Marshal` |


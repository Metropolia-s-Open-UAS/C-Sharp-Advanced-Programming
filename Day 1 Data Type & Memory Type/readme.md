# C# Data Types and Memory Types

## 1. Built-in value types

| C# keyword | .NET type | Size | Range / notes |
|---|---|---|---|
| `bool` | `System.Boolean` | 1 byte | `true` / `false` |
| `byte` / `sbyte` | `Byte` / `SByte` | 1 byte | 0 to 255 / -128 to 127 |
| `short` / `ushort` | `Int16` / `UInt16` | 2 bytes | -32,768 to 32,767 / 0 to 65,535 |
| `int` / `uint` | `Int32` / `UInt32` | 4 bytes | ±2.1 billion / 0 to 4.29 billion |
| `long` / `ulong` | `Int64` / `UInt64` | 8 bytes | ±9.2×10¹⁸ / 0 to 1.8×10¹⁹ |
| `nint` / `nuint` | `IntPtr` / `UIntPtr` | 4 or 8 bytes | Platform-dependent (32/64-bit) |
| `float` | `Single` | 4 bytes | ~6-9 digits precision |
| `double` | `Double` | 8 bytes | ~15-17 digits precision |
| `decimal` | `Decimal` | 16 bytes | 28-29 digits, base-10, for money |
| `char` | `Char` | 2 bytes | UTF-16 code unit |
| `Half` | `System.Half` | 2 bytes | 16-bit float |
| `Int128` / `UInt128` | `Int128` / `UInt128` | 16 bytes | Large integers (.NET 7+) |

## 2. Built-in reference types

| Type | Notes |
|---|---|
| `string` | Immutable, UTF-16, interned literals |
| `object` | Root of all types |
| `dynamic` | Resolved at runtime (DLR), bypasses compile-time checks |
| Arrays (`int[]`) | Always reference types, even when the elements are value types |
| `class`, `interface`, `delegate`, `record` | User-defined reference types |

## 3. User-defined and special types

| Type | Category | Key points |
|---|---|---|
| `struct` | Value | Copied on assignment, no inheritance (interfaces only) |
| `readonly struct` | Value | Immutable, enables compiler optimizations (no defensive copies) |
| `record struct` | Value | Value equality plus `with` expressions |
| `ref struct` | Value (stack-only) | Cannot be boxed, cannot be a field of a class, cannot be used in `async` methods. Example: `Span<T>` |
| `enum` | Value | Backed by an integral type (default `int`) |
| `Nullable<T>` (`int?`) | Value | Wrapper with `HasValue` and `Value` |
| `ValueTuple` `(int, string)` | Value | Lightweight, mutable tuple |
| `Tuple<...>` | Reference | Older, heap-allocated tuple |
| Pointers (`int*`) | Unmanaged | Require `unsafe` context |
| Function pointers (`delegate*<int,int>`) | Unmanaged | Faster than delegates, `unsafe` |

## 4. Value type vs reference type

| Aspect | Value type | Reference type |
|---|---|---|
| Stored | Inline: stack, or inside the containing object | Heap (variable holds a reference) |
| Assignment | Copies the data | Copies the reference |
| Default value | `0` / `false` / zeroed struct | `null` |
| Equality (default) | Compares contents | Compares reference (unless overridden) |
| GC cost | None when on the stack | Allocation plus collection |
| Can be `null` | Only via `Nullable<T>` | Yes (flagged by nullable reference types) |
| Inheritance | None (except from `ValueType`/`Enum`) | Single class inheritance |

**Common misconception:** "value types always live on the stack" is false. A value type lives on the heap when it is a field of a class, an element of an array, captured by a closure, or boxed.

## 5. Memory areas in .NET

| Memory area | Managed by | Holds | Lifetime |
|---|---|---|---|
| **CPU registers** | JIT / CPU | Hot locals, temporaries | Instruction-level |
| **Stack** | Runtime (per thread, ~1 MB default) | Local value types, references, method frames, `stackalloc` buffers | Until the method returns |
| **Small Object Heap (SOH)** | GC | Objects under 85,000 bytes | Until unreachable |
| **Large Object Heap (LOH)** | GC | Objects of 85,000 bytes or more (large arrays/strings) | Collected only in Gen 2 (rarely compacted) |
| **Pinned Object Heap (POH)** | GC | Objects pinned for native interop (`GC.AllocateArray(pinned: true)`) | Until unreachable |
| **Frozen segments / static data** | Runtime | String literals, static readonly data (.NET 8+) | App lifetime |
| **Static storage** | Runtime | `static` fields | App domain lifetime |
| **Unmanaged (native) memory** | You | `Marshal.AllocHGlobal`, `NativeMemory.Alloc`, native libraries | Until you free it |

## 6. GC generations (SOH)

| Generation | Contains | Collected | Cost |
|---|---|---|---|
| **Gen 0** | Newly allocated, short-lived objects | Very frequently | Cheapest |
| **Gen 1** | Survivors of Gen 0 (buffer) | Often | Low |
| **Gen 2** | Long-lived objects and the LOH | Rarely (full GC) | Most expensive |

## 7. Memory-related types and features

| Feature | Memory it touches | Purpose |
|---|---|---|
| `Span<T>` | Stack, heap, or native | Safe slice over contiguous memory, stack-only |
| `ReadOnlySpan<T>` | Same | Read-only view, ideal for parsing |
| `Memory<T>` / `ReadOnlyMemory<T>` | Heap | Span-like but storable on the heap, usable in `async` |
| `stackalloc` | Stack | Fast temporary buffers (keep them small) |
| `ArrayPool<T>` | Heap (reused) | Rent/return arrays to avoid GC pressure |
| `MemoryPool<T>` | Heap/native | Pooled `Memory<T>` |
| `NativeMemory` | Unmanaged | `malloc`/`free` equivalent |
| `GCHandle` | Pinned/heap | Pin objects or hold weak references |
| `WeakReference<T>` | Heap | Reference that doesn't prevent collection |
| `IDisposable` / `using` | Unmanaged resources | Deterministic cleanup |
| Finalizer (`~Class`) | Heap | Safety net for unmanaged cleanup (costly) |

## 8. Boxing and unboxing

| Operation | What happens | Cost |
|---|---|---|
| **Boxing** | Value type copied into a new heap object | Allocation plus GC pressure |
| **Unboxing** | Value copied back out, with a type check | Cast cost; throws `InvalidCastException` if the type is wrong |

```csharp
int x = 42;
object boxed = x;          // boxing: heap allocation
int y = (int)boxed;        // unboxing

// Hidden boxing examples to avoid:
string s = string.Format("{0}", x);          // boxes x
IComparable c = x;                           // boxes x
var list = new System.Collections.ArrayList(); list.Add(x); // boxes
// Fix: use generics (List<int>) or interpolation/spans
```

## 9. Where do the bytes actually live?

```csharp
class Order { public int Id; public Point Pos; }   // Point is a struct
struct Point { public int X, Y; }

void Demo()
{
    int a = 5;                  // stack
    Point p = new Point();      // stack
    Order o = new Order();      // reference on stack, object on heap
                                // o.Id and o.Pos live INSIDE the heap object
    int[] arr = new int[10];    // reference on stack, array on heap
    Span<int> s = stackalloc int[10]; // stack
}
```

| Variable | Reference/value stored in | Actual data stored in |
|---|---|---|
| `a` | Stack | Stack |
| `p` | Stack | Stack |
| `o` | Stack (reference) | Heap |
| `o.Pos` | Heap (inline in `Order`) | Heap |
| `arr` | Stack (reference) | Heap (LOH if large) |
| `s` | Stack | Stack |

## 10. Rules of thumb

| Situation | Recommendation |
|---|---|
| Small, immutable, short-lived data (under ~16 bytes) | `readonly struct` / `record struct` |
| Needs identity, inheritance, or is large | `class` |
| Parsing or slicing strings/buffers | `ReadOnlySpan<char>` / `Span<byte>` |
| Frequent large temp buffers | `ArrayPool<T>.Shared` |
| Money / exact decimals | `decimal`, never `float`/`double` |
| Native interop memory | `NativeMemory` plus `try/finally`, or `SafeHandle` |
| Suspected memory problem | Measure first with dotMemory, PerfView, or BenchmarkDotNet's `[MemoryDiagnoser]` |

I can go deeper on any of these: boxing and GC tuning, `Span<T>` and `Memory<T>` with real parsing examples, struct layout (`StructLayout`, `FieldOffset`), or unmanaged memory and `SafeHandle`. Which would be most useful?

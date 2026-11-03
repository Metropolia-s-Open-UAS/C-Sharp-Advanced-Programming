### Dapper Overview — Tabulated

#### 1. ORM vs. Micro-ORM

| Aspect | ORM (Object Relational Mapper) | Micro-ORM |
|---|---|---|
| Definition | Concept of writing queries using the object-oriented paradigm of a preferred programming language | Lightweight version of a full ORM |
| Purpose | Interact with SQL databases using preferred language instead of SQL; creates objects based on DB queries | Does mapping between database and preferred language without heavy features |
| Features | Full/heavy feature set | Minimal, saves work |
| Example | Entity Framework (ASP.NET) | Dapper ("King of Micro ORM" — speed & ease) |

#### 2. How Dapper Works

| Step | Description |
|---|---|
| 1 | Creates an `IDbConnection` object and allows writing queries for CRUD operations |
| 2 | Passes the queries as parameters through one of its various methods |

#### 3. Dapper Parameters

| Parameter | Description | Default |
|---|---|---|
| `sql` | The command to execute | — |
| `param` | The command parameters | `null` |
| `commandTimeout` | The command timeout | `null` |
| `commandType` | The command type | `null` |

#### 4. Dapper Methods

| # | Method | Called From | Behavior | Example Query |
|---|---|---|---|---|
| 1 | `Execute` | `IDbConnection` object | Executes a command one or multiple times; returns number of affected rows. Can run Stored Procedures, SELECT/INSERT/DELETE, etc. | `INSERT INTO Companies (CompanyName) Values (@CompanyName);` |
| 2 | `Query` | `IDbConnection` object | Executes a query and maps the result | `SELECT * FROM Companies` |
| 3 | `QueryFirst` | `IDbConnection` object | Executes a query and maps the **first** result | `SELECT * FROM Companies WHERE CompanyId = @CompanyId` |
| 4 | `QuerySingle` | `IDbConnection` object | Executes a query and maps the first result; **throws exception** if not exactly one element | `SELECT * FROM Companies WHERE CompanyId = @CompanyId` |

#### 5. Related "OrDefault" Variants

| Method | Behavior |
|---|---|
| `QueryFirstOrDefault` | Maps the first result, or a **default value** if the sequence contains no elements |
| `QuerySingleOrDefault` | Maps the first result, or a default value if no elements; **throws exception** if more than one element |

#### 6. Method Comparison Summary

| Method | Returns | No Elements | Multiple Elements |
|---|---|---|---|
| `Query` | All results | Empty collection | All returned |
| `QueryFirst` | First result | Throws exception | Returns first only |
| `QueryFirstOrDefault` | First result or default | Default value | Returns first only |
| `QuerySingle` | Single result | Throws exception | Throws exception |
| `QuerySingleOrDefault` | Single result or default | Default value | Throws exception |
| `Execute` | Affected row count | N/A (0 rows affected) | N/A |

Here are the most common Dapper methods. They're all extension methods on `IDbConnection`.

#### Query methods (return data)

| Method | Returns | Behavior if no rows | Behavior if multiple rows |
|---|---|---|---|
| `Query<T>` | `IEnumerable<T>` | Empty list | Returns all |
| `QueryFirst<T>` | `T` | Throws exception | Returns the first |
| `QueryFirstOrDefault<T>` | `T` | Returns `default` (null) | Returns the first |
| `QuerySingle<T>` | `T` | Throws exception | Throws exception |
| `QuerySingleOrDefault<T>` | `T` | Returns `default` (null) | Throws exception |
| `QueryMultiple` | `GridReader` | n/a | Reads several result sets from one call |

#### Execute methods (no mapped rows)

| Method | Returns | Typical use |
|---|---|---|
| `Execute` | `int` (rows affected) | `INSERT`, `UPDATE`, `DELETE`, stored procedures |
| `ExecuteScalar<T>` | `T` (first column of first row) | `COUNT(*)`, `SUM()`, getting a new ID |
| `ExecuteReader` | `IDataReader` | Streaming large results or custom reading |

#### Async versions

| Sync | Async |
|---|---|
| `Query<T>` | `QueryAsync<T>` |
| `QueryFirstOrDefault<T>` | `QueryFirstOrDefaultAsync<T>` |
| `QuerySingleOrDefault<T>` | `QuerySingleOrDefaultAsync<T>` |
| `QueryMultiple` | `QueryMultipleAsync` |
| `Execute` | `ExecuteAsync` |
| `ExecuteScalar<T>` | `ExecuteScalarAsync<T>` |

In web apps, prefer the async versions so threads aren't blocked while waiting on the database.

#### Example

```csharp
using Dapper;
using System.Data.SqlClient;

using (var connection = new SqlConnection(connectionString))
{
    // Query: list of rows
    var products = connection.Query<Product>(
        "SELECT * FROM Products WHERE Price > @MinPrice",
        new { MinPrice = 100 }).ToList();

    // QueryFirstOrDefault: one row or null
    var product = connection.QueryFirstOrDefault<Product>(
        "SELECT * FROM Products WHERE Id = @Id",
        new { Id = 1 });

    // QuerySingle: exactly one row expected
    var user = connection.QuerySingle<User>(
        "SELECT * FROM Users WHERE Email = @Email",
        new { Email = "a@b.com" });

    // Execute: returns rows affected
    int affected = connection.Execute(
        "UPDATE Products SET Price = @Price WHERE Id = @Id",
        new { Price = 59.99m, Id = 1 });

    // ExecuteScalar: single value
    int count = connection.ExecuteScalar<int>(
        "SELECT COUNT(*) FROM Products");

    // Insert and get the new ID (SQL Server)
    int newId = connection.ExecuteScalar<int>(
        "INSERT INTO Products (Name, Price) VALUES (@Name, @Price); " +
        "SELECT CAST(SCOPE_IDENTITY() AS INT)",
        new { Name = "Stethoscope", Price = 49.99m });

    // QueryMultiple: two result sets in one round trip
    using (var multi = connection.QueryMultiple(
        "SELECT * FROM Products; SELECT * FROM Categories"))
    {
        var allProducts = multi.Read<Product>().ToList();
        var categories = multi.Read<Category>().ToList();
    }

    // Async version
    var asyncProducts = await connection.QueryAsync<Product>(
        "SELECT * FROM Products");
}
```

#### Choosing the right method

| Situation | Use |
|---|---|
| Need a list | `Query<T>` |
| Lookup by ID, might not exist | `QueryFirstOrDefault<T>` |
| Must be unique, error if not | `QuerySingle<T>` / `QuerySingleOrDefault<T>` |
| Write operation | `Execute` |
| Need one value (count, new ID) | `ExecuteScalar<T>` |
| Multiple SELECTs in one call | `QueryMultiple` |


#### The parameters

| Parameter | Type | Default | Purpose |
|---|---|---|---|
| `sql` | `string` | required | The SQL text, or a stored procedure name, to run. |
| `param` | `object` | `null` | Values for the placeholders in the SQL (`@Name`). Can be an anonymous object, a class instance, or `DynamicParameters`. |
| `commandTimeout` | `int?` | `null` | Seconds to wait before the command times out. `null` uses the provider default (30 seconds for SQL Server). |
| `commandType` | `CommandType?` | `null` | How to interpret `sql`: `Text`, `StoredProcedure`, or `TableDirect`. `null` means `Text`. |

Note that the full method signatures also include `transaction` (an `IDbTransaction` for running inside a transaction) and, for `Query`, `buffered`. Those aren't in your list but you will see them often.

#### What each one looks like

| Parameter | Example |
|---|---|
| `sql` | `"SELECT * FROM Products WHERE Price > @MinPrice"` |
| `param` | `new { MinPrice = 100 }` |
| `commandTimeout` | `commandTimeout: 60` |
| `commandType` | `commandType: CommandType.StoredProcedure` |

#### Full example

```csharp
using Dapper;
using System.Data;
using System.Data.SqlClient;

using (var connection = new SqlConnection(connectionString))
{
    // Plain SQL with parameters and a custom timeout
    var products = connection.Query<Product>(
        sql: "SELECT * FROM Products WHERE Price > @MinPrice",
        param: new { MinPrice = 100 },
        commandTimeout: 60
    ).ToList();

    // Stored procedure
    var orders = connection.Query<Order>(
        sql: "GetOrdersByCustomer",
        param: new { CustomerId = 5 },
        commandType: CommandType.StoredProcedure
    ).ToList();
}
```

#### Key points

| Tip | Why it matters |
|---|---|
| Always pass values through `param` | Parameterized queries protect against SQL injection. Never concatenate user input into `sql`. |
| Use `commandType` for stored procedures | Then `sql` is just the procedure name, with no `EXEC` needed. |
| Raise `commandTimeout` for heavy reports | Long-running queries fail at the default 30 seconds otherwise. |
| Use `DynamicParameters` for output values | Needed when a stored procedure returns output or return parameters. |

If you'd like, I can go through the common Dapper methods next (`Query`, `QueryFirst`, `Execute`, `QueryMultiple`) in the same table format.

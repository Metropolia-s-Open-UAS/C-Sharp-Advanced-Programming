| Concept | What it means |
|---|---|
| **ORM** | Lets you query the database using objects in your language (C#) instead of writing raw SQL. Example: Entity Framework. |
| **Micro-ORM** | A lightweight ORM. It still maps database rows to objects, but skips heavy features like change tracking and migrations. |
| **Dapper** | A popular micro-ORM for .NET, known for speed and simplicity. |

#### How Dapper works

| Step | What happens |
|---|---|
| 1 | You create an `IDbConnection` (for example `SqlConnection`). |
| 2 | You call Dapper's extension methods on it (`Query`, `Execute`, etc.) and pass your SQL and parameters. |
| 3 | Dapper runs the query and maps the results to your C# objects. |

#### Dapper vs. a full ORM (Entity Framework)

| Aspect | Dapper | Entity Framework |
|---|---|---|
| Speed | Very fast, close to raw ADO.NET | Slower, more overhead |
| SQL | You write it yourself | Generated from LINQ |
| Features | Minimal (mapping only) | Change tracking, migrations, relationships |
| Learning curve | Low, if you know SQL | Higher |
| Best for | Performance-critical or SQL-heavy apps | Rapid development with complex models |

#### Minimal example

```csharp
using Dapper;
using System.Data.SqlClient;

public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}

using (var connection = new SqlConnection(connectionString))
{
    // READ
    var products = connection.Query<Product>(
        "SELECT * FROM Products WHERE Price > @MinPrice",
        new { MinPrice = 100 }).ToList();

    // CREATE
    connection.Execute(
        "INSERT INTO Products (Name, Price) VALUES (@Name, @Price)",
        new { Name = "Stethoscope", Price = 49.99m });
}
```

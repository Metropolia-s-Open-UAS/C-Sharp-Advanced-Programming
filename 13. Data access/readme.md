#### Data Access in C#

| Approach | What it is | Best for | Trade-offs |
|---|---|---|---|
| **ADO.NET** (`SqlConnection`, `SqlCommand`) | Low-level, built-in API | Full control, max performance, bulk operations | Verbose, manual mapping |
| **Dapper** | Micro-ORM over ADO.NET | Fast raw SQL with automatic object mapping | No change tracking, you write the SQL |
| **Entity Framework Core** | Full ORM (LINQ to SQL, migrations, change tracking) | Most business apps, rapid development | Overhead, can generate inefficient queries if misused |
| **Stored procedures** (via any of the above) | Logic lives in the database | Legacy systems, DBA-controlled logic | Harder to version and test |

#### Quick examples

| Approach | Code |
|---|---|
| **ADO.NET** | `using var conn = new SqlConnection(cs);`<br>`await conn.OpenAsync();`<br>`using var cmd = new SqlCommand("SELECT Id, Name FROM Products WHERE Id = @id", conn);`<br>`cmd.Parameters.AddWithValue("@id", 1);`<br>`using var reader = await cmd.ExecuteReaderAsync();`<br>`while (await reader.ReadAsync()) Console.WriteLine(reader.GetString(1));` |
| **Dapper** | `using var conn = new SqlConnection(cs);`<br>`var product = await conn.QueryFirstOrDefaultAsync<Product>("SELECT * FROM Products WHERE Id = @id", new { id = 1 });` |
| **EF Core** | `var product = await db.Products.FirstOrDefaultAsync(p => p.Id == 1);` |

#### EF Core setup

| Step | Code / command |
|---|---|
| Install packages | `dotnet add package Microsoft.EntityFrameworkCore.SqlServer`<br>`dotnet add package Microsoft.EntityFrameworkCore.Design` |
| Define entity | `public class Product { public int Id { get; set; } public string Name { get; set; } = ""; }` |
| Define context | `public class AppDbContext : DbContext {`<br>`  public AppDbContext(DbContextOptions<AppDbContext> o) : base(o) {}`<br>`  public DbSet<Product> Products => Set<Product>();`<br>`}` |
| Register (ASP.NET Core) | `builder.Services.AddDbContext<AppDbContext>(o => o.UseSqlServer(builder.Configuration.GetConnectionString("Default")));` |
| Create and apply migration | `dotnet ef migrations add Init`<br>`dotnet ef database update` |

#### Best practices

| Practice | Why |
|---|---|
| Always use parameterized queries | Prevents SQL injection |
| Use `async`/`await` | Doesn't block threads on I/O |
| Wrap connections and contexts in `using` or use DI | Releases resources and pools connections |
| Use `AsNoTracking()` for read-only EF queries | Faster, less memory |
| Keep data access behind a repository or service layer | Easier testing and swapping |
| Store connection strings in config or secrets, not code | Security |

#### Which to pick?

| Situation | Choice |
|---|---|
| Typical CRUD or business app | EF Core |
| Performance-critical queries or reporting | Dapper |
| Mix of both | EF Core for writes, Dapper for heavy reads |
| Bulk inserts, special DB features | ADO.NET |

> Quick note: these four sit at different levels. ADO.NET, Dapper, and EF are data access technologies, while LINQ is a query language feature built into C# that EF (and others) use.

| Aspect | ADO.NET | Dapper | Entity Framework (EF Core) | LINQ |
|---|---|---|---|---|
| **What it is** | Low-level data access library in .NET | Micro ORM built on top of ADO.NET | Full ORM built on top of ADO.NET | Query syntax in C# for any data source |
| **Category** | Data provider | Micro ORM | Full ORM | Language feature, not a data access tool by itself |
| **Writes SQL for you** | No | No | Yes | Not alone; EF translates LINQ to SQL |
| **You write** | SQL, connections, commands, readers | SQL, with parameters as objects | C# LINQ queries | C# queries over collections or providers |
| **Object mapping** | Manual (`SqlDataReader` loops) | Automatic from query results | Automatic, full entity mapping | N/A (works on objects) |
| **Relationships** | Manual | Manual (multi-mapping) | Built in (navigation properties, Include) | N/A |
| **Change tracking** | None | None | Yes | N/A |
| **Migrations** | None | None | Yes | N/A |
| **Performance** | Fastest (baseline) | Near ADO.NET | Slower, though much improved in EF Core | Depends on the provider |
| **Boilerplate** | Very high | Low | Lowest for CRUD | Low |
| **Learning curve** | Medium | Low | Medium to high | Low to medium |
| **Control over SQL** | Total | Total | Limited (can use raw SQL when needed) | Depends on the provider |
| **Best for** | Maximum control, legacy code, bulk operations | Fast, simple, performance-focused queries | Rapid development, CRUD apps, complex domain models | Querying in-memory collections, XML, or as EF's query layer |

#### How they relate

| Layer | Technology |
|---|---|
| Your C# code | LINQ queries (optional) |
| ORM | EF Core or Dapper |
| Foundation | ADO.NET (`SqlConnection`, `SqlCommand`, `SqlDataReader`) |
| Database | SQL Server, PostgreSQL, MySQL, etc. |

EF Core and Dapper both use ADO.NET underneath. LINQ is what you type when using EF; EF translates it into SQL.

#### LINQ flavors

| Type | Works on |
|---|---|
| LINQ to Objects | In-memory collections (`List<T>`, arrays) |
| LINQ to Entities (EF) | Database via EF, translated to SQL |
| LINQ to XML | XML documents |

#### Same task in each

| Technology | Example |
|---|---|
| **ADO.NET** | `var cmd = new SqlCommand("SELECT * FROM Users WHERE Age > @age", conn); cmd.Parameters.AddWithValue("@age", 18); var reader = cmd.ExecuteReader(); while (reader.Read()) { /* map each column manually */ }` |
| **Dapper** | `var users = conn.Query<User>("SELECT * FROM Users WHERE Age > @age", new { age = 18 });` |
| **EF Core + LINQ** | `var users = context.Users.Where(u => u.Age > 18).ToList();` |
| **LINQ to Objects** | `var adults = userList.Where(u => u.Age > 18).ToList();` |

#### Which to pick

| Need | Choice |
|---|---|
| Fast development and a rich domain model | EF Core |
| Hot paths, reports, hand-tuned SQL | Dapper |
| Bulk inserts, streaming, or maximum control | ADO.NET |
| Hybrid (most common in real projects) | EF Core for CRUD plus Dapper for heavy queries |

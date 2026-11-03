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


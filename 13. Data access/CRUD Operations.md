#### CRUD, Query Language, and ORM Benefits in C# (Entity Framework Core)

| Topic | Meaning | In EF Core |
|---|---|---|
| **CRUD** | Create, Read, Update, Delete. The base of all database interaction. | `Add`, LINQ queries, change tracking, `Remove`, then `SaveChangesAsync()` |
| **Query language** | An object-oriented way to query (HQL in Hibernate/Java) | **LINQ** (and `FromSqlInterpolated` for raw SQL when needed) |
| **Benefits of ORM** | Abstraction, portability, reusability, maintainability | Same model works across providers, migrations manage schema changes |

#### CRUD operations

| Operation | Code | SQL generated (roughly) |
|---|---|---|
| **Create** | `db.Products.Add(new Product { Name = "X1", Price = 1200 });`<br>`await db.SaveChangesAsync();` | `INSERT INTO Products ...` |
| **Read (one)** | `var p = await db.Products.FindAsync(1);` | `SELECT ... WHERE Id = 1` |
| **Read (list)** | `var list = await db.Products.Where(p => p.Price > 500).ToListAsync();` | `SELECT ... WHERE Price > 500` |
| **Update** | `p.Price = 99;`<br>`await db.SaveChangesAsync();` | `UPDATE Products SET Price = 99 WHERE Id = 1` |
| **Delete** | `db.Products.Remove(p);`<br>`await db.SaveChangesAsync();` | `DELETE FROM Products WHERE Id = 1` |

```csharp
public class ProductService
{
    private readonly AppDbContext _db;
    public ProductService(AppDbContext db) => _db = db;

    public async Task<Product> CreateAsync(Product product)
    {
        _db.Products.Add(product);
        await _db.SaveChangesAsync();
        return product;
    }

    public Task<List<Product>> GetAllAsync() =>
        _db.Products.AsNoTracking().ToListAsync();

    public Task<Product?> GetByIdAsync(int id) =>
        _db.Products.FindAsync(id).AsTask();

    public async Task<bool> UpdateAsync(int id, decimal newPrice)
    {
        var p = await _db.Products.FindAsync(id);
        if (p is null) return false;
        p.Price = newPrice;
        await _db.SaveChangesAsync();
        return true;
    }

    public async Task<bool> DeleteAsync(int id)
    {
        var p = await _db.Products.FindAsync(id);
        if (p is null) return false;
        _db.Products.Remove(p);
        await _db.SaveChangesAsync();
        return true;
    }
}
```

| Efficient variants (EF Core 7+) | Code |
|---|---|
| Update without loading | `await db.Products.Where(p => p.Id == 1).ExecuteUpdateAsync(s => s.SetProperty(p => p.Price, 99));` |
| Delete without loading | `await db.Products.Where(p => p.Id == 1).ExecuteDeleteAsync();` |

#### Query language

| Framework | Language | Example |
|---|---|---|
| Hibernate (Java) | HQL | `from Product p where p.price > 500` |
| **EF Core (C#)** | **LINQ** | `db.Products.Where(p => p.Price > 500)` |
| EF Core (raw SQL) | SQL | `db.Products.FromSqlInterpolated($"SELECT * FROM Products WHERE Price > {min}")` |

```csharp
var result = await db.Products
    .Where(p => p.Price > 500)
    .OrderBy(p => p.Name)
    .Select(p => new { p.Name, p.Price })
    .ToListAsync();
```

| LINQ advantage | Why |
|---|---|
| Compile-time checking | Typos in property names are caught before running |
| IntelliSense | Easier to write and refactor |
| Provider translation | Same query becomes SQL Server, PostgreSQL, or MySQL syntax |

#### Benefits of ORM

| Benefit | Meaning | EF Core example |
|---|---|---|
| **Abstraction** | Developers focus on logic, not SQL and schema details | LINQ and `SaveChanges` instead of hand-written SQL |
| **Portability** | Switch databases with few code changes | Change `UseSqlServer(...)` to `UseNpgsql(...)` or `UseSqlite(...)` |
| **Code reusability** | One codebase works across databases and projects | Same entities and `DbContext` used by API, web, and background jobs |
| **Maintenance and scalability** | Schema changes are managed, queries can be tuned without rewriting the app | Migrations, `AsNoTracking()`, indexes, compiled queries |

```csharp
// Portability: only the provider line changes
options.UseSqlServer(connectionString);   // SQL Server
options.UseNpgsql(connectionString);      // PostgreSQL
options.UseSqlite(connectionString);      // SQLite
```

| Trade-off to know | Detail |
|---|---|
| Portability is not perfect | Provider-specific features (JSON columns, full-text search) may differ |
| Abstraction can hide cost | N+1 queries and over-fetching if you do not use `Include` or projections |
| Raw SQL still has a place | Complex reports and bulk operations |

#### Significance in modern development

| Point | Detail |
|---|---|
| Combines OOP with relational databases | No manual row-to-object conversion |
| Higher productivity | Less boilerplate, faster delivery |
| Cleaner code | Data access isolated in a service or repository layer |
| Fewer errors | Parameterized queries by default, strongly typed models |


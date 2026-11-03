#### Object-Relational Mapping (ORM) in DBMS
- Object-relational mapping (ORM) is a key concept in the field of Database Management Systems (DBMS), addressing the bridge between the object-oriented programming approach and relational databases.
-  ORM is useful in data interaction simplification, code optimization, and smooth blending of applications and databases.

#### Object-Relational Database Management System (ORDBMS)
- An ORDBMS stands for an object-relational database management system that further enhances the functionalities of a relational database by incorporating object-oriented principles.
   - ORDBMS deals with complex data types, encapsulation, and inheritance.
   - It supports applications that require both relational and object-oriented abilities.
##### ORM Concepts in C# (Entity Framework Core)

| Concept | Meaning | In EF Core |
|---|---|---|
| **Entity** | A C# class mapped to a database table. Each property maps to a column, each object to a row. | A plain class exposed through `DbSet<T>` in your `DbContext` |
| **Relationship** | How entities connect to each other, mirroring foreign keys between tables. | Navigation properties plus foreign key properties |
| **Persistence** | Data survives after the application closes or restarts because it is stored in the database. | `SaveChanges()` / `SaveChangesAsync()` writes changes to the database |

#### Entities

| Aspect | Detail |
|---|---|
| Class to table | `Product` class becomes the `Products` table |
| Property to column | `Name` property becomes the `Name` column |
| Primary key | A property named `Id` or `ProductId` is detected by convention |
| Who creates the tables | EF Core, through migrations (`dotnet ef migrations add`, `dotnet ef database update`) |

```csharp
public class Product
{
    public int Id { get; set; }                 // primary key
    public string Name { get; set; } = "";      // column
    public decimal Price { get; set; }          // column
}
```

#### Relationships

| Type | Example | Database result |
|---|---|---|
| **One-to-many** | One `Category` has many `Product`s | Foreign key `CategoryId` in `Products` |
| **One-to-one** | One `User` has one `Profile` | Foreign key with a unique constraint |
| **Many-to-many** | `Student` and `Course` | Join table (EF Core creates it automatically) |

```csharp
public class Category
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
    public List<Product> Products { get; set; } = new();   // collection navigation
}

public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
    public int CategoryId { get; set; }                     // foreign key
    public Category Category { get; set; } = null!;         // reference navigation
}
```

| Loading related data | Code |
|---|---|
| Eager loading | `db.Products.Include(p => p.Category).ToListAsync()` |
| Explicit loading | `db.Entry(product).Reference(p => p.Category).LoadAsync()` |
| Lazy loading | Needs `Microsoft.EntityFrameworkCore.Proxies` and `UseLazyLoadingProxies()` |

#### Persistence

| Step | Code | What happens |
|---|---|---|
| Create | `db.Products.Add(product);` | Marked as `Added` in memory |
| Update | `product.Price = 99;` | Change tracker marks it `Modified` |
| Delete | `db.Products.Remove(product);` | Marked as `Deleted` |
| Save | `await db.SaveChangesAsync();` | EF Core runs INSERT / UPDATE / DELETE in one transaction |

```csharp
var category = new Category { Name = "Monitors" };
category.Products.Add(new Product { Name = "Patient Monitor X1", Price = 1200 });

db.Categories.Add(category);
await db.SaveChangesAsync();   // data now persists in the database, even after the app restarts
```

#### How they fit together

| Layer | Role |
|---|---|
| Entities | Define the structure (tables and columns) |
| Relationships | Define the links between tables (foreign keys, joins) |
| Persistence | Stores and retrieves the data through `DbContext` |


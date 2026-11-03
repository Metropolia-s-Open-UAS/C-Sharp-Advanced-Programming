#### Object-Relational Mapping (ORM) in DBMS
- With Object-Relational Mapping, it becomes much easier to work with an object-oriented programming language and relational database.
- Fundamentally, it acts as a translator, translating data between the database and the application without any hitch.
- ORM enables developers to work with objects in their programming language which are mapped to corresponding database entities, such as tables, views, or stored procedures.
  
| **Concept** | **Explanation** |
|------------|-----------------|
| **Object-Oriented Paradigm** | ORM focuses on OOP principles where data and behavior are encapsulated within objects. Database entities are mapped to objects, and developers interact with and manipulate data through objects in a fairly easy way. |
| **Mapping** | The main purpose of ORM is mapping objects to database tables and back. This mapping is defined through metadata, which represents the interconnections between objects and the corresponding database schemas. ORM frameworks use this metadata to generate SQL queries and manage data flow between the application and the database. |

#### Mapping Steps in ORM (Entity Framework Core)


| **Stage** | **Description** |
|-----------|-----------------|
| **Entity Mapping** | The first initialization step in the ORM process is to identify entities from the object-oriented model and match them with corresponding tables in the relational database. Developers specify objects or classes for business entities in their code. The ORM framework manages the conversion of these entities into database tables. Each feature in the class maps to a new column in the table, and instances of the class turn into rows in the table. |
| **Relationship Mapping** | Having mapped entities, the next important stage is to provide relations between them and structure the relational database schema accordingly. ORM frameworks provide functions that enable representation of relationships between entities such as one-to-one, one-to-many, and many-to-many. Relationships are transformed into foreign keys, which keep data consistent and valid even when stored in related tables. |
| **Data Type Mapping** | Data mapping is the practice of mapping data types from the object-oriented model to the database. ORM frameworks handle the conversion of data types, enabling application objects to align with database data types. It ensures cohesion and prevents data type mismatches that would result in errors. |


| Step | Meaning | In EF Core |
|---|---|---|
| **Entity mapping** | Classes map to tables, properties to columns, instances to rows | `DbSet<T>` in `DbContext`, plus conventions, annotations, or Fluent API |
| **Relationship mapping** | One-to-one, one-to-many, many-to-many become foreign keys | Navigation properties, FK properties, `HasOne` / `HasMany` |
| **Data type mapping** | .NET types are converted to database column types and back | Built-in type mappings, `HasColumnType`, value converters |

  #### 1. Entity mapping

| Object model | Database |
|---|---|
| Class `Product` | Table `Products` |
| Property `Name` | Column `Name` |
| Instance `new Product { ... }` | Row |

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
}

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> o) : base(o) { }
    public DbSet<Product> Products => Set<Product>();   // maps Product to the Products table
}
```

#### 2. Relationship mapping

| Relationship | Fluent API | Result in database |
|---|---|---|
| One-to-one | `HasOne(...).WithOne(...).HasForeignKey<T>(...)` | FK with unique index |
| One-to-many | `HasOne(...).WithMany(...).HasForeignKey(...)` | FK on the "many" side |
| Many-to-many | `HasMany(...).WithMany(...)` | Join table, created automatically |

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    // One-to-many: Category -> Products
    modelBuilder.Entity<Product>()
        .HasOne(p => p.Category)
        .WithMany(c => c.Products)
        .HasForeignKey(p => p.CategoryId)
        .OnDelete(DeleteBehavior.Restrict);

    // Many-to-many: Student <-> Course
    modelBuilder.Entity<Student>()
        .HasMany(s => s.Courses)
        .WithMany(c => c.Students);
}
```

| Foreign key benefit | Why it matters |
|---|---|
| Referential integrity | A product cannot point to a category that does not exist |
| Delete rules | `Cascade`, `Restrict`, `SetNull` control what happens to child rows |
| Valid data | Related tables stay consistent |

#### 3. Data type mapping

| C# type | SQL Server type | Notes |
|---|---|---|
| `int` | `int` | |
| `long` | `bigint` | |
| `string` | `nvarchar(max)` | Use `HasMaxLength` to limit |
| `decimal` | `decimal(18,2)` | Set precision explicitly for money |
| `bool` | `bit` | |
| `DateTime` | `datetime2` | |
| `Guid` | `uniqueidentifier` | |
| `byte[]` | `varbinary(max)` | |
| `enum` | `int` | Can store as string with a converter |

```csharp
modelBuilder.Entity<Product>(e =>
{
    e.Property(p => p.Name).HasMaxLength(100);
    e.Property(p => p.Price).HasColumnType("decimal(18,2)");
    e.Property(p => p.Status).HasConversion<string>();   // enum stored as text
});
```

| Problem | Cause | Fix |
|---|---|---|
| Truncated or rounded decimals | Default precision too small | `HasColumnType("decimal(18,2)")` |
| Unexpected `nvarchar(max)` columns | No length set | `HasMaxLength(n)` |
| Enum shows as number in database | Default enum mapping | `HasConversion<string>()` |

#### Order of the mapping process

| Order | Stage | Result |
|---|---|---|
| 1 | Entities | Tables and columns |
| 2 | Relationships | Foreign keys and join tables |
| 3 | Data types | Correct column types, no mismatches |
| 4 | Migration | `dotnet ef migrations add Init` then `dotnet ef database update` creates the schema |

Want me to put all three parts together in one complete working example (entities, DbContext, and migration)?

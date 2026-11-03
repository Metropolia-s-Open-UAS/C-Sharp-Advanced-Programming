| Aspect | Full ORM | Micro ORM |
|---|---|---|
| **Examples** | Eloquent, Hibernate, Entity Framework, Doctrine, SQLAlchemy | Dapper, PetaPoco, Massive, Sql2o, JDBI |
| **Core idea** | Maps tables to objects and manages the whole object lifecycle | Thin layer that maps query results to objects |
| **SQL writing** | Mostly generated for you via a query builder or fluent API | You write raw SQL yourself |
| **Relationships** | Built in (hasMany, belongsTo, lazy/eager loading) | Manual joins and manual mapping |
| **Change tracking** | Yes (Unit of Work, dirty checking) | No |
| **Migrations / schema tools** | Usually included | Not included |
| **Caching** | Often built in (identity map, second-level cache) | None |
| **Performance** | Slower due to overhead and generated queries | Close to raw ADO/PDO speed |
| **Learning curve** | Steeper | Very small |
| **Control over queries** | Lower (can hit N+1 and inefficient SQL) | Full |
| **Database portability** | High (abstracts dialect differences) | Lower (SQL is dialect-specific) |
| **Boilerplate** | Low for CRUD | Higher for CRUD |
| **Best for** | CRUD-heavy apps, rapid development, complex domain models | Reporting, performance-critical paths, complex or hand-tuned queries |

#### When to choose which

| Situation | Better choice |
|---|---|
| Typical web app with lots of CRUD (e.g. admin panels, CRMs) | Full ORM |
| Complex reports, heavy aggregations, large joins | Micro ORM or raw SQL |
| High-throughput, latency-sensitive endpoints | Micro ORM |
| Team wants fast development and conventions | Full ORM |
| Team has strong SQL skills and wants predictability | Micro ORM |
| Rapidly changing schema with migrations | Full ORM |

#### In a Laravel context

| Tool | Category | Notes |
|---|---|---|
| **Eloquent** | Full ORM (Active Record) | Models, relationships, events, scopes |
| **Query Builder (`DB::table`)** | Between the two | No models or relationships, but fluent SQL generation |
| **`DB::select()` with raw SQL** | Closest to micro ORM | Full control, returns plain objects |


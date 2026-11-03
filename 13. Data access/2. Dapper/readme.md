#### What is Dapper? 

| **Aspect** | **Description** |
|------------|-----------------|
| **Definition** | Dapper is an open-source object-relational mapping (ORM) library for .NET and .NET Core applications, available as a NuGet package. |
| **Purpose** | It allows developers to quickly and easily access data from databases without writing tedious code. Dapper lets you execute raw SQL queries, map results to objects, and execute stored procedures, among other things. |
| **Performance** | Dapper is lightweight and fast, making it an ideal choice for applications requiring low latency and high performance. |
| **Object Mapping** | It is a simple yet powerful object mapping tool for any .NET language (e.g., C#), enabling developers to quickly and easily map query results from ADO.NET data readers to instances of business objects. |
| **Query Support** | It has excellent support for both asynchronous and synchronous database queries, and for batching multiple queries into a single call. |
| **Security** | Dapper supports parameterized queries, which help protect against SQL injection attacks. |

| **** | **** |
|------------|-----------------|
| **Classification** | Dapper is a **micro-ORM**. Micro-ORMs perform only a subset of the functionality of full-blown ORMs such as Entity Framework Core. Dapper is known for its speed and simple implementation compared to others. |
| **Focus** | Dapper concentrates its efforts on the **O and M of ORM — Object Mapping**. |

**Capabilities: Micro ORM vs. Full ORM**

| **Capability** | **Micro ORM (Dapper)** | **Full ORM** |
|----------------|:----------------------:|:------------:|
| Map queries to objects | ✅ | ✅ |
| Caching results | ❌ | ✅ |
| Change tracking | ❌ ¹ | ✅ |
| SQL generation | ❌ ² | ✅ |
| Identity management | ❌ | ✅ |
| Association management | ❌ | ✅ |
| Lazy loading | ❌ | ✅ |
| Unit of work support | ❌ | ✅ |
| Database migrations | ❌ | ✅ |


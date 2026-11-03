#### Features of ADO.NET

| **Feature** | **Description** |
|-------------|-----------------|
| **Interoperability** | XML documents are text-based formats that can be edited using standard text-editing tools. ADO.NET uses XML for all data exchanges and for the internal representation of data. |
| **Maintainability** | ADO.NET is designed with a separation between data logic and the user interface. This allows developers to create applications in independent layers, making them easier to maintain. |
| **Programmability (Typed Programming)** | Typed programming is a style in which specific user-defined types are used to construct statements or evaluate expressions. For example, to select the "Marks" column for "Kawal" from the "Student" table: `DataSet.Student["Kawal"].Marks;` |
| **Performance** | ADO.NET uses a disconnected data architecture, which reduces the load on the database and improves scalability. Since most operations are handled on the client side, overall performance is enhanced. |
| **Scalability** | Scalability refers to the system's ability to handle an increasing number of clients without degrading performance. By using disconnected data access, applications do not retain database connections for long durations. This conserves resources and allows multiple users to access data simultaneously. |
 #### Features of ADO.NET

| Feature | Meaning | How ADO.NET delivers it |
|---|---|---|
| **Interoperability** | Data can be exchanged with other systems and platforms | XML is used for data exchange and internal data representation |
| **Maintainability** | Data logic is separated from the user interface | Layered design: data access, business logic, and UI are independent |
| **Programmability** | Typed programming with user-defined types | Strongly typed `DataSet` generated from a schema |
| **Performance** | Less load on the database | Disconnected architecture, most work done client-side |
| **Scalability** | Handles more clients without slowing down | Connections are held only briefly, then released to the pool |

#### 1. Interoperability (XML)

| Aspect | Detail |
|---|---|
| Format | Text-based, editable with any text editor |
| Standard | Readable by any platform or language |
| Use | Data exchange, saving and loading datasets, web services |

```csharp
// Save a DataSet to XML
dataSet.WriteXml("products.xml");
dataSet.WriteXmlSchema("products.xsd");

// Load it back (any system that understands XML can produce this file)
var ds = new DataSet();
ds.ReadXmlSchema("products.xsd");
ds.ReadXml("products.xml");
```

| Note | Detail |
|---|---|
| Modern practice | JSON is now more common for web APIs, but `DataSet` XML support is still built in |
| Wire format | Today ADO.NET sends data over the database's own protocol (such as TDS for SQL Server), and XML is mainly used for persistence and exchange |

#### 2. Maintainability

| Layer | Responsibility |
|---|---|
| Presentation (UI) | Forms, pages, controls |
| Business logic | Rules and validation |
| Data access | `SqlConnection`, `SqlCommand`, `SqlDataAdapter` |

| Benefit | Why |
|---|---|
| Change the database code without touching the UI | Layers are independent |
| Easier testing | Data layer can be mocked |
| Team work | Different people work on different layers |

#### 3. Programmability (typed programming)

| Style | Example | Risk |
|---|---|---|
| **Untyped** | `ds.Tables["Student"].Rows[0]["Marks"]` | Typo in the name only fails at runtime, needs casting |
| **Typed** | `studentDataSet.Student.FindByName("Kawal").Marks` | Compile-time checking, IntelliSense, correct type |

```csharp
// Untyped DataSet
int marks = (int)ds.Tables["Student"].Rows[0]["Marks"];

// Typed DataSet (generated class from an .xsd schema)
int marks = studentDs.Student.FindByName("Kawal").Marks;
```

| Typed DataSet benefit | Detail |
|---|---|
| Compile-time errors | Wrong column names are caught before running |
| IntelliSense | Tables and columns appear as properties |
| No casting | Values already have the right .NET type |

The `DataSet.Student["Kawal"].Marks` form in the text is conceptual. In real code you use a generated method such as `FindByName`.

#### 4. Performance

| Factor | Effect |
|---|---|
| Disconnected architecture | Database is contacted only to fill and update |
| Client-side processing | Sorting, filtering, and navigating run in memory |
| Connection pooling | Reuses open connections instead of creating new ones |
| `DataReader` | Fast, forward-only reads when you do not need caching |

```csharp
// Filter and sort in memory, no database round trip
var rows = table.Select("Price > 500", "Name ASC");
var view = new DataView(table) { RowFilter = "Price > 500", Sort = "Name" };
```

#### 5. Scalability

| Approach | Connection held | Effect with many users |
|---|---|---|
| Connected, long-lived | Entire session | Connections run out, performance drops |
| **Disconnected** | Only during fill and update | Many users share a small pool of connections |

| Practice | Why |
|---|---|
| Open late, close early | Frees the connection for others |
| Use `using` blocks | Guarantees the connection returns to the pool |
| Keep `DataSet`s small | Avoids heavy memory use per user |

#### Trade-offs

| Disconnected strength | Cost |
|---|---|
| Less database load | Data can become stale |
| Offline editing | Concurrency conflicts when saving (`DBConcurrencyException`) |
| Fewer open connections | More memory used on the client or server |


#### Introduction to ADO.NET

| **Aspect** | **Description** |
|------------|-----------------|
| **Definition** | ADO.NET is the .NET Framework's own data access technology and the latest implementation of Microsoft's Universal Data Access strategy. It consists of managed classes that allow .NET applications to connect to data sources (e.g., Microsoft SQL Server, Microsoft Access, Oracle, XML), execute commands, and manage disconnected data. |
| **Evolution** | ADO.NET is an improvement over ADO. It was first introduced in version 1.0 of the .NET Framework and offers a wide range of features for handling data in different modes. |
| **Data Modes** | **Connected mode:** deals with live data; the application queries the database each time data is needed, without caching. **Disconnected mode:** data is accessed from a stored source (cached in runtime memory). |
| **Primary Purpose** | ADO.NET was developed to address two common ways of working with data: (1) accessing data once and iterating through it in a single instance (caching data in runtime memory), and (2) working with data in a connected manner where data is not cached and the application always queries the database to retrieve it. |

| Topic | Meaning | Key classes |
|---|---|---|
| **ADO.NET** | Microsoft's built-in data access technology in .NET: managed classes to connect to data sources, run commands, and manage disconnected data | `SqlConnection`, `SqlCommand`, `SqlDataReader`, `SqlDataAdapter`, `DataSet` |
| **Data sources** | SQL Server, Oracle, MS Access, XML, and others | Chosen via a data provider |
| **Origin** | Improvement over classic ADO, introduced in .NET Framework 1.0 | Part of Microsoft's Universal Data Access strategy |

#### Two ways of working with data

| Mode | Idea | Data state | Main classes |
|---|---|---|---|
| **Connected** | Always query the database, no caching. You work with live data. | Stays in the database; read row by row | `SqlConnection`, `SqlCommand`, `SqlDataReader` |
| **Disconnected** | Fetch data once, cache it in memory, then work with it offline. | Held in memory, reconnect only to save changes | `SqlDataAdapter`, `DataSet`, `DataTable` |

| Aspect | Connected | Disconnected |
|---|---|---|
| Connection | Open while reading | Open only briefly (fill and update) |
| Memory use | Low (forward-only stream) | Higher (data cached in memory) |
| Navigation | Forward-only, read-only | Any direction, can edit offline |
| Data freshness | Always current | May become stale |
| Best for | Large result sets, quick reads | Small datasets, offline edits, binding to UI |

#### Connected mode example

```csharp
using var conn = new SqlConnection(connectionString);
await conn.OpenAsync();

using var cmd = new SqlCommand("SELECT Id, Name, Price FROM Products", conn);
using var reader = await cmd.ExecuteReaderAsync();

while (await reader.ReadAsync())
{
    Console.WriteLine($"{reader.GetInt32(0)} - {reader.GetString(1)} - {reader.GetDecimal(2)}");
}
```

#### Disconnected mode example

```csharp
var table = new DataTable();

using var conn = new SqlConnection(connectionString);
using var adapter = new SqlDataAdapter("SELECT Id, Name, Price FROM Products", conn);

adapter.Fill(table);   // opens, fills, closes the connection automatically

// Work offline
table.Rows[0]["Price"] = 99;

// Push changes back
var builder = new SqlCommandBuilder(adapter);
adapter.Update(table);
```

#### ADO.NET components

| Component | Role |
|---|---|
| **Data provider** | Set of classes for a specific database (`Microsoft.Data.SqlClient`, `Npgsql`, `MySqlConnector`) |
| **Connection** | Opens the link to the database |
| **Command** | Executes SQL or stored procedures |
| **DataReader** | Fast, forward-only, read-only stream (connected) |
| **DataAdapter** | Bridge between the database and a `DataSet` (disconnected) |
| **DataSet / DataTable** | In-memory copy of data, including tables and relations |

#### Command execution methods

| Method | Returns | Use for |
|---|---|---|
| `ExecuteReader()` | `SqlDataReader` | SELECT with multiple rows |
| `ExecuteScalar()` | Single value | `COUNT`, `SUM`, one cell |
| `ExecuteNonQuery()` | Rows affected | INSERT, UPDATE, DELETE |

#### Practical notes

| Note | Detail |
|---|---|
| Use `Microsoft.Data.SqlClient` | It replaces the older `System.Data.SqlClient` for SQL Server, including on .NET Core / .NET 5+ |
| Always parameterize | `cmd.Parameters.Add("@id", SqlDbType.Int).Value = 1;` prevents SQL injection |
| Dispose connections | `using` returns them to the connection pool |
| ADO.NET sits under EF Core and Dapper | They both build on it, so knowing ADO.NET helps with debugging and performance tuning |


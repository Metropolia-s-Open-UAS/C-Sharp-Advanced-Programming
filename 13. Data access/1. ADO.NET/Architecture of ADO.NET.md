#### Architecture of ADO.NET

| **Aspect** | **Description** |
|------------|-----------------|
| **Multilayered Architecture** | ADO.NET uses a multilayered architecture built around a few key concepts: **Connection**, **Command**, and **DataSet** objects. |
| **Difference from ADO** | The ADO.NET architecture differs slightly from ADO (illustrated in a figure not included here). |
| **Data Source Handling** | A key difference between ADO and ADO.NET is how they handle various data sources. In ADO.NET, programmers use a generic set of objects regardless of the underlying data source. For example, retrieving a record from an Oracle database uses the same connection class as with SQL Server. |
| **Flexibility Mechanism** | ADO.NET achieves this flexibility through a **data provider model** combined with the **DataSet**. |

##### ADO.NET Architecture

| Core concept | Role | Provider-specific classes |
|---|---|---|
| **Connection** | Opens the link to the data source | `SqlConnection`, `OracleConnection`, `NpgsqlConnection` |
| **Command** | Runs SQL or stored procedures against the connection | `SqlCommand`, `OracleCommand`, `NpgsqlCommand` |
| **DataSet** | In-memory, disconnected copy of data (tables, relations, constraints) | Provider-independent |

#### Two layers of the architecture

| Layer | Contains | Depends on the data source? |
|---|---|---|
| **Data provider (connected layer)** | Connection, Command, DataReader, DataAdapter | Yes, one provider per database type |
| **DataSet (disconnected layer)** | `DataSet`, `DataTable`, `DataRow`, `DataRelation` | No, works the same for any source |
| **DataAdapter (the bridge)** | Fills the `DataSet` from the database and pushes changes back | Provider-specific, but exposes the same pattern |

#### ADO vs ADO.NET

| Aspect | ADO | ADO.NET |
|---|---|---|
| Data model | Recordset | `DataSet` / `DataTable` and `DataReader` |
| Working mode | Mostly connected | Connected and disconnected |
| Data source handling | Tied to OLE DB, one generic Recordset object | Data provider model, with a common set of interfaces |
| XML support | Limited | Built in (`DataSet.WriteXml`, `ReadXml`) |
| Platform | COM-based | Managed .NET classes |

#### Data provider model

| Provider | Namespace / package | Data source |
|---|---|---|
| SQL Server | `Microsoft.Data.SqlClient` | SQL Server, Azure SQL |
| Oracle | `Oracle.ManagedDataAccess.Core` | Oracle |
| PostgreSQL | `Npgsql` | PostgreSQL |
| MySQL | `MySqlConnector` | MySQL / MariaDB |
| SQLite | `Microsoft.Data.Sqlite` | SQLite |
| OLE DB / ODBC | `System.Data.OleDb`, `System.Data.Odbc` | Access, Excel, other legacy sources |

Every provider implements the same base types from `System.Data.Common`:

| Abstract base class | Interface | Implemented by |
|---|---|---|
| `DbConnection` | `IDbConnection` | `SqlConnection`, `OracleConnection`, ... |
| `DbCommand` | `IDbCommand` | `SqlCommand`, `OracleCommand`, ... |
| `DbDataReader` | `IDataReader` | `SqlDataReader`, `OracleDataReader`, ... |
| `DbDataAdapter` | `IDbDataAdapter` | `SqlDataAdapter`, `OracleDataAdapter`, ... |

#### Same code, different database

```csharp
// SQL Server
using var conn = new SqlConnection(sqlServerCs);
using var cmd = new SqlCommand("SELECT Id, Name FROM Products", conn);

// Oracle: same pattern, different classes
using var conn2 = new OracleConnection(oracleCs);
using var cmd2 = new OracleCommand("SELECT Id, Name FROM Products", conn2);
```

#### Writing provider-independent code

```csharp
static async Task PrintProductsAsync(DbConnection conn)
{
    await conn.OpenAsync();

    using var cmd = conn.CreateCommand();
    cmd.CommandText = "SELECT Id, Name FROM Products";

    using var reader = await cmd.ExecuteReaderAsync();
    while (await reader.ReadAsync())
        Console.WriteLine($"{reader.GetInt32(0)} - {reader.GetString(1)}");
}

// Works with any provider
await PrintProductsAsync(new SqlConnection(sqlServerCs));
await PrintProductsAsync(new NpgsqlConnection(postgresCs));
```

| Alternative | Code |
|---|---|
| Provider factory | `DbProviderFactory factory = SqlClientFactory.Instance;`<br>`using var conn = factory.CreateConnection();` |

#### How the flow works

| Step | What happens | Object |
|---|---|---|
| 1 | Open a connection to the source | `Connection` |
| 2 | Define the SQL or stored procedure | `Command` |
| 3a | Read rows forward-only (connected) | `DataReader` |
| 3b | Fill an in-memory copy (disconnected) | `DataAdapter` then `DataSet` |
| 4 | Edit data offline and push changes back | `DataAdapter.Update()` |

#### Practical notes

| Note | Detail |
|---|---|
| The classes are generic in pattern, not in type | You still instantiate a provider-specific class, so the SQL dialect and provider features may differ |
| `DataSet` is the part that is truly source-independent | It has no knowledge of where the data came from |
| Provider-independent code has limits | Parameter syntax (`@id` vs `:id`) and data types vary by database |


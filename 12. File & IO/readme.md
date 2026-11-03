#### File Handling in C#

#### 1. Key Namespace and Classes

| Class/Namespace | Purpose |
|---|---|
| `System.IO` | Namespace containing all file/directory I/O classes — needs `using System.IO;` |
| `File` | Static class for simple, one-off file operations (read/write/copy/delete) |
| `Directory` | Static class for folder operations (create/delete/list) |
| `StreamReader` / `StreamWriter` | Read/write text files line-by-line or in chunks — more control, better for large files |
| `FileStream` | Low-level byte-based read/write access |
| `Path` | Utility for manipulating file paths (combine, extension, filename) |

#### 2. Quick File Operations (Static `File` Class)

| Operation | Syntax | Notes |
|---|---|---|
| Read entire file as text | `string text = File.ReadAllText("data.txt");` | Loads whole file into memory at once |
| Read all lines into array | `string[] lines = File.ReadAllLines("data.txt");` | Each line becomes an array element |
| Write text (overwrite) | `File.WriteAllText("data.txt", "Hello");` | Creates file if it doesn't exist, overwrites if it does |
| Write lines (overwrite) | `File.WriteAllLines("data.txt", stringArray);` | Writes an array/list, one line per element |
| Append text | `File.AppendAllText("data.txt", "More text");` | Adds to end without overwriting existing content |
| Append lines | `File.AppendAllLines("data.txt", stringArray);` | Adds multiple lines to end |
| Check existence | `bool exists = File.Exists("data.txt");` | Returns `true`/`false`, doesn't throw |
| Copy | `File.Copy("a.txt", "b.txt");` | Add `true` as 3rd arg to overwrite destination |
| Move / rename | `File.Move("a.txt", "b.txt");` | Also used for renaming |
| Delete | `File.Delete("data.txt");` | No error if... actually throws if file doesn't exist in some cases — check `Exists` first |

#### 3. Reading with `StreamReader`

| Feature | Syntax | Notes |
|---|---|---|
| Basic setup | `StreamReader sr = new StreamReader("data.txt");` | Opens file for reading |
| Read one line | `string line = sr.ReadLine();` | Returns `null` at end of file |
| Read entire file | `string all = sr.ReadToEnd();` | Reads everything remaining |
| Loop through lines | `while ((line = sr.ReadLine()) != null) { ... }` | Common pattern for line-by-line processing |
| Close | `sr.Close();` | Important — releases the file handle |
| Auto-close (recommended) | `using (StreamReader sr = new StreamReader("data.txt")) { ... }` | Automatically closes/disposes even if an exception occurs |

```csharp
using (StreamReader sr = new StreamReader("data.txt"))
{
    string line;
    while ((line = sr.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

#### 4. Writing with `StreamWriter`

| Feature | Syntax | Notes |
|---|---|---|
| Overwrite mode | `new StreamWriter("data.txt")` | Default — erases existing content |
| Append mode | `new StreamWriter("data.txt", true)` | Second parameter `true` = append instead of overwrite |
| Write line | `sw.WriteLine("Hello");` | Adds a newline after |
| Write (no newline) | `sw.Write("Hello");` | No newline added |
| Close | `sw.Close();` | Flushes buffer and releases handle |
| Auto-close (recommended) | `using (StreamWriter sw = new StreamWriter("data.txt")) { ... }` | Same benefit as `StreamReader` |

```csharp
using (StreamWriter sw = new StreamWriter("data.txt"))
{
    sw.WriteLine("Line 1");
    sw.WriteLine("Line 2");
}
```

#### 5. `Path` Utility Methods

| Method | Example | Result |
|---|---|---|
| `Path.Combine` | `Path.Combine("folder", "file.txt")` | `"folder\file.txt"` (OS-correct separator) |
| `Path.GetFileName` | `Path.GetFileName(@"C:\data\file.txt")` | `"file.txt"` |
| `Path.GetExtension` | `Path.GetExtension("file.txt")` | `".txt"` |
| `Path.GetFileNameWithoutExtension` | `Path.GetFileNameWithoutExtension("file.txt")` | `"file"` |
| `Path.GetDirectoryName` | `Path.GetDirectoryName(@"C:\data\file.txt")` | `@"C:\data"` |

#### 6. Directory Operations

| Operation | Syntax |
|---|---|
| Create folder | `Directory.CreateDirectory("myFolder");` |
| Check existence | `Directory.Exists("myFolder");` |
| Delete folder | `Directory.Delete("myFolder");` (add `true` to delete non-empty) |
| List files | `string[] files = Directory.GetFiles("myFolder");` |
| List subfolders | `string[] dirs = Directory.GetDirectories("myFolder");` |

#### 7. Exception Handling (Important for File I/O)

| Exception | When it's thrown |
|---|---|
| `FileNotFoundException` | Trying to read a file that doesn't exist |
| `UnauthorizedAccessException` | No permission to access the file/folder |
| `IOException` | File is in use by another process, disk full, etc. |
| `DirectoryNotFoundException` | The specified folder path doesn't exist |

```csharp
try
{
    string content = File.ReadAllText("data.txt");
    Console.WriteLine(content);
}
catch (FileNotFoundException)
{
    Console.WriteLine("File not found!");
}
catch (IOException ex)
{
    Console.WriteLine("Error reading file: " + ex.Message);
}
```

#### 8. Quick Decision Guide

| Situation | Recommended Approach |
|---|---|
| Small file, just need contents once | `File.ReadAllText` / `File.ReadAllLines` |
| Large file, process line-by-line to save memory | `StreamReader` with `using` |
| Writing a small amount of data quickly | `File.WriteAllText` / `File.WriteAllLines` |
| Writing incrementally / building output over time | `StreamWriter` with `using` |
| Adding data without erasing existing content | `File.AppendAllText` or `StreamWriter(path, true)` |
| Need to guarantee file handle is released | Always use `using (...)` blocks |


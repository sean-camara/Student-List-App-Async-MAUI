# Student List App — Asynchronous SQLite Implementation (.NET MAUI)

A .NET MAUI application that manages local student records using fully **asynchronous SQLite operations**.  
This project refactors a synchronous version and applies complete `async/await` support, fulfilling the requirements of Laboratory Exercise #2 and extending functionality.

---

## Objectives

- Convert the application to use asynchronous SQLite API.  
- Change initialization method (`Init()`) to return `Task`.  
- Replace `SQLiteConnection` with `SQLiteAsyncConnection`.  
- Await `CreateTableAsync()`, `InsertAsync()`, `ToListAsync()`, etc.  
- Update UI event handlers to `async` and await repository calls.

---

## Task Implementation Checklist

| Requirement | Status |
|------------|--------|
| `Init()` changed to `async Task` | ✔ Completed |
| `SQLiteAsyncConnection` used | ✔ Completed |
| `CreateTableAsync()` awaited | ✔ Completed |
| `AddNewStudent` uses `InsertAsync()` | ✔ Completed |
| `GetSection` uses `ToListAsync()` | ✔ Completed |
| UI handlers use `async`/`await` | ✔ Completed |
| Extended CRUD and paging | ✔ Enhanced |

---

## Side-by-Side Comparison (Before vs After)

### Database Initialization

| Before | After |
|--------|-------|
| ```csharp
public void Init()
{
    if (conn != null) return;
    conn = new SQLiteConnection(dbPath);
    conn.CreateTable<Student>();
}
``` | ```csharp
public async Task Init()
{
    if (conn != null) return;
    string dbPath = Path.Combine(FileSystem.AppDataDirectory, "students.db");
    conn = new SQLiteAsyncConnection(dbPath);
    await conn.CreateTableAsync<Student>();
}
``` |

### Insert Operation

| Before | After |
|--------|-------|
| ```csharp
public void AddNewStudent(string name)
{
    conn.Insert(new Student { Name = name });
}
``` | ```csharp
public async Task AddNewStudent(string name)
{
    await Init();
    await conn.InsertAsync(new Student { Name = name });
}
``` |

### Retrieve Operation

| Before | After |
|--------|-------|
| ```csharp
public List<Student> GetSection()
{
    return conn.Table<Student>().ToList();
}
``` | ```csharp
public async Task<List<Student>> GetSection()
{
    await Init();
    return await conn.Table<Student>().ToListAsync();
}
``` |

### UI Handler

| Before | After |
|--------|-------|
| ```csharp
private void OnNewButtonClicked(object sender, EventArgs e)
{
    App.StudentRepo.AddNewStudent(name);
}
``` | ```csharp
private async void OnNewButtonClicked(object sender, EventArgs e)
{
    await App.StudentRepo.AddNewStudent(name);
}
``` |

---

## Database Design

**Student table schema**

```csharp
[PrimaryKey, AutoIncrement]
public int Id { get; set; }
public string Name { get; set; } = string.Empty;

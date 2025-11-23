# Student List App — Asynchronous SQLite Implementation (.NET MAUI)

This repository converts a synchronous MAUI student list app to a fully **asynchronous SQLite implementation** using `SQLiteAsyncConnection`.  
All database operations now use `async/await`, the UI calls await repository methods, and the app includes async CRUD with optional paging.

---

## Objectives

- Convert the project to use asynchronous SQLite API.  
- Add `async` to the database initialization method `Init()`.  
- Use `SQLiteAsyncConnection` instead of `SQLiteConnection`.  
- Await `CreateTableAsync()`, `InsertAsync()`, and `ToListAsync()`.  
- Convert UI handlers to `async` and `await` repository calls.  

This documentation verifies that **all tasks were completed correctly**.

---

## ✔ Task Implementation Checklist

| Requirement | Status |
|-------------|--------|
| `Init()` changed to `async Task` | ✔ Completed |
| `SQLiteConnection` replaced with `SQLiteAsyncConnection` | ✔ Completed |
| `CreateTableAsync()` awaited | ✔ Completed |
| `AddNewStudent` made async and uses `InsertAsync()` | ✔ Completed |
| `GetSection` made async and uses `ToListAsync()` | ✔ Completed |
| UI handlers updated to async and await repo calls | ✔ Completed |
| Additional async CRUD and paging added | ✔ Enhanced beyond requirements |

---

## 🔁 Side-by-Side Comparison (Before vs After)

### 🆚 Database Initialization

| Before (Synchronous) | After (Asynchronous) |
|----------------------|----------------------|
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

---

### 🆚 Insert Operation

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

---

### 🆚 Retrieve Operation

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

---

### 🆚 UI Handler

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

## 🗃 Database Design

### 📌 Student Table Schema
```csharp
[PrimaryKey, AutoIncrement]
public int Id { get; set; }
public string Name { get; set; } = string.Empty;

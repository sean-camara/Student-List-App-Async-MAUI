# Student List App — Asynchronous SQLite Implementation (.NET MAUI)

A .NET MAUI application that manages local student records using **fully asynchronous SQLite operations**.  
This project refactors a synchronous version and applies complete `async/await` support, fulfilling the requirements of *Laboratory Exercise #2: Using SQLite Asynchronously* while extending the functionality beyond the minimum requirements.

---

## 🧾 Purpose of the Laboratory Exercise

The goal of this exercise is to convert a synchronous MAUI application into an asynchronous one by:

- Replacing `SQLiteConnection` with `SQLiteAsyncConnection`
- Converting all database methods to use `async Task` / `Task<T>`
- Awaiting all database calls (`CreateTableAsync`, `InsertAsync`, `ToListAsync`, etc.)
- Updating UI event handlers to `async` and using `await` during DB calls

---

## 🔁 Side-by-Side Comparison (Before vs. After)

### 🆚 **Database Initialization**

| **Before (Synchronous)** | **After (Asynchronous)** |
|--------------------------|--------------------------|
| ```csharp<br>public void Init()<br>{<br>    if (conn != null) return;<br>    conn = new SQLiteConnection(dbPath);<br>    conn.CreateTable<Student>();<br>} ``` | ```csharp<br>public async Task Init()<br>{<br>    if (conn != null) return;<br>    string dbPath = Path.Combine(FileSystem.AppDataDirectory, "students.db");<br>    conn = new SQLiteAsyncConnection(dbPath);<br>    await conn.CreateTableAsync<Student>();<br>} ``` |

### 🆚 **Insert Operation**

| **Before (Synchronous)** | **After (Asynchronous)** |
|--------------------------|--------------------------|
| ```csharp<br>public void AddNewStudent(string name)<br>{<br>    conn.Insert(new Student { Name = name });<br>} ``` | ```csharp<br>public async Task AddNewStudent(string name)<br>{<br>    await Init();<br>    await conn.InsertAsync(new Student { Name = name });<br>} ``` |

### 🆚 **Retrieve Operation**

| **Before (Synchronous)** | **After (Asynchronous)** |
|--------------------------|--------------------------|
| ```csharp<br>public List<Student> GetSection()<br>{<br>    return conn.Table<Student>().ToList();<br>} ``` | ```csharp<br>public async Task<List<Student>> GetSection()<br>{<br>    await Init();<br>    return await conn.Table<Student>().ToListAsync();<br>} ``` |

### 🆚 **UI Button Handler**

| **Before (Synchronous)** | **After (Asynchronous)** |
|--------------------------|--------------------------|
| ```csharp<br>private void OnNewButtonClicked(object sender, EventArgs e)<br>{<br>    App.StudentRepo.AddNewStudent(name);<br>} ``` | ```csharp<br>private async void OnNewButtonClicked(object sender, EventArgs e)<br>{<br>    await App.StudentRepo.AddNewStudent(name);<br>} ``` |

---

## 🧱 System Architecture

### 🗂 Layers Used

| Layer | Description |
|-------|-------------|
| UI | Handles user events (button clicks, list updates) |
| Repository | Contains all database logic and async CRUD methods |
| SQLite Database | Stores local student records |
| Data Model | Defines the `Student` table schema |


---

## 🗃 Database Design

### 📌 Student Table Schema
```csharp
[PrimaryKey, AutoIncrement]
public int Id { get; set; }
public string Name { get; set; }

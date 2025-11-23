# Student List App — Asynchronous SQLite Implementation (.NET MAUI)

This repository demonstrates the transformation of a synchronous MAUI SQLite application into a fully asynchronous student list manager using `SQLiteAsyncConnection`. All CRUD operations now use `async/await`, ensuring smooth UI performance even with larger datasets.

## Source of Original (Synchronous) Project
The asynchronous version in this repository is based on the original synchronous Student List App, found here:

https://github.com/sean-camara/Student_List_App

---

## Objectives

- Convert database operations from synchronous to asynchronous.
- Change the `Init()` method to return `Task`.
- Replace `SQLiteConnection` with `SQLiteAsyncConnection`.
- Await all major SQLite operations (`InsertAsync`, `CreateTableAsync`, `ToListAsync`, etc.).
- Update UI event handlers to use `async` and `await`.
- Implement additional asynchronous features such as update, delete, and paging.

---

## Task Implementation Checklist

| Requirement | Status |
|-------------|--------|
| Converted `Init()` to `async Task` | Done |
| Replaced `SQLiteConnection` with `SQLiteAsyncConnection` | Done |
| Used `CreateTableAsync()` and awaited it | Done |
| Made `AddNewStudent` asynchronous using `InsertAsync()` | Done |
| Converted `GetSection` to use `ToListAsync()` | Done |
| Updated UI handlers to `async` and awaited calls | Done |
| Added async update, delete, and paging | Enhanced |

---

## Side-by-Side Code Comparison

### 1. Database Initialization

**Before (Synchronous)**

```csharp
public void Init()
{
    if (conn != null) return;
    conn = new SQLiteConnection(dbPath);
    conn.CreateTable<Student>();
}
```

**After (Asynchronous)**

```csharp
public async Task Init()
{
    if (conn != null) return;
    string dbPath = Path.Combine(FileSystem.AppDataDirectory, "students.db");
    conn = new SQLiteAsyncConnection(dbPath);
    await conn.CreateTableAsync<Student>();
}
```

### 2. Insert Operation

**Before (Synchronous)

```csharp
public void AddNewStudent(string name)
{
    conn.Insert(new Student { Name = name });
}
```

**After (Asynchronous)**

```csharp
public async Task AddNewStudent(string name)
{
    await Init();
    await conn.InsertAsync(new Student { Name = name });
}
```

### 3. Retrieve All Students

**Before (Synchronous)

```csharp
public List<Student> GetSection()
{
    return conn.Table<Student>().ToList();
}
```

**After (Asynchronous)**

```csharp
public async Task<List<Student>> GetSection()
{
    await Init();
    return await conn.Table<Student>().ToListAsync();
}
```

### 4. Retrieve with Paging

Before (Not Available in Original Project)
(No implementation)

**After (Asynchronous Paging)**

```csharp
public async Task<List<Student>> GetSection(int offset, int limit)
{
    await Init();
    return await conn.Table<Student>()
                     .OrderBy(s => s.Id)
                     .Skip(offset)
                     .Take(limit)
                     .ToListAsync();
}
```

### 5. Update Operation

Before (Not Available in Original Project)

**After (Asynchronous)**

```csharp
public async Task<bool> UpdateStudentAsync(Student student)
{
    await Init();
    return await conn.UpdateAsync(student) > 0;
}
```

### 6. Delete Operation

Before (Not Available in Original Project)

**After (Asynchronous)**

```csharp
public async Task<bool> DeleteStudentAsync(int id)
{
    await Init();
    return await conn.DeleteAsync<Student>(id) > 0;
}
```

### 7. UI Handler (Add)

**Before (Synchronous)**

```csharp
private void OnNewButtonClicked(object sender, EventArgs e)
{
    App.StudentRepo.AddNewStudent(name);
}
```

**After (Asynchronous)**

```csharp
private async void OnNewButtonClicked(object sender, EventArgs e)
{
    await App.StudentRepo.AddNewStudent(name);
}
```

### 8. UI Handler (Edit)

After Implementation (New Feature)

```csharp
private async void OnEditClicked(object sender, EventArgs e)
{
    var btn = sender as Button;
    if (btn?.BindingContext is not Student student) return;

    string result = await DisplayPromptAsync("Edit student", "Update name:", initialValue: student.Name);
    if (string.IsNullOrWhiteSpace(result)) return;

    student.Name = result.Trim();
    await App.StudentRepo.UpdateStudentAsync(student);
}
```

### 9. UI Handler (Delete)


```csharp
private async void OnDeleteClicked(object sender, EventArgs e)
{
    var btn = sender as Button;
    if (btn?.BindingContext is not Student student) return;

    bool ok = await DisplayAlert("Delete", $"Delete \"{student.Name}\"?", "Delete", "Cancel");
    if (!ok) return;

    await App.StudentRepo.DeleteStudentAsync(student.Id);
}
```

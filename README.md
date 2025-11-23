# Student List App — Asynchronous SQLite Implementation (.NET MAUI)

This repository demonstrates the transformation of a synchronous MAUI SQLite application into a fully **asynchronous student list manager**, using `SQLiteAsyncConnection`. All CRUD operations now use `async/await`, ensuring smooth UI performance even with larger datasets.

### 📍 Source of Original (Synchronous) Project

The code in this repository is an improved asynchronous version of the original synchronous Student List application, which can be found here:

🔗 **Original Project (Before Conversion):**
https://github.com/sean-camara/Student_List_App

---

## Objectives

- Convert SQLite operations from synchronous to asynchronous.
- Change the initialization method (`Init()`) to an async `Task`.
- Replace `SQLiteConnection` with `SQLiteAsyncConnection`.
- Await database operations such as `InsertAsync()`, `CreateTableAsync()`, and `ToListAsync()`.
- Modify UI event handlers to use `async` and `await`.
- Implement additional asynchronous CRUD and paging features to enhance the original functionality.

---

## Task Implementation Checklist

| Requirement | Status |
|------------|--------|
| Converted `Init()` to `async Task` | ✔ Done |
| Replaced `SQLiteConnection` with `SQLiteAsyncConnection` | ✔ Done |
| Used `CreateTableAsync()` and awaited it | ✔ Done |
| Made `AddNewStudent` asynchronous using `InsertAsync()` | ✔ Done |
| Converted `GetSection` to use `ToListAsync()` | ✔ Done |
| UI handlers updated to `async` and await calls | ✔ Done |
| Added update, delete, and paging asynchronously | ✔ Enhanced |

---

## 🔁 Side-by-Side Code Comparison

### 🔹 Database Initialization

**Before (Synchronous)**

```csharp
public void Init()
{
    if (conn != null) return;
    conn = new SQLiteConnection(dbPath);
    conn.CreateTable<Student>();
}

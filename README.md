# Student List App (Async SQLite – .NET MAUI)

A mobile application built with **.NET MAUI** that manages student records using **asynchronous SQLite database operations**.  
This project demonstrates proper use of `async/await` to maintain UI responsiveness, based on **Laboratory Exercise #2** requirements.

---

## 🎯 Objectives

- Convert the original Student List App from synchronous to **asynchronous SQLite operations**.
- Use `SQLiteAsyncConnection` instead of `SQLiteConnection`.
- Apply `async/await` to all database actions to avoid UI freezing.
- Implement asynchronous CRUD operations and table initialization.

---

## 🔄 Summary of Changes (Sync ➜ Async)

| Before (Synchronous) | After (Asynchronous) |
|----------------------|----------------------|
| `SQLiteConnection` | `SQLiteAsyncConnection` |
| Methods returned `void` | Methods return `Task` / `Task<T>` |
| `CreateTable()` | `CreateTableAsync()` |
| `Insert()` | `InsertAsync()` |
| `ToList()` | `ToListAsync()` |
| No async initialization | Added `Init()` with await |
| UI handlers were synchronous | UI handlers became `async` |

---

## 📌 Code Examples

### 🛑 Before (Synchronous Init Method)

```csharp
public void Init()
{
    if (conn != null) return;
    conn = new SQLiteConnection(path);
    conn.CreateTable<Student>();
}

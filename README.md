# Student List App — Asynchronous SQLite Implementation (.NET MAUI)

A .NET MAUI application that manages local student records using **fully asynchronous SQLite operations**.  
This project refactors a synchronous version and applies complete `async/await` support, fulfilling the requirements of **Laboratory Exercise #2: Using SQLite Asynchronously** while extending the functionality beyond the minimum requirements.

---

## Purpose of the Laboratory Exercise

The goal of this exercise is to convert a synchronous MAUI application into an asynchronous one by:

- Replacing `SQLiteConnection` with `SQLiteAsyncConnection`
- Converting all database methods to use `async Task` / `Task<T>`
- Awaiting all database calls (`CreateTableAsync`, `InsertAsync`, `ToListAsync`, etc.)
- Updating UI event handlers to `async` and using `await` during DB calls

---

## System Architecture

### Layers Used

| Layer | Description |
|-------|-------------|
| UI | Handles user events (button clicks, list updates) |
| Repository | Contains all database logic and async CRUD methods |
| SQLite Database | Stores local student records |
| Data Model | Defines the `Student` table schema |

```text
┌────────────┐        ┌────────────────────┐        ┌─────────────────────┐
│   UI/XAML  │  -->   │ StudentRepository  │  -->   │ SQLiteAsyncConnection│
└────────────┘        └────────────────────┘        └─────────────────────┘

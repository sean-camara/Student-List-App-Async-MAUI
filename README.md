# Student List App — Asynchronous SQLite Implementation (.NET MAUI)

This repository converts a synchronous MAUI student list app to a fully **asynchronous** implementation using `SQLiteAsyncConnection`.  
All database operations now use `async/await`, the UI calls await repository methods, and the app includes async CRUD and optional paging features.

**Lab reference (local copy):** `/mnt/data/07_Laboratory_Exercise_2(9).pdf`. :contentReference[oaicite:1]{index=1}

---

## Objectives (from the lab)
- Convert the project to use asynchronous SQLite API. :contentReference[oaicite:2]{index=2}  
- Make `Init()` async and return `Task`. :contentReference[oaicite:3]{index=3}  
- Change `conn` to `SQLiteAsyncConnection` and await `CreateTableAsync`. :contentReference[oaicite:4]{index=4}  
- Convert `AddNewStudent`, `GetSection` (and UI callers) to `async` and use `InsertAsync`, `ToListAsync`. :contentReference[oaicite:5]{index=5}

This documentation shows how each item above was implemented and verified.

---

## Verification checklist (lab tasks → how satisfied)

- **Task:** Add `async` modifier to `Init()` and return `Task`.  
  **Status:** Implemented as `public async Task Init()` in `StudentRepository.cs`. (see code below)

- **Task:** Change `conn` property to `SQLiteAsyncConnection` and call `await conn.CreateTableAsync<Student>()`.  
  **Status:** Implemented; the repository creates `SQLiteAsyncConnection(dbPath)` and awaits `CreateTableAsync`. (see code below)

- **Task:** Make `AddNewStudent` async and use `InsertAsync` (awaited).  
  **Status:** Implemented as `public async Task AddNewStudent(string name)` calling `await conn.InsertAsync(...)`.

- **Task:** Make `GetSection` async returning `Task<List<Student>>` and using `ToListAsync`.  
  **Status:** Implemented as `public async Task<List<Student>> GetSection()` and `return await conn.Table<Student>().ToListAsync()`.

- **Task:** Update UI (MainPage) handlers to `async` and `await` repository calls.  
  **Status:** Implemented: UI event handlers (`OnNewButtonClicked`, paging, edit, delete) now `async` and await the repository methods.

All of the above are implemented and tested in the provided final code.

---

## Side-by-side code comparison (Before vs After)

> The table below shows **Before (synchronous)** code on the left and **After (asynchronous)** code on the right.  
> Both columns use full formatted code blocks so you can copy directly into your README.

<table>
  <tr>
    <th style="width:50%; text-align:left">Before (Synchronous)</th>
    <th style="width:50%; text-align:left">After (Asynchronous)</th>
  </tr>

  <tr>
    <td valign="top">
<pre><code class="language-csharp">
// Database Initialization - Before
public void Init()
{
    if (conn != null) return;
    conn = new SQLiteConnection(dbPath);
    conn.CreateTable<Student>();
}
</code></pre>
    </td>
    <td valign="top">
<pre><code class="language-csharp">
// Database Initialization - After
public async Task Init()
{
    if (conn != null) return;
    string dbPath = Path.Combine(FileSystem.AppDataDirectory, "students.db");
    conn = new SQLiteAsyncConnection(dbPath);
    await conn.CreateTableAsync<Student>();
}
</code></pre>
    </td>
  </tr>

  <tr>
    <td valign="top">
<pre><code class="language-csharp">
// Insert Operation - Before
public void AddNewStudent(string name)
{
    conn.Insert(new Student { Name = name });
}
</code></pre>
    </td>
    <td valign="top">
<pre><code class="language-csharp">
// Insert Operation - After
public async Task AddNewStudent(string name)
{
    await Init();
    await conn.InsertAsync(new Student { Name = name });
}
</code></pre>
    </td>
  </tr>

  <tr>
    <td valign="top">
<pre><code class="language-csharp">
// Retrieve Operation - Before
public List<Student> GetSection()
{
    return conn.Table<Student>().ToList();
}
</code></pre>
    </td>
    <td valign="top">
<pre><code class="language-csharp">
// Retrieve Operation - After
public async Task<List<Student>> GetSection()
{
    await Init();
    return await conn.Table<Student>().ToListAsync();
}
</code></pre>
    </td>
  </tr>

  <tr>
    <td valign="top">
<pre><code class="language-csharp">
// UI Handler - Before
private void OnNewButtonClicked(object sender, EventArgs e)
{
    App.StudentRepo.AddNewStudent(name);
}
</code></pre>
    </td>
    <td valign="top">
<pre><code class="language-csharp">
// UI Handler - After
private async void OnNewButtonClicked(object sender, EventArgs e)
{
    await App.StudentRepo.AddNewStudent(name);
}
</code></pre>
    </td>
  </tr>

</table>

---

## Database design (final)

**Student table schema** (final model used in the app):

```csharp
[PrimaryKey, AutoIncrement]
public int Id { get; set; }
public string Name { get; set; } = string.Empty;

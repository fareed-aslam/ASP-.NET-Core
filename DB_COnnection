# Database Connection Setup in ASP.NET Core with Entity Framework Core

This guide explains the complete process of connecting a SQL Server database to an ASP.NET Core application using Entity Framework Core.

---

# Step 1: Install Required Packages

Install the following NuGet packages:

```bash
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

## Package Purpose

### Microsoft.EntityFrameworkCore

Provides the core functionality of Entity Framework Core.

### Microsoft.EntityFrameworkCore.SqlServer

Allows EF Core to communicate with SQL Server.

### Microsoft.EntityFrameworkCore.Tools

Provides migration commands such as:

```bash
dotnet ef migrations add
dotnet ef database update
```

---

# Step 2: Add Connection String

Open:

```text
appsettings.json
```

Add a ConnectionStrings section:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=MyDatabase;Trusted_Connection=True;TrustServerCertificate=True;"
  }
}
```

## Explanation

### Server=.

Represents the SQL Server instance.

Examples:

```text
Server=.
Server=localhost
Server=DESKTOP-PC\SQLEXPRESS
```

### Database=MyDatabase

Specifies the database name.

### Trusted_Connection=True

Uses Windows Authentication.

### TrustServerCertificate=True

Avoids SSL certificate validation issues during development.

---

# Step 3: Create the Model Class

A model represents a table in the database.

Example:

```csharp
public class User
{
    public int Id { get; set; }

    public string Name { get; set; }
}
```

## Mapping

```text
User Class
     ↓
Users Table
```

Properties become columns:

```text
Id      → Column
Name    → Column
```

---

# Step 4: Create DbContext Class

Create a class that inherits from DbContext.

```csharp
using Microsoft.EntityFrameworkCore;

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options)
        : base(options)
    {
    }

    public DbSet<User> Users { get; set; }
}
```

---

# Understanding DbContext

DbContext is the bridge between:

```text
Application
     ↔
Entity Framework Core
     ↔
SQL Server
```

It manages:

- Database Connections
- Queries
- Updates
- Inserts
- Deletes
- Change Tracking

---

# Understanding DbSet

```csharp
public DbSet<User> Users { get; set; }
```

Represents a table.

```text
DbSet<User>
      ↓
Users Table
```

Examples:

```csharp
_context.Users.Add(user);

_context.Users.Remove(user);

_context.Users.ToList();
```

---

# Understanding DbContextOptions

Constructor:

```csharp
public AppDbContext(DbContextOptions<AppDbContext> options)
    : base(options)
{
}
```

The options object contains all database configuration.

Examples:

- Connection String
- Database Provider
- Logging Configuration
- Tracking Configuration

Flow:

```text
Program.cs
     ↓
AddDbContext()
     ↓
DbContextOptions<AppDbContext>
     ↓
AppDbContext Constructor
```

---

# Step 5: Register DbContext in Program.cs

Open:

```text
Program.cs
```

Register AppDbContext.

```csharp
using Microsoft.EntityFrameworkCore;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<AppDbContext>(options =>
{
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")
    );
});

var app = builder.Build();

app.Run();
```

---

# Understanding AddDbContext

```csharp
builder.Services.AddDbContext<AppDbContext>();
```

Registers AppDbContext in Dependency Injection.

Benefits:

- Automatic object creation
- Automatic disposal
- Lifetime management
- Constructor injection support

---

# Understanding UseSqlServer

```csharp
options.UseSqlServer(connectionString);
```

Tells EF Core:

```text
Use SQL Server as Database Provider
```

Without this line EF Core would not know which database engine to use.

---

# Complete Configuration Flow

```text
appsettings.json
        ↓
Connection String
        ↓
Program.cs
        ↓
AddDbContext()
        ↓
UseSqlServer()
        ↓
DbContextOptions<AppDbContext>
        ↓
AppDbContext
        ↓
Entity Framework Core
        ↓
SQL Server
```

---

# Step 6: Create Migration

Run:

```bash
dotnet ef migrations add InitialCreate
```

This command compares:

```text
Current Models
       VS
Database Snapshot
```

Then generates migration files.

Example:

```text
Migrations
│
├── InitialCreate.cs
│
└── AppDbContextModelSnapshot.cs
```

---

# Understanding Migration

A migration is a set of instructions that tells EF Core how to update the database.

Example:

```text
Create Users Table
Add Name Column
Create Primary Key
```

---

# Step 7: Apply Migration

Run:

```bash
dotnet ef database update
```

This command:

1. Connects to SQL Server
2. Creates Database (if it doesn't exist)
3. Creates Tables
4. Applies Pending Migrations

---

# Database Creation Flow

```text
Model Classes
      ↓
Migration
      ↓
Database Update
      ↓
SQL Statements
      ↓
Database Created
      ↓
Tables Created
```

---

# Step 8: Inject DbContext into Controllers

Example:

```csharp
public class UserController : Controller
{
    private readonly AppDbContext _context;

    public UserController(AppDbContext context)
    {
        _context = context;
    }
}
```

Dependency Injection automatically provides the AppDbContext instance.

---

# Example Insert Operation

```csharp
var user = new User
{
    Name = "Fareed"
};

_context.Users.Add(user);

_context.SaveChanges();
```

Flow:

```text
User Object
      ↓
Add()
      ↓
SaveChanges()
      ↓
SQL INSERT
      ↓
Database
```

---

# Example Read Operation

```csharp
var users = _context.Users.ToList();
```

Flow:

```text
Users Table
      ↓
SQL SELECT
      ↓
List<User>
```

---

# Example Update Operation

```csharp
var user = _context.Users.Find(1);

user.Name = "Updated Name";

_context.SaveChanges();
```

Flow:

```text
Find User
      ↓
Modify Property
      ↓
SaveChanges()
      ↓
SQL UPDATE
```

---

# Example Delete Operation

```csharp
var user = _context.Users.Find(1);

_context.Users.Remove(user);

_context.SaveChanges();
```

Flow:

```text
Find User
      ↓
Remove()
      ↓
SaveChanges()
      ↓
SQL DELETE
```

---

# Final Architecture

```text
Client
   ↓
Controller
   ↓
AppDbContext
   ↓
Entity Framework Core
   ↓
SQL Server
```

---

# Complete Database Connection Workflow

```text
1. Install EF Core Packages
          ↓
2. Add Connection String
          ↓
3. Create Models
          ↓
4. Create DbContext
          ↓
5. Register DbContext
          ↓
6. Create Migration
          ↓
7. Update Database
          ↓
8. Inject DbContext
          ↓
9. Perform CRUD Operations
```

---

# Common Commands

Create Migration

```bash
dotnet ef migrations add InitialCreate
```

Update Database

```bash
dotnet ef database update
```

Remove Last Migration

```bash
dotnet ef migrations remove
```

Generate SQL Script

```bash
dotnet ef migrations script
```

List Migrations

```bash
dotnet ef migrations list
```

---

# Summary

Entity Framework Core database connection process:

```text
Connection String
       ↓
Program.cs
       ↓
AddDbContext()
       ↓
UseSqlServer()
       ↓
DbContextOptions
       ↓
AppDbContext
       ↓
Migration
       ↓
Database Update
       ↓
SQL Server
```

Once configured, EF Core handles communication between the ASP.NET Core application and SQL Server, making CRUD operations simpler and more maintainable.

# 📘 Entity Framework Core - ApplicationDbContext

## What is ApplicationDbContext?

`ApplicationDbContext` is the main class of Entity Framework Core that acts as a bridge between your application and the database.

It is responsible for:
- Database Connection
- CRUD Operations
- Change Tracking
- Relationships
- Migrations

---

# Basic Structure

```csharp
using Microsoft.EntityFrameworkCore;

public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    public DbSet<User> Users { get; set; }
}
```

---

# DbContextOptions

Used to pass database configuration to DbContext.

```csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

Registration

```csharp
builder.Services.AddDbContext<ApplicationDbContext>(options =>
{
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"));
});
```

---

# DbSet<T>

Every entity corresponds to one database table.

```csharp
public DbSet<User> Users { get; set; }

public DbSet<Product> Products { get; set; }

public DbSet<Order> Orders { get; set; }

public DbSet<Category> Categories { get; set; }
```

---

# OnModelCreating()

Used to configure database schema using Fluent API.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);
}
```

---

# Table Name

```csharp
modelBuilder.Entity<User>()
    .ToTable("Users");
```

---

# Primary Key

```csharp
modelBuilder.Entity<User>()
    .HasKey(x => x.Id);
```

Composite Key

```csharp
modelBuilder.Entity<StudentCourse>()
    .HasKey(x => new
    {
        x.StudentId,
        x.CourseId
    });
```

---

# Column Name

```csharp
modelBuilder.Entity<User>()
    .Property(x => x.Name)
    .HasColumnName("User_Name");
```

---

# Required Field

```csharp
modelBuilder.Entity<User>()
    .Property(x => x.Email)
    .IsRequired();
```

---

# Max Length

```csharp
modelBuilder.Entity<User>()
    .Property(x => x.Name)
    .HasMaxLength(100);
```

---

# Column Type

```csharp
modelBuilder.Entity<Product>()
    .Property(x => x.Price)
    .HasColumnType("decimal(18,2)");
```

---

# Default Value

```csharp
modelBuilder.Entity<User>()
    .Property(x => x.CreatedAt)
    .HasDefaultValueSql("GETDATE()");
```

---

# Unique Index

```csharp
modelBuilder.Entity<User>()
    .HasIndex(x => x.Email)
    .IsUnique();
```

---

# Normal Index

```csharp
modelBuilder.Entity<Product>()
    .HasIndex(x => x.Name);
```

---

# Alternate Key

```csharp
modelBuilder.Entity<User>()
    .HasAlternateKey(x => x.Email);
```

---

# Check Constraint

```csharp
modelBuilder.Entity<Product>()
    .ToTable(t =>
    {
        t.HasCheckConstraint(
            "CK_Product_Price",
            "Price > 0"
        );
    });
```

---

# Ignore Property

```csharp
modelBuilder.Entity<User>()
    .Ignore(x => x.ConfirmPassword);
```

---

# One-to-Many Relationship

## Models

```csharp
public class User
{
    public int Id { get; set; }

    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }

    public int UserId { get; set; }

    public User User { get; set; }
}
```

Configuration

```csharp
modelBuilder.Entity<Order>()
    .HasOne(x => x.User)
    .WithMany(x => x.Orders)
    .HasForeignKey(x => x.UserId);
```

---

# One-to-One Relationship

## Models

```csharp
public class User
{
    public int Id { get; set; }

    public UserProfile Profile { get; set; }
}

public class UserProfile
{
    public int Id { get; set; }

    public int UserId { get; set; }

    public User User { get; set; }
}
```

Configuration

```csharp
modelBuilder.Entity<User>()
    .HasOne(x => x.Profile)
    .WithOne(x => x.User)
    .HasForeignKey<UserProfile>(x => x.UserId);
```

---

# Many-to-Many Relationship

## Models

```csharp
public class Student
{
    public int Id { get; set; }

    public ICollection<StudentCourse> StudentCourses { get; set; }
}

public class Course
{
    public int Id { get; set; }

    public ICollection<StudentCourse> StudentCourses { get; set; }
}

public class StudentCourse
{
    public int StudentId { get; set; }

    public Student Student { get; set; }

    public int CourseId { get; set; }

    public Course Course { get; set; }
}
```

Configuration

```csharp
modelBuilder.Entity<StudentCourse>()
    .HasKey(x => new
    {
        x.StudentId,
        x.CourseId
    });
```

---

# Cascade Delete

```csharp
modelBuilder.Entity<Order>()
    .HasOne(x => x.User)
    .WithMany(x => x.Orders)
    .HasForeignKey(x => x.UserId)
    .OnDelete(DeleteBehavior.Cascade);
```

Other Options

```csharp
.OnDelete(DeleteBehavior.Restrict);

.OnDelete(DeleteBehavior.NoAction);
```

---

# Seed Data

```csharp
modelBuilder.Entity<Role>()
    .HasData(
        new Role
        {
            Id = 1,
            Name = "Admin"
        },
        new Role
        {
            Id = 2,
            Name = "User"
        }
    );
```

---

# Global Query Filter

```csharp
modelBuilder.Entity<User>()
    .HasQueryFilter(x => !x.IsDeleted);
```

---

# Value Conversion

```csharp
modelBuilder.Entity<User>()
    .Property(x => x.Status)
    .HasConversion<string>();
```

---

# Professional Approach (Entity Configuration)

## UserConfiguration.cs

```csharp
public class UserConfiguration : IEntityTypeConfiguration<User>
{
    public void Configure(EntityTypeBuilder<User> builder)
    {
        builder.ToTable("Users");

        builder.HasKey(x => x.Id);

        builder.Property(x => x.Name)
               .HasMaxLength(100)
               .IsRequired();

        builder.HasIndex(x => x.Email)
               .IsUnique();
    }
}
```

Register All Configurations

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.ApplyConfigurationsFromAssembly(
        typeof(ApplicationDbContext).Assembly
    );
}
```

---

# Complete ApplicationDbContext

```csharp
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    public DbSet<User> Users => Set<User>();

    public DbSet<Product> Products => Set<Product>();

    public DbSet<Category> Categories => Set<Category>();

    public DbSet<Order> Orders => Set<Order>();

    public DbSet<Role> Roles => Set<Role>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.ApplyConfigurationsFromAssembly(
            typeof(ApplicationDbContext).Assembly
        );
    }
}
```

---

# Folder Structure

```
Data/
│── ApplicationDbContext.cs

Models/
│── User.cs
│── Product.cs
│── Order.cs
│── Category.cs

Configurations/
│── UserConfiguration.cs
│── ProductConfiguration.cs
│── OrderConfiguration.cs
│── CategoryConfiguration.cs
```

---

# Interview Revision

- ✅ DbContext
- ✅ DbContextOptions
- ✅ DbSet<T>
- ✅ OnModelCreating()
- ✅ Fluent API
- ✅ HasKey()
- ✅ HasIndex()
- ✅ HasAlternateKey()
- ✅ HasCheckConstraint()
- ✅ HasConversion()
- ✅ HasQueryFilter()
- ✅ HasData()
- ✅ Ignore()
- ✅ One-to-One
- ✅ One-to-Many
- ✅ Many-to-Many
- ✅ Cascade Delete
- ✅ Entity Configuration
- ✅ ApplyConfigurationsFromAssembly()

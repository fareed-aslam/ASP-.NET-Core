4. Typical Folder Structure
Project
│
├── Models
│      User.cs
│      Product.cs
│      Category.cs
│      Order.cs
│      Review.cs
│
├── Data
│      ApplicationDbContext.cs
│
├── Controllers
│
├── Services
│
├── Repositories
│
└── Program.cs


ApplicationDbContext with Multiple Tables
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(
        DbContextOptions<ApplicationDbContext> options
    ) : base(options)
    {

    }

    public DbSet<User> Users { get; set; }

    public DbSet<Product> Products { get; set; }

    public DbSet<Category> Categories { get; set; }

    public DbSet<Order> Orders { get; set; }

    public DbSet<Review> Reviews { get; set; }
}


What is OnModelCreating()?

Ye method Entity Framework ko batata hai

Table ka naam
Primary key
Foreign key
Relationships
Constraints
Indexes
Seed Data
Default values

etc.

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);
}


Fluent API

Fluent API Entity Framework ka configuration syntax hai.

Instead of attributes

[Required]
[StringLength(100)]

Hum likhte hain

modelBuilder.Entity<User>()
    .Property(x => x.Name)
    .HasMaxLength(100)
    .IsRequired();

Advantages

Cleaner
More powerful
Professional projects use this


Professional ApplicationDbContext
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(
        DbContextOptions<ApplicationDbContext> options
    ) : base(options)
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

        modelBuilder.Entity<User>()
            .HasIndex(x => x.Email)
            .IsUnique();

        modelBuilder.Entity<Product>()
            .Property(x => x.Price)
            .HasColumnType("decimal(18,2)");

        modelBuilder.Entity<Order>()
            .HasOne(x => x.User)
            .WithMany(x => x.Orders)
            .HasForeignKey(x => x.UserId);

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
    }
}


Enterprise-Level Best Practice

Large projects me OnModelCreating() ko bada nahi kiya jata.

Har entity ki configuration alag file me hoti hai.

Example

Configurations
│
├── UserConfiguration.cs
├── ProductConfiguration.cs
├── OrderConfiguration.cs
├── CategoryConfiguration.cs
├── ReviewConfiguration.cs

Example

public class UserConfiguration
    : IEntityTypeConfiguration<User>
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

DbContext

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.ApplyConfigurationsFromAssembly(
        typeof(ApplicationDbContext).Assembly
    );
}

Ye automatically sari configuration classes ko load kar leta hai.


📌 Quick Revision
Feature	Purpose
DbContext	Database ke sath communication
DbSet<T>	Database table ko represent karta hai
OnModelCreating()	Entity configuration ki central jagah
Fluent API	Relationships aur constraints configure karne ke liye
HasKey()	Primary Key define karta hai
HasOne() / WithMany()	One-to-Many relationship
WithOne()	One-to-One relationship    
HasForeignKey()	Foreign Key define karta hai
HasIndex()	Index banata hai
IsUnique()	Unique constraint lagata hai
HasDefaultValueSql()	Default SQL value set karta hai
HasConversion()	Data type conversion (e.g., Enum → String)
HasQueryFilter()	Global filter (Soft Delete)
HasData()	Seed data insert karta hai
ApplyConfigurationsFromAssembly()	Sab entity configurations automatically register karta hai

# SOLID Principles in Object-Oriented Programming (OOP)

## Introduction

SOLID is a collection of **5 Object-Oriented Design Principles** that help developers write:

- Clean Code
- Maintainable Code
- Scalable Applications
- Reusable Components
- Loosely Coupled Systems
- Easily Testable Software

These principles were introduced by **Robert C. Martin (Uncle Bob)**.

---

# What is SOLID?

SOLID is an acronym.

| Letter | Principle |
|---------|-----------|
| S | Single Responsibility Principle |
| O | Open Closed Principle |
| L | Liskov Substitution Principle |
| I | Interface Segregation Principle |
| D | Dependency Inversion Principle |

---

# Why SOLID?

Without SOLID, applications become

- Difficult to maintain
- Difficult to test
- Highly coupled
- Difficult to extend
- Buggy after every new feature

With SOLID

✅ Easy Maintenance

✅ Better Reusability

✅ Better Testing

✅ Less Coupling

✅ Easy Feature Addition

---

# 1. Single Responsibility Principle (SRP)

## Definition

> A class should have only **one reason to change**.

OR

A class should perform only **one responsibility**.

Every class should do only one job.

---

## Bad Example

User class

- Register User
- Login User
- Send Email
- Save Data
- Generate Report

```
class UserService
{
    public void Register(){}
    public void Login(){}
    public void SendEmail(){}
    public void SaveToDatabase(){}
    public void GenerateReport(){}
}
```

Problem

One class is doing multiple jobs.

If email changes

↓

UserService changes.

If database changes

↓

UserService changes.

If report changes

↓

UserService changes.

Too many reasons to modify one class.

---

## Good Example

Separate responsibilities.

```
class UserService
{
    public void Register(){}
}

class EmailService
{
    public void SendEmail(){}
}

class ReportService
{
    public void GenerateReport(){}
}

class UserRepository
{
    public void Save(){}
}
```

Now every class has only one job.

---

## Benefits

- Easier debugging
- Easier testing
- Better readability
- Less coupling

---

# 2. Open Closed Principle (OCP)

## Definition

> Software entities should be

- Open for Extension
- Closed for Modification

Meaning

You should be able to add new features

WITHOUT

Changing existing code.

---

## Bad Example

```
class PaymentService
{
    public void Pay(string paymentType)
    {
        if(paymentType=="Card")
        {

        }
        else if(paymentType=="Paypal")
        {

        }
    }
}
```

Now client says

"Add JazzCash"

Again modify class.

Tomorrow

"Easypaisa"

Again modify.

Every new payment method changes old code.

Violation of OCP.

---

## Good Example

Create abstraction.

```
public interface IPayment
{
    void Pay();
}
```

Card

```
class CardPayment : IPayment
{
    public void Pay()
    {

    }
}
```

Paypal

```
class PaypalPayment : IPayment
{
    public void Pay()
    {

    }
}
```

JazzCash

```
class JazzCashPayment : IPayment
{
    public void Pay()
    {

    }
}
```

Payment Service

```
class PaymentService
{
    public void Process(IPayment payment)
    {
        payment.Pay();
    }
}
```

Adding a new payment

↓

Create another class

No old code changes.

---

## Benefits

- Existing code stays safe
- Easy feature addition
- Less bugs

---

# 3. Liskov Substitution Principle (LSP)

## Definition

> Child class should be able to replace Parent class without breaking the program.

Wherever parent object works,

child object should also work correctly.

---

## Bad Example

```
class Bird
{
    public virtual void Fly(){}
}

class Penguin : Bird
{
    public override void Fly()
    {
        throw new Exception();
    }
}
```

Problem

Penguin cannot fly.

But Bird says every bird can fly.

Violation.

---

## Good Example

```
abstract class Bird
{

}

interface IFly
{
    void Fly();
}
```

Flying Bird

```
class Sparrow : Bird, IFly
{
    public void Fly()
    {

    }
}
```

Penguin

```
class Penguin : Bird
{

}
```

Now

No incorrect behavior.

---

## Benefits

- Proper inheritance
- Reliable polymorphism
- Less runtime errors

---

# 4. Interface Segregation Principle (ISP)

## Definition

> Clients should not be forced to implement methods they don't use.

Instead of one huge interface,

create multiple small interfaces.

---

## Bad Example

```
interface IWorker
{
    void Work();
    void Eat();
    void Sleep();
}
```

Robot

```
class Robot : IWorker
{
    public void Work(){}

    public void Eat()
    {
        throw new Exception();
    }

    public void Sleep()
    {
        throw new Exception();
    }
}
```

Robot doesn't eat.

Robot doesn't sleep.

Violation.

---

## Good Example

```
interface IWork
{
    void Work();
}

interface IEat
{
    void Eat();
}

interface ISleep
{
    void Sleep();
}
```

Human

```
class Human : IWork, IEat, ISleep
{

}
```

Robot

```
class Robot : IWork
{

}
```

Perfect.

Only implement required functionality.

---

## Benefits

- Smaller interfaces
- Cleaner code
- Better flexibility

---

# 5. Dependency Inversion Principle (DIP)

## Definition

> High-level modules should not depend on low-level modules.

Both should depend on abstractions.

Also

Don't depend on concrete classes.

Depend on Interfaces.

---

## Bad Example

```
class MySqlDatabase
{
    public void Save(){}
}

class UserService
{
    private MySqlDatabase db = new MySqlDatabase();

    public void Register()
    {
        db.Save();
    }
}
```

Problem

UserService depends directly on MySqlDatabase.

Tomorrow

Need SQL Server.

Need MongoDB.

Need PostgreSQL.

Need Oracle.

You must modify UserService.

Violation.

---

## Good Example

Create Interface

```
interface IDatabase
{
    void Save();
}
```

MySQL

```
class MySqlDatabase : IDatabase
{
    public void Save()
    {

    }
}
```

SQL Server

```
class SqlServerDatabase : IDatabase
{
    public void Save()
    {

    }
}
```

User Service

```
class UserService
{
    private readonly IDatabase database;

    public UserService(IDatabase database)
    {
        this.database = database;
    }

    public void Register()
    {
        database.Save();
    }
}
```

Now

```
IDatabase db = new SqlServerDatabase();

UserService user = new UserService(db);
```

Tomorrow switch to MongoDB

Only change object creation.

UserService remains unchanged.

---

# Real-World Example (.NET)

Repository

```
public interface IUserRepository
{
    Task AddUser(User user);
}
```

Implementation

```
public class UserRepository : IUserRepository
{
    public async Task AddUser(User user)
    {
        // Save into SQL Server
    }
}
```

Service

```
public class UserService
{
    private readonly IUserRepository repository;

    public UserService(IUserRepository repository)
    {
        this.repository = repository;
    }

    public async Task Register(User user)
    {
        await repository.AddUser(user);
    }
}
```

Program.cs

```
builder.Services.AddScoped<IUserRepository, UserRepository>();
builder.Services.AddScoped<UserService>();
```

ASP.NET Core Dependency Injection automatically injects the required implementation.

---

# SOLID Summary

| Principle | Meaning | Main Goal |
|------------|---------|-----------|
| SRP | One class, one responsibility | Easier maintenance |
| OCP | Extend without modifying | Easy feature addition |
| LSP | Child should replace parent safely | Correct inheritance |
| ISP | Small focused interfaces | Avoid unnecessary methods |
| DIP | Depend on interfaces, not concrete classes | Loose coupling |

---

# Easy Way to Remember

### S

One Class → One Job

---

### O

Add New Code

Don't Change Old Code

---

### L

Child should behave like Parent.

---

### I

Small Interfaces are Better.

---

### D

Depend on Interfaces

NOT on Concrete Classes.

---

# SOLID in ASP.NET Core

SOLID is used everywhere in ASP.NET Core.

Examples:

- Controllers
- Services
- Repositories
- Dependency Injection
- Middleware
- Entity Framework Core
- Clean Architecture
- Onion Architecture
- CQRS
- MediatR

Almost every modern .NET project follows SOLID principles.

---

# Final Takeaway

Whenever you design a class, ask yourself:

- Does this class have only one responsibility? (SRP)
- Can I add new functionality without changing existing code? (OCP)
- Can derived classes replace the base class safely? (LSP)
- Is my interface small and focused? (ISP)
- Am I depending on abstractions instead of concrete implementations? (DIP)

If the answer is **Yes** to all five questions, your design is likely following the SOLID principles and will be easier to maintain, test, and extend.

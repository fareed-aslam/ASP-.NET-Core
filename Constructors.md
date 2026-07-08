# C# Core OOP: Constructors & Static Architecture Guide

Comprehensive reference detailing Object-Oriented initialization strategies and the memory rules governing static versus instance states.

---

## 1. Memory Domains: Static vs. Instance

*   **Instance Members:** Exist entirely within individual object structures on the Managed Heap. Every instance maintains discrete variable addresses. Accessed via `instanceName.Member`.
*   **Static Members:** Exist globally on the High-Frequency Heap space bound directly to the class type footprint. Memory is allocated once upon application domain load. Accessed via `ClassName.Member`.

---

## 2. The 5 C# Constructors

### Default Constructor
Automatically supplied by the compiler if no explicit user constructor is mapped. Initializes fields to default literal bit values.
```csharp
public class Item { } // Default parameterless constructor implicit
```

### Parameterized Constructor
Injects variables explicitly into an object's instance state during allocation execution.
```csharp
public class User 
{
    public string Username;
    public User(string name) => Username = name;
}
```

### Copy Constructor
Accepts an instance parameter of its own encompassing type to duplicate instance state values safely into a clean allocation.
```csharp
public class Profile
{
    public string Avatar;
    public Profile(Profile existing) => this.Avatar = existing.Avatar;
}
```

### Private Constructor
Restricts access to local methods. Halts external invocation of the instantiation routine. Used to model Singletons or abstract pure Utility classes.
```csharp
public class DatabaseUtility
{
    private DatabaseUtility() { } // Prevents "new DatabaseUtility()" externally
}
```

### Static Constructor
Invoked natively by the CLR precisely once before initial runtime initialization exposure to the type ecosystem. Holds no parameters, no access modifiers, and cannot bypass automation.
```csharp
public class SystemConfig
{
    public static string TargetEnv;
    static SystemConfig() => TargetEnv = "Production";
}
```

---

## 3. Static Components Specification

### Static Variables & Properties
A unified point of reference across every instance of the system.
```csharp
public class SharedMetrics
{
    private static int _activeCount;
    public static int ActiveCount { get => _activeCount; set => _activeCount = value; }
}
```

### Static Methods
Pure procedural executions. Bound solely to incoming arguments and global static context. They cannot access local instance variables (`this` reference is invalid).
```csharp
public static class MathUtilities
{
    public static int Square(int num) => num * num;
}
```

### Static Classes
Marked with the `static` modifier. Declares that the class cannot be initialized or extended via inheritance. Must strictly contain static members.

---

## 4. Constructor Chaining Rules

Constructor chaining allows developers to pipe initialization data from a downstream constructor upward to a primary constructor structure using the `: this(...)` syntax keyword.

⚠️ **Static Limitation:** Static constructors run purely on automatic runtime events and cannot be invoked from source structures. **Static constructor chaining is strictly impossible.**

```csharp
public class Employee
{
    public string Role;
    public double PayRate;

    // Primary Initializer
    public Employee(string role, double pay)
    {
        Role = role;
        PayRate = pay;
    }

    // Chained Initializer passing default variables upward
    public Employee(string role) : this(role, 25.00) { }
}
```

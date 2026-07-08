# C# Variables, Data Types, and Modifiers Guide

A quick reference guide covering strongly typed, implicitly typed, dynamic variables, and immutability modifiers in C#.

---

## 1. Standard Data Types

```csharp
int age = 22;               // 4-byte whole number
string name = "Fareed";     // Reference type text
double salary = 25000.50;   // 8-byte floating point number
bool isActive = true;       // Boolean (true/false)
```

---

## 2. Special Variable Types

### Implicitly Typed Variables (`var`)
*   **Compile-time fixed:** The compiler determines the type at compilation based on the assigned value.
*   **Initialization requirement:** Must be assigned a value immediately when declared.
*   **Type Locking:** Once initialized, the data type cannot change (e.g., a `string` var cannot later hold an `int`).
*   **Tooling support:** Full IntelliSense support is available because the type is known to the IDE.
*   **Under the hood:** `var` is not a type itself; it is a shorthand. It can represent either value types (`int`) or reference types (`string`).
*   **Scope limits:** Cannot be used as function parameters or return types.

### Dynamic Type (`dynamic`)
*   **Runtime evaluation:** Bypasses compile-time checking; types are resolved only when the application runs.
*   **Type Flexibility:** The underlying data type can safely change mid-execution.
*   **Tooling support:** IntelliSense is not available because the IDE does not know the type ahead of time.
*   **Under the hood:** `dynamic` is always a reference type.

```csharp
// Implicitly typed (Fixed at compile time)
var score = 95; 

// Dynamic type (Evaluated at runtime)
dynamic flexibleData = "Hello";
flexibleData = 100; // Completely valid
```

---

## 3. Immutability Modifiers (`const` vs `readonly`)

### Constants (`const`)
*   **Compile-time evaluation:** Value must be written directly into the code at declaration.
*   **Scope flexibility:** Can be used inside methods or as class member variables.

### Readonly (`readonly`)
*   **Runtime evaluation:** Can be assigned during declaration or dynamically set inside a class constructor.
*   **Scope limits:** Cannot be declared inside individual method scopes; must be class-level member variables.

```csharp
// Constants (Must be assigned at declaration; completely unchangeable)
const double Pi = 3.14159;

// Readonly (Class member variable scope only)
readonly string ConfigPath = "C:/app/config.json";
```

---

## 4. Code Implementation Example

```csharp
using System;

public class HelloWorld
{
    // Readonly field (Must be class level, not inside Main)
    public static readonly string ConfigPath = "C:/app/config.json";

    public static void Main(string[] args)
    {
        Console.WriteLine("Start small. Ship something.");
        
        // --- Working with var ---
        var a = "bro";
        
        // Output variations
        Console.WriteLine(\$"value of a is: {a}");        // String Interpolation
        Console.WriteLine("value of a is {0}", a);       // Composite Formatting
        Console.WriteLine("value of a is: " + a);        // String Concatenation
        
        a = "okay bhai"; // Allowed: Same data type
        Console.WriteLine(\$"value of a is: {a}");
        
        // --- Working with dynamic ---
        dynamic flexible = "Initial string";
        flexible = 12345; // Allowed: Type changes at runtime
    }
}
```

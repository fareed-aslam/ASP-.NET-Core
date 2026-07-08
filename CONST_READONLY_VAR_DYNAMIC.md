# C# Variables, Data Types, and Modifiers Guide (Advanced)

A quick reference guide covering standard types, memory management (Value vs. Reference types), implicitly typed variables, dynamic variables, and immutability modifiers in C#.

---

## 1. Value Types vs. Reference Types (Memory Management)

C# splits data types into two main categories based on how they store data in your computer's memory.

### Value Types
*   **Memory Location:** Stored directly in the **Stack** memory. The stack is fast, structured, and managed automatically.
*   **Assignment Behavior:** When you copy a value type variable, it creates an entirely independent **duplicate copy** of the data. Changing the copy does not affect the original.
*   **Nullability:** Cannot be `null` by default (unless declared as nullable, like `int?`). They hold a default value (e.g., `0` for numbers, `false` for booleans).
*   **Examples:** All built-in numbers (`int`, `double`, `float`), `bool`, `char`, `struct`, and `enum`.

### Reference Types
*   **Memory Location:** The actual data is stored in the **Heap** memory (a large, disorganized pool). The **Stack** only holds a small "pointer" (address reference) pointing to that heap location.
*   **Assignment Behavior:** When you copy a reference type variable, you only copy the **pointer**, not the actual data. Both variables now point to the exact same memory layout on the heap. Changing data through one variable changes it for the other.
*   **Nullability:** Can be `null`, meaning the pointer points to nothing.
*   **Examples:** `string`, `object`, `dynamic`, `class`, arrays, and interfaces.

### Memory Visual Comparison
```text
Value Type (int x = 10; int y = x;)
STACK: [ x = 10 ]  ->  [ y = 10 ] (Two separate slots)

Reference Type (int[] a = {1,2}; int[] b = a;)
STACK: [ a ] -----\
                  +---> HEAP: [ 1, 2 ] (One shared data slot)
STACK: [ b ] -----/
```

---

## 2. Standard Data Types Quick-List

```csharp
int age = 22;               // Value Type: 4-byte whole number
double salary = 25000.50;   // Value Type: 8-byte floating point number
bool isActive = true;       // Value Type: Boolean (true/false)
string name = "Fareed";     // Reference Type: Text string
```

---

## 3. Special Variable Types

### Implicitly Typed Variables (`var`)
*   **Compile-time fixed:** The compiler determines the type at compilation based on the assigned value.
*   **Initialization requirement:** Must be assigned a value immediately when declared.
*   **Type Locking:** Once initialized, the data type cannot change (e.g., a `string` var cannot later hold an `int`).
*   **Tooling support:** Full IntelliSense support is available because the type is known to the IDE.
*   **Under the hood:** `var` is not a data type itself; it is a shorthand. It can represent either value types (`int`) or reference types (`string`).
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

## 4. Immutability Modifiers (`const` vs `readonly`)

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

## 5. Code Implementation Example

```csharp
using System;

public class HelloWorld
{
    // Readonly field (Must be class level, not inside Main)
    public static readonly string ConfigPath = "C:/app/config.json";

    public static void Main(string[] args)
    {
        Console.WriteLine("Start small. Ship something.");
        
        // --- 1. Working with var ---
        var a = "bro"; // Compiled as a reference type (string)
        
        // Output variations
        Console.WriteLine(\$"value of a is: {a}");        // String Interpolation
        Console.WriteLine("value of a is {0}", a);       // Composite Formatting
        Console.WriteLine("value of a is: " + a);        // String Concatenation
        
        a = "okay bhai"; // Allowed: Same data type
        Console.WriteLine(\$"value of a is: {a}");
        
        // --- 2. Working with dynamic ---
        dynamic flexible = "Initial string";
        flexible = 12345; // Allowed: Type changes at runtime
        
        // --- 3. Value Type vs Reference Type Behavior ---
        int valueX = 5;
        int valueY = valueX; // Copying data value
        valueY = 99;         // Changing Y does NOT change X
        Console.WriteLine(\$"Value Type -> X: {valueX}, Y: {valueY}"); // X is still 5
        
        int[] refA = { 1, 2, 3 };
        int[] refB = refA;   // Copying pointer reference
        refB[0] = 99;        // Changing B DOES change A
        Console.WriteLine(\$"Reference Type -> A[0]: {refA[0]}, B[0]: {refB[0]}"); // Both are 99
    }
}
```

# C# Methods and Functions Guide

A quick-reference guide covering methods, parameters, return types, and method overloading mechanics in C#.

---

## 1. Core Concepts: Void vs. Return

A method is a block of code that runs only when it is called. You must explicitly declare whether a method sends data back after it finishes executing.

### Void Methods
*   **Definition:** Uses the `void` keyword. It executes a series of actions but **does not return any data** to the code that called it.
*   **Behavior:** You can use an empty `return;` statement to exit the method early, but you cannot return a value.

### Return Methods
*   **Definition:** Declares a specific data type (like `int`, `string`, `bool`, or a custom class) instead of `void`.
*   **Behavior:** **Must** use the `return` keyword followed by a value matching that data type before the method ends.

```csharp
// Void Method (Performs an action)
public void GreetUser(string name)
{
    Console.WriteLine(\$"Hello, {name}!");
    // No return statement needed
}

// Return Method (Sends back a calculation)
public int AddNumbers(int x, int y)
{
    return x + y; // Must return an int
}
```

---

## 2. Working with Parameters

Parameters act as placeholders for data passed into a method. C# provides highly flexible ways to configure and pass these variables.

### Standard Parameters
Values are passed in the exact order they are declared in the method signature.

### Optional Parameters
*   **Definition:** Allows you to assign a **default value** to a parameter in the method declaration. 
*   **Rule:** All optional parameters **must be placed at the end** of the parameter list, after all required parameters.
*   **Behavior:** If the caller omits the argument, the method uses the default value.

### Named Parameters
*   **Definition:** Allows you to pass arguments by specifying the parameter name followed by a colon (`name: value`).
*   **Behavior:** Frees you from matching the exact positional order of the parameters. This is highly useful for improving readability when working with a long list of optional parameters.

```csharp
// Method declaration with standard and optional parameters
public void DisplayProfile(string name, int age, string city = "Unknown", string role = "Guest")
{
    Console.WriteLine(\$"{name} ({age}) from {city} - Role: {role}");
}

// --- How to Call This Method ---

// 1. Using Standard & Optional defaults
DisplayProfile("Fareed", 22); 

// 2. Overriding the first optional parameter
DisplayProfile("Fareed", 22, "Karachi"); 

// 3. Using Named Parameters to skip 'city' and only change 'role'
DisplayProfile("Fareed", 22, role: "Admin"); 

// 4. Changing parameter order completely using named syntax
DisplayProfile(age: 22, name: "Fareed");
```

---

## 3. Method Overloading

Method overloading allows a class to have **multiple methods with the exact same name**, as long as their input parameters are different. 

### How the Compiler Distinguishes Overloads (The Signature)
The compiler determines which method to call based on its **Unique Signature**. The signature is made up of:
1. The method name.
2. The number of parameters.
3. The data types of the parameters.
4. The order of the parameter data types.

⚠️ **Golden Rule:** The **return type** (e.g., changing `void` to `int`) **does not** count toward method overloading. Changing *only* the return type while keeping identical parameters will cause a compiler error.

```csharp
public class Calculator
{
    // Overload 1: Takes two integers
    public int Multiply(int a, int b)
    {
        return a * b;
    }

    // Overload 2: Takes three integers (Different number of parameters)
    public int Multiply(int a, int b, int c)
    {
        return a * b * c;
    }

    // Overload 3: Takes two doubles (Different parameter types)
    public double Multiply(double a, double b)
    {
        return a * b;
    }
}
```

---

## 4. Code Implementation Example

```csharp
using System;

public class MethodDemo
{
    public static void Main(string[] args)
    {
        // 1. Calling standard return method
        int sum = CalculateSum(10, 20);
        Console.WriteLine(\$"Sum: {sum}");

        // 2. Calling method with optional parameters
        PrintInvoice("Laptop", 1200.50); // Uses default tax (0.05)
        PrintInvoice("Phone", 500.00, 0.15); // Overrides default tax

        // 3. Calling method with named parameters
        PrintInvoice(taxRate: 0.10, itemName: "Tablet", price: 350.00);

        // 4. Calling overloaded methods
        PrintData("Hello Bro"); // Calls string overload
        PrintData(100);         // Calls int overload
    }

    // Standard Return Method
    public static int CalculateSum(int a, int b)
    {
        return a + b;
    }

    // Method with Optional Parameter
    public static void PrintInvoice(string itemName, double price, double taxRate = 0.05)
    {
        double total = price + (price * taxRate);
        Console.WriteLine(\$"Item: {itemName} | Total with Tax: {total}");
    }

    // Method Overloading Implementation
    public static void PrintData(string message)
    {
        Console.WriteLine(\$"Printing string data: {message}");
    }

    public static void PrintData(int number)
    {
        Console.WriteLine(\$"Printing integer data: {number}");
    }
}
```

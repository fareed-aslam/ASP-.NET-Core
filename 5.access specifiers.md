# C# — Access Specifiers / Access Modifiers (A to Z)

Access modifiers ye decide karte hain ke class, method, variable, ya property ko **kahan se access** kiya ja sakta hai — same class, same file, same project (assembly), ya bahar dusre project/class se.

C# mein total **6 access modifiers** hote hain:

1. `public`
2. `private`
3. `protected`
4. `internal`
5. `protected internal`
6. `private protected`

---

## 1. `public`

Sabse open/loose access modifier. Jo member `public` hoga, wo **har jagah se access** ho sakta hai — same class, same project, dusra project, dusri assembly, kahin se bhi.

```csharp
public class Car
{
    public string Model;

    public void Start()
    {
        Console.WriteLine("Car started");
    }
}

// Kahin bhi access ho sakta hai
Car car = new Car();
car.Model = "Corolla";
car.Start();
```

**Kab use karo:** Jab kisi cheez ko outside world (dusri classes/projects) ke liye expose karna ho — jaise public APIs, methods jo bahar se call honi hain.

---

## 2. `private`

Sabse restricted access modifier. `private` member sirf **usi class ke andar** access ho sakta hai jahan wo define hua hai. Bahar se, ya derived class se bhi access nahi hota.

```csharp
class BankAccount
{
    private double balance = 1000;

    private void ShowBalance()
    {
        Console.WriteLine(balance);
    }

    public void Deposit(double amount)
    {
        balance += amount;  // same class ke andar access ho sakta hai
    }
}

// BankAccount acc = new BankAccount();
// acc.balance;      // Error! private hai
// acc.ShowBalance(); // Error! private hai
```

**Kab use karo:** Jab kisi field/method ko sirf internal implementation detail rakhna ho, bahar se koi chhed-chhaad na ho — encapsulation ka core principle yehi hai.

> Note: Agar class member pe koi access modifier na lagao, to by default wo `private` hi hota hai.

---

## 3. `protected`

`protected` member **same class** aur uski **derived (child) classes** mein access ho sakta hai — chahe wo derived class kisi bhi project/assembly mein ho. Lekin bahar se (non-derived class se) access nahi hota.

```csharp
class Animal
{
    protected string Sound = "Generic sound";

    protected void MakeSound()
    {
        Console.WriteLine(Sound);
    }
}

class Dog : Animal
{
    public void Bark()
    {
        Sound = "Woof";   // derived class access kar sakti hai
        MakeSound();
    }
}

// Animal a = new Animal();
// a.Sound; // Error! protected hai, bahar se access nahi
```

**Kab use karo:** Jab kisi base class ki cheez ko sirf uski child classes ke liye available rakhna ho, taake inheritance ke through use ho sake lekin bahar expose na ho.

---

## 4. `internal`

`internal` member sirf **same assembly (project)** ke andar access ho sakta hai. Dusre project/assembly se access nahi hota, chahe wo class inherit hi kyun na ho.

```csharp
internal class Logger
{
    internal void Log(string message)
    {
        Console.WriteLine(message);
    }
}

// Same project ke andar:
Logger logger = new Logger();
logger.Log("App started");

// Dusre project (different assembly) se access nahi hoga
```

**Kab use karo:** Jab koi class/method sirf apne project ke andar use honi ho, bahar (dusre projects/DLLs) ko expose nahi karni.

> Note: Agar top-level class pe koi modifier na lagao, to by default wo `internal` hoti hai.

---

## 5. `protected internal`

Ye `protected` aur `internal` ka combination hai (**OR logic**). Member access ho sakta hai:
- Same assembly (project) ke andar se (kisi bhi class se), **YA**
- Kisi bhi derived class se, chahe wo dusri assembly mein hi kyun na ho.

```csharp
public class Vehicle
{
    protected internal string Type = "Vehicle";
}

// Same project mein koi bhi class access kar sakti hai:
class Garage
{
    void Test()
    {
        Vehicle v = new Vehicle();
        Console.WriteLine(v.Type); // OK, same assembly
    }
}

// Dusre project mein sirf derived class access karegi:
class SportsCar : Vehicle
{
    void Show()
    {
        Console.WriteLine(Type); // OK, derived class hai
    }
}
```

**Kab use karo:** Jab chaho ke apne project ke andar sab access kar sakein, aur bahar sirf inheritance ke through access ho.

---

## 6. `private protected`

Ye `private` aur `protected` ka combination hai (**AND logic**) — C# 7.2 mein introduce hua. Member access ho sakta hai sirf:
- Same class ke andar se, **AND**
- Sirf usi assembly ke andar wali derived classes se (dusri assembly wali derived class access nahi kar sakti).

```csharp
public class Base
{
    private protected int Value = 50;
}

class Derived : Base
{
    void Show()
    {
        Console.WriteLine(Value); // OK, same assembly + derived class
    }
}

// Agar Derived class kisi dusri assembly (project) mein hoti, to Value access nahi hoti
```

**Kab use karo:** Jab inheritance allow karni ho lekin sirf apne hi project ke andar tak restrict karni ho — bohot tight control ke liye.

---

## Quick Comparison Table

| Modifier              | Same Class | Derived Class (Same Assembly) | Same Assembly (Non-Derived) | Derived Class (Other Assembly) | Other Assembly (Non-Derived) |
|------------------------|:----------:|:------------------------------:|:-----------------------------:|:---------------------------------:|:-------------------------------:|
| `public`               | ✅ | ✅ | ✅ | ✅ | ✅ |
| `private`               | ✅ | ❌ | ❌ | ❌ | ❌ |
| `protected`             | ✅ | ✅ | ❌ | ✅ | ❌ |
| `internal`              | ✅ | ✅ | ✅ | ❌ | ❌ |
| `protected internal`    | ✅ | ✅ | ✅ | ✅ | ❌ |
| `private protected`     | ✅ | ✅ | ❌ | ❌ | ❌ |

---

## Default Access Modifiers (agar kuch na likho)

| Element             | Default Modifier |
|----------------------|-------------------|
| `class` members (fields, methods) | `private` |
| Top-level `class`, `struct`, `interface` | `internal` |
| `interface` members  | `public` (aur override nahi ho sakta) |

---

## Real-World Example (sab combine)

```csharp
public class Employee
{
    public string Name;              // Har jagah se access

    private double salary;           // Sirf isi class ke andar

    protected string Department;     // Sirf derived classes

    internal string EmployeeCode;    // Sirf same project

    protected internal string Level; // Same project + derived (kahin se bhi)

    private protected int Rank;      // Sirf same project ki derived class

    public double GetSalary()
    {
        return salary;  // private field ko public method ke through expose kiya
    }
}
```

**Best Practice:** Fields ko hamesha `private` rakho aur `public` properties/methods ke through unhe expose karo — ye **encapsulation** ka golden rule hai.

---

## Summary (One-Liner Yaad Rakhne Ke Liye)

- `public` → Sab kahin se access.
- `private` → Sirf apni class ke andar.
- `protected` → Apni class + child classes (kahin bhi ho).
- `internal` → Sirf apne project ke andar.
- `protected internal` → Apna project (sab) + child classes (bahar bhi).
- `private protected` → Apna project ki child classes tak hi restricted.

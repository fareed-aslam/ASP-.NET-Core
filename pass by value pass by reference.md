# C# — Pass by Value vs Pass by Reference (`ref` & `out`)

## 1. Pass by Value

Default behavior in C#. Jab variable function ko pass hota hai, uski **copy** banti hai. Function ke andar changes original variable ko affect nahi karte.

```csharp
void ChangeValue(int x)
{
    x = 100; // sirf copy change hoti hai
}

int number = 10;
ChangeValue(number);
Console.WriteLine(number); // Output: 10 (koi change nahi)
```

**Value types** (int, double, bool, char, struct, enum) hamesha copy hoke jaate hain jab tak `ref`/`out` na lagao.

**Reference types** (class objects, arrays, string ko chhod ke) ka reference (address) copy hoke jaata hai — isliye object ke fields modify karo to reflect hota hai:

```csharp
class Person { public string Name; }

void ChangeName(Person p)
{
    p.Name = "Ali"; // original object ka field change hoga
}

Person person = new Person { Name = "Ahmed" };
ChangeName(person);
Console.WriteLine(person.Name); // Output: Ali
```

Lekin agar function ke andar naya object assign karo (bina `ref` ke), to wo bahar reflect nahi hoga:

```csharp
void Reassign(Person p)
{
    p = new Person { Name = "Bilal" }; // sirf local reference change hui
}

Reassign(person);
Console.WriteLine(person.Name); // Output: Ali (Bilal nahi)
```

---

## 2. Pass by Reference (`ref` keyword)

`ref` se function ko variable ki **actual memory location** milti hai. Function ke andar changes original variable pe direct reflect hote hain.

### Rules:
- Variable **initialized** hona chahiye call karne se pehle.
- Function `ref` se read aur modify dono kar sakta hai.
- Method signature aur call, dono jagah `ref` likhna zaroori hai.

```csharp
void ChangeValue(ref int x)
{
    x = 100;
}

int number = 10;
ChangeValue(ref number);
Console.WriteLine(number); // Output: 100
```

---

## 3. `out` keyword

`out` bhi reference se pass karta hai, lekin iska use tab hota hai jab function se **multiple values return** karwani hon.

### Rules:
- Variable **initialize karna zaroori nahi** call karne se pehle.
- Function ke andar us parameter ko **assign karna compulsory** hai (warna compile error aayega).

```csharp
void GetValues(out int a, out int b)
{
    a = 5;
    b = 10;
}

int x, y;
GetValues(out x, out y);
Console.WriteLine($"{x}, {y}"); // Output: 5, 10
```

### Modern C# shortcut (inline declaration):

```csharp
GetValues(out int x, out int y);
Console.WriteLine($"{x}, {y}");
```

---

## 4. `ref` vs `out` — Quick Comparison

| Feature                          | `ref`                        | `out`                          |
|-----------------------------------|-------------------------------|----------------------------------|
| Initialize before passing?        | Zaroori hai                   | Zaroori nahi                     |
| Must assign inside method?        | Nahi                          | Zaroori hai                      |
| Use case                          | Read + modify existing value  | Return multiple values from method |
| Value pass hoti hai andar?        | Haan                          | Nahi (garbage maan liya jata hai)|

---

## 5. Quick Summary

- **Pass by Value** → copy jaati hai, original safe rehta hai.
- **Pass by Reference (`ref`)** → original variable directly modify hota hai, initialize hona chahiye.
- **`out`** → multiple return values ke liye, initialize zaroori nahi lekin function ke andar assign zaroori hai.

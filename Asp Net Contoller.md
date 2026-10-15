# ASP.NET Core Web API Controller — Complete Notes (Roman Urdu)

> Zero se advanced concepts tak — definitions, C# examples, routing, HTTP methods, request binding, return types, CRUD aur interview revision.

---

# 1. Controller Kya Hota Hai?

ASP.NET Core Web API mein **Controller ek class hoti hai jo HTTP requests handle karti hai aur client ko response return karti hai.**

Misaal ke taur par hum Student Management System bana rahe hain. Humein students ko:

- Get karna hai.
- Naya student add karna hai.
- Student update karna hai.
- Student delete karna hai.

In operations ko handle karne ke liye hum `StudentController` banate hain.

### Request ka flow

```text
Client / Frontend
       |
       | HTTP Request
       v
ASP.NET Core Routing
       |
       v
StudentController
       |
       v
Action Method
       |
       v
Service / Database
       |
       v
HTTP Response (JSON)
       |
       v
Client / Frontend
```

**Yaad rakho:** Controller request ko handle karta hai, jabke business logic ko aksar separate service mein rakhna behtar hota hai.

# 2. Controller Kaise Banate Hain?

```csharp
using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("api/[controller]")]
public class StudentController : ControllerBase
{
    [HttpGet]
    public string GetStudents()
    {
        return "All Students";
    }
}
```

## Code ki explanation

### `using Microsoft.AspNetCore.Mvc;`

Is namespace mein controller-related classes aur attributes hote hain, jaise:

- `ControllerBase`
- `[ApiController]`
- `[Route]`
- `[HttpGet]`
- `IActionResult`

### `public class StudentController : ControllerBase`

- `public`: Class ka access modifier hai.
- `class`: Class declare karne ke liye keyword hai.
- `StudentController`: Controller ka naam hai.
- `ControllerBase`: API controllers ki base class hai.

Web API controller aam tor par `ControllerBase` se inherit karta hai. MVC controller jo views return karta hai, aam tor par `Controller` se inherit karta hai.

### `[ApiController]`

Ye attribute API-specific behavior enable karta hai, jaise:

- Model validation errors par automatic HTTP 400 response.
- Request data binding ke liye conventions.
- API development ko convenient banana.

### `[Route("api/[controller]")]`

Ye controller ka base URL pattern define karta hai.

`[controller]` token controller ke naam se `Controller` suffix hata deta hai.

```text
StudentController
       |
       v
     student
       |
       v
/api/student
```

### `[HttpGet]`

Ye batata hai ke action HTTP GET request handle karega.

### `GetStudents()`

Ye action method hai jo request receive hone par execute hota hai.

---

# 3. Controller Aur Action Mein Difference

| Controller | Action |
|---|---|
| Ek class hoti hai | Controller ke andar method hota hai |
| Related operations ko organize karta hai | Specific request handle karta hai |
| Example: `StudentController` | Example: `GetStudents()` |
| Multiple actions ho sakte hain | Ek particular operation perform karta hai |

Example:

```csharp
[ApiController]
[Route("api/[controller]")]
public class StudentController : ControllerBase
{
    [HttpGet]
    public IActionResult GetAll()
    {
        return Ok("All Students");
    }

    [HttpGet("{id}")]
    public IActionResult GetById(int id)
    {
        return Ok($"Student ID: {id}");
    }
}
```

Endpoints:

```text
GET /api/student
GET /api/student/5
```

Pehli request `GetAll()` ko aur doosri request `GetById(5)` ko match karegi.

---

# 4. Controller Ko Program.cs Mein Register Karna

ASP.NET Core ke standard controller-based project mein `Program.cs` mein controllers configure kiye jate hain.

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();

var app = builder.Build();

app.MapControllers();

app.Run();
```

### Har line ka kaam

- `CreateBuilder(args)`: Application builder banata hai.
- `AddControllers()`: Controller-related services register karta hai.
- `Build()`: Application banata hai.
- `MapControllers()`: Attribute-routed controllers ke endpoints map karta hai.
- `Run()`: Application start karta hai.

Agar controller-based API endpoints use kar rahe ho, to `AddControllers()` aur `MapControllers()` dono ka role important hai.

---

# 5. Routing Kya Hai?

Routing decide karti hai ke incoming URL aur HTTP method ke mutabiq kaunsa endpoint execute hoga.

Example:

```csharp
[Route("api/[controller]")]
public class StudentController : ControllerBase
{
    [HttpGet]
    public IActionResult GetAll()
    {
        return Ok("All Students");
    }

    [HttpGet("{id}")]
    public IActionResult GetById(int id)
    {
        return Ok(id);
    }
}
```

| Request | Action |
|---|---|
| `GET /api/student` | `GetAll()` |
| `GET /api/student/5` | `GetById(5)` |

## Route parameters

```csharp
[HttpGet("{id}")]
public IActionResult GetById(int id)
{
    return Ok(id);
}
```

Request:

```text
GET /api/student/10
```

Yahan `id = 10` hoga.

Route constraints bhi laga sakte hain:

```csharp
[HttpGet("{id:int}")]
public IActionResult GetById(int id)
{
    return Ok(id);
}
```

`{id:int}` route parameter ko integer route constraint ke mutabiq match karta hai.

## Absolute route

```csharp
[HttpGet("/api/students/all")]
public IActionResult GetAll()
{
    return Ok("All students");
}
```

Starting `/` wala template controller ke route prefix se independent absolute route hota hai.

---

# 6. HTTP Methods Aur CRUD

CRUD ka matlab hai Create, Read, Update aur Delete.

| HTTP Method | Purpose | Example |
|---|---|---|
| GET | Data read karna | `GET /api/student` |
| POST | Naya record create karna | `POST /api/student` |
| PUT | Resource ko update/replace karna | `PUT /api/student/5` |
| PATCH | Resource ka kuch hissa update karna | `PATCH /api/student/5` |
| DELETE | Record delete karna | `DELETE /api/student/5` |

Example:

```csharp
[HttpGet]
public IActionResult GetAll() => Ok();

[HttpPost]
public IActionResult Create() => Ok();

[HttpPut("{id}")]
public IActionResult Update(int id) => Ok();

[HttpPatch("{id}")]
public IActionResult PartialUpdate(int id) => Ok();

[HttpDelete("{id}")]
public IActionResult Delete(int id) => NoContent();
```

Ye methods sirf response examples hain; abhi database mein changes nahi karte.

---

# 7. C# Return Types Ka Concept

C# mein return type batata hai ke method kis type ki value return kar sakta hai.

```csharp
public int GetAge()
{
    return 22;
}
```

Yahan `int` return type hai.

```csharp
public string GetName()
{
    return "Ali";
}
```

Yahan `string` return type hai.

```csharp
public bool IsActive()
{
    return true;
}
```

Yahan `bool` return type hai.

## Common return types

| Return Type | Matlab |
|---|---|
| `string` | Text return karna |
| `int` | Integer return karna |
| `bool` | True ya false return karna |
| `Student` | Ek student object return karna |
| `List<Student>` | Students ki list return karna |
| `IEnumerable<Student>` | Students ki enumerable collection return karna |
| `IActionResult` | Different HTTP results return karna |
| `ActionResult<T>` | Typed data ya HTTP result return karna |
| `Task<T>` | Asynchronous operation jo `T` return kare |
| `Task<IActionResult>` | Asynchronous action jo HTTP result return kare |

---

# 8. IActionResult Kya Hai?

`IActionResult` ek interface hai jo controller action ke HTTP response ko represent karta hai.

Iska faida ye hai ke ek hi action different situations mein different HTTP responses return kar sakta hai.

```csharp
[HttpGet("{id}")]
public IActionResult GetStudent(int id)
{
    if (id <= 0)
    {
        return BadRequest("Invalid student ID");
    }

    if (id == 10)
    {
        return Ok(new { Id = 10, Name = "Ali" });
    }

    return NotFound("Student not found");
}
```

Is example mein:

- `BadRequest()` → HTTP 400
- `Ok()` → HTTP 200
- `NotFound()` → HTTP 404

### Important HTTP result helpers

| Helper | Status Code | Meaning |
|---|---:|---|
| `Ok(data)` | 200 | Request successful |
| `CreatedAtAction(...)` | 201 | Resource create hua |
| `NoContent()` | 204 | Successful response, content nahi |
| `BadRequest()` | 400 | Request invalid |
| `Unauthorized()` | 401 | Authentication required/failed |
| `Forbid()` | 403 | Access allowed nahi |
| `NotFound()` | 404 | Resource nahi mila |
| `Conflict()` | 409 | Resource state mein conflict |
| `StatusCode(500)` | 500 | Server error |

### IActionResult aur Ok() ka difference

```csharp
public IActionResult GetName()
{
    return Ok("Ali");
}
```

- `IActionResult` return type hai.
- `Ok()` helper method hai.
- `"Ali"` response data hai.

`Ok()` khud return type nahi hai.

---

# 9. ActionResult<T> Kya Hai?

Jab action ek specific data type return karta ho aur zaroorat par error response bhi dena ho, tab `ActionResult<T>` useful hai.

```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
}
```

Controller action:

```csharp
[HttpGet("{id}")]
public ActionResult<Student> GetStudent(int id)
{
    if (id != 10)
    {
        return NotFound();
    }

    var student = new Student
    {
        Id = 10,
        Name = "Ali"
    };

    return student;
}
```

Yahan:

- `return student;` → typed student data return hota hai.
- `return NotFound();` → HTTP 404 response return hota hai.

## IActionResult vs ActionResult<T>

| Feature | IActionResult | ActionResult<T> |
|---|---|---|
| Different HTTP responses | Yes | Yes |
| Expected data type declare karta hai | No | Yes |
| `Ok()` / `NotFound()` | Yes | Yes |
| Direct typed model return | Aam tor par `Ok(model)` | Yes |
| Response contract clear karna | Kam explicit | Zyada explicit |

**Practical rule:** Agar endpoint ka expected response `Student` hai, to `ActionResult<Student>` achha choice hai. Agar action ka return data type fixed nahi ya multiple different result types hain, to `IActionResult` suitable ho sakta hai.

---

# 10. List<Student> Aur IEnumerable<Student>

Agar multiple students return karne hon:

```csharp
[HttpGet]
public List<Student> GetAll()
{
    return new List<Student>
    {
        new Student { Id = 1, Name = "Ali" },
        new Student { Id = 2, Name = "Ahmed" }
    };
}
```

Agar HTTP responses par zyada control chahiye:

```csharp
[HttpGet]
public ActionResult<List<Student>> GetAll()
{
    var students = new List<Student>
    {
        new Student { Id = 1, Name = "Ali" },
        new Student { Id = 2, Name = "Ahmed" }
    };

    return Ok(students);
}
```

`List<Student>` ek concrete collection type hai, jabke `IEnumerable<Student>` collection ke elements ko iterate karne ka interface deta hai.

---

# 11. Task<IActionResult> Aur Async/Await

Database queries aur external API calls mein asynchronous programming commonly use hoti hai.

```csharp
[HttpGet]
public async Task<IActionResult> GetStudents()
{
    var students = await _context.Students.ToListAsync();

    return Ok(students);
}
```

### Isko samjho

- `Task<IActionResult>`: Asynchronous operation jo action result produce karega.
- `async`: Method ke andar `await` use karne ki ijazat deta hai.
- `await`: Awaitable operation ke complete hone ka asynchronously wait karta hai.
- `ToListAsync()`: EF Core se data asynchronously retrieve karta hai.
- `Ok(students)`: HTTP 200 result return karta hai.

`Task` HTTP status code nahi hai. Ye asynchronous operation ko represent karta hai.

### Different async return types

```csharp
public async Task<string> GetNameAsync()
{
    await Task.Delay(100);
    return "Ali";
}
```

```csharp
public async Task<ActionResult<Student>> GetStudentAsync(int id)
{
    var student = await _context.Students.FindAsync(id);

    if (student == null)
    {
        return NotFound();
    }

    return student;
}
```

Async ka use especially I/O operations ke liye hota hai. Ye wait ke dauran thread ko synchronously block hone se bachata hai.

---

# 12. Model Binding Kya Hai?

Model binding ASP.NET Core ka process hai jo incoming HTTP request se data read karke action method ke parameters ya model properties mein populate karta hai.

Data mukhtalif jagahon se aa sakta hai:

- URL route.
- Query string.
- Request body.
- Form fields.
- Request headers.

Example:

```csharp
[HttpGet("{id}")]
public IActionResult GetStudent(int id)
{
    return Ok(id);
}
```

Request:

```text
GET /api/student/5
```

ASP.NET Core route se `id = 5` bind karta hai.

Ab hum explicit binding attributes samajhte hain.

---

# 13. [FromRoute] — URL Ke Path Se Data Lena

Jab value URL ke path mein ho, to `[FromRoute]` use kar sakte hain.

```csharp
[HttpGet("{id}")]
public IActionResult GetStudent([FromRoute] int id)
{
    return Ok(new { StudentId = id });
}
```

Request:

```text
GET /api/student/10
```

Result:

```json
{
  "studentId": 10
}
```

Yahan `id = 10` hai.

### Kab use hota hai?

Jab kisi specific resource ko uski ID se access karna ho.

Examples:

```text
GET /api/student/5
PUT /api/student/5
DELETE /api/student/5
```

---

# 14. [FromQuery] — Query String Se Data Lena

Query string URL mein `?` ke baad aati hai.

Example:

```text
GET /api/student/search?name=Ali
```

Controller:

```csharp
[HttpGet("search")]
public IActionResult SearchStudent([FromQuery] string name)
{
    return Ok(new { Name = name });
}
```

Yahan `name = "Ali"` hoga.

### Multiple query parameters

```csharp
[HttpGet("search")]
public IActionResult Search(
    [FromQuery] string name,
    [FromQuery] int page = 1,
    [FromQuery] int pageSize = 10)
{
    return Ok(new
    {
        Name = name,
        Page = page,
        PageSize = pageSize
    });
}
```

Request:

```text
GET /api/student/search?name=Ali&page=2&pageSize=20
```

Result:

```json
{
  "name": "Ali",
  "page": 2,
  "pageSize": 20
}
```

### Kab use hota hai?

- Search.
- Pagination.
- Filtering.
- Sorting.
- Optional parameters.

---

# 15. [FromBody] — Request Body Se Data Lena

Jab client POST, PUT ya PATCH request mein JSON data bhejta hai, to `[FromBody]` us data ko C# model mein deserialize karne ke liye use hota hai.

### Step 1: Student model

```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
}
```

### Step 2: Controller action

```csharp
[HttpPost]
public IActionResult CreateStudent([FromBody] Student student)
{
    return Ok(student);
}
```

### Step 3: Request body

```http
POST /api/student
Content-Type: application/json
```

```json
{
  "id": 1,
  "name": "Ali"
}
```

ASP.NET Core JSON ko `Student` object mein convert karta hai.

Conceptually:

```text
JSON Request Body
       |
       v
JSON Deserialization
       |
       v
Student Object
       |
       v
CreateStudent(student)
```

**Important:** Is example mein student database mein save nahi hota. Sirf request se data receive karke return kiya jata hai.

`[ApiController]` ke saath complex types aam tor par request body se infer hote hain. Explicit `[FromBody]` likhne se source clear ho jata hai.

Ek action mein aam tor par ek hi parameter request body se bind kiya jata hai.

---

# 16. [FromHeader] — Request Headers Se Data Lena

HTTP headers request ke baare mein extra information carry karte hain.

```csharp
[HttpGet("profile")]
public IActionResult GetProfile(
    [FromHeader(Name = "X-Client-Version")] string version)
{
    return Ok(new { ClientVersion = version });
}
```

Example request header:

```http
X-Client-Version: 1.0
```

Yahan `version = "1.0"` hoga.

Headers mein authentication credentials bhi aa sakte hain, lekin authentication ke liye aam tor par configured authentication middleware aur `[Authorize]` use kiya jata hai.

---

# 17. [FromForm] — Form Data Se Data Lena

Jab client form fields ya file upload bhejta hai, to `[FromForm]` useful hai.

```csharp
[HttpPost("upload")]
public IActionResult UploadFile([FromForm] IFormFile file)
{
    if (file == null || file.Length == 0)
    {
        return BadRequest("File is required");
    }

    return Ok(new
    {
        FileName = file.FileName,
        Size = file.Length
    });
}
```

Request mein `multipart/form-data` use hota hai.

Ye example sirf file ki information return karta hai, file ko disk par save nahi karta.

### Binding attributes ka comparison

| Attribute | Data source | Example |
|---|---|---|
| `[FromRoute]` | URL path | `/api/student/5` |
| `[FromQuery]` | Query string | `?page=2` |
| `[FromBody]` | JSON request body | `{ "name": "Ali" }` |
| `[FromHeader]` | HTTP headers | `X-Client-Version: 1.0` |
| `[FromForm]` | Form fields / uploaded file | `multipart/form-data` |
| `[FromServices]` | Dependency injection container | Injected service |

---

# 18. [FromServices] — Dependency Injection Se Service Lena

`[FromServices]` controller action ke parameter ko dependency injection container se resolve karta hai.

```csharp
public interface IStudentService
{
    string GetStudentName(int id);
}
```

Service:

```csharp
public class StudentService : IStudentService
{
    public string GetStudentName(int id)
    {
        return id == 1 ? "Ali" : "Unknown";
    }
}
```

Program.cs:

```csharp
builder.Services.AddScoped<IStudentService, StudentService>();
```

Controller:

```csharp
[HttpGet("{id}")]
public IActionResult GetStudent(
    int id,
    [FromServices] IStudentService studentService)
{
    var name = studentService.GetStudentName(id);

    return Ok(new { Id = id, Name = name });
}
```

`[FromServices]` service ko dependency injection container se leta hai. Real projects mein services ko constructor injection se lena bhi bohot common hai.

---

# 19. Model Binding Aur Model Validation Mein Difference

Dono related hain, lekin same cheez nahi.

- **Model binding:** Request se data read karke model/parameters mein populate karti hai.
- **Model validation:** Check karti hai ke received data defined rules ko satisfy karta hai ya nahi.

Example:

```csharp
using System.ComponentModel.DataAnnotations;

public class CreateStudentRequest
{
    [Required]
    [StringLength(100, MinimumLength = 2)]
    public string Name { get; set; } = "";

    [Range(1, 100)]
    public int Age { get; set; }
}
```

Controller:

```csharp
[HttpPost]
public IActionResult Create([FromBody] CreateStudentRequest request)
{
    return Ok(new
    {
        request.Name,
        request.Age
    });
}
```

`[ApiController]` ke saath invalid model state par framework automatically HTTP 400 response return kar sakta hai.

### Common validation attributes

| Attribute | Kaam |
|---|---|
| `[Required]` | Required value |
| `[StringLength(100)]` | String ki maximum length |
| `[MinLength(2)]` | Minimum collection/string length |
| `[Range(1, 100)]` | Numeric range |
| `[EmailAddress]` | Email format validation |
| `[RegularExpression(...)]` | Pattern validation |

**Note:** Value types, jaise `int`, ko required banane ke liye non-nullable hone ka matlab har situation mein explicit required validation jaisa nahi hota. Rules ko apni requirements ke mutabiq define karo.

---

# 20. DTO Kya Hota Hai?

DTO ka full form **Data Transfer Object** hai.

DTO request aur response data ko represent karta hai. Isse database entity ko directly expose karne ki zaroorat nahi hoti.

Example:

```csharp
public class CreateStudentDto
{
    public string Name { get; set; } = "";
    public int Age { get; set; }
}
```

Controller:

```csharp
[HttpPost]
public IActionResult Create([FromBody] CreateStudentDto dto)
{
    return Ok(new
    {
        Message = "Request received",
        dto.Name,
        dto.Age
    });
}
```

DTO use karne ke faide:

- Request aur response shape control karna.
- Database entity ko directly expose karne se bachna.
- Validation rules ko separate rakhna.
- Unwanted fields ke mass assignment risk ko kam karna.

Real application mein DTO ko service/database layer ke through process karke actual resource create kiya jata hai.

---

# 21. Dependency Injection Aur Constructor Injection

Dependency Injection (DI) ka matlab hai ke class apni dependencies khud create karne ke bajaye configured container se receive karti hai.

Example:

```csharp
public interface IStudentService
{
    List<string> GetAll();
}
```

Implementation:

```csharp
public class StudentService : IStudentService
{
    public List<string> GetAll()
    {
        return new List<string> { "Ali", "Ahmed" };
    }
}
```

Program.cs:

```csharp
builder.Services.AddScoped<IStudentService, StudentService>();
```

Controller:

```csharp
[ApiController]
[Route("api/[controller]")]
public class StudentController : ControllerBase
{
    private readonly IStudentService _studentService;

    public StudentController(IStudentService studentService)
    {
        _studentService = studentService;
    }

    [HttpGet]
    public IActionResult GetAll()
    {
        return Ok(_studentService.GetAll());
    }
}
```

### Lifetimes

- `AddTransient`: Har resolution par naya instance.
- `AddScoped`: Web request ke scope mein aam tor par ek instance.
- `AddSingleton`: Application container ke lifetime mein ek instance.

Service lifetime ko uski dependencies aur state ke mutabiq choose karna chahiye.

---

# 22. Controller, Service Aur Repository Mein Difference

| Layer | Responsibility |
|---|---|
| Controller | HTTP requests aur responses |
| Service | Business logic |
| Repository | Data access abstraction, agar architecture mein use ho |
| DbContext | EF Core ke through database access |
| DTO | Request/response data structure |
| Entity | Domain/database model |

Example architecture:

```text
HTTP Request
     |
     v
Controller
     |
     v
Service
     |
     v
Repository / DbContext
     |
     v
Database
```

Har project mein repository pattern zaroori nahi hota. EF Core ka `DbContext` khud unit-of-work aur repository-like functionality provide karta hai.

---

# 23. Entity Framework Core Ke Saath Real CRUD Controller

Ab ek practical example dekhte hain jisme controller database ke saath interact karta hai.

## Student Entity

```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
    public int Age { get; set; }
}
```

## DbContext

```csharp
using Microsoft.EntityFrameworkCore;

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options)
        : base(options)
    {
    }

    public DbSet<Student> Students => Set<Student>();
}
```

## Program.cs

SQL Server provider installed aur connection string configured honi chahiye.

```csharp
using Microsoft.EntityFrameworkCore;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();

builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));

var app = builder.Build();

app.MapControllers();

app.Run();
```

## Controller

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

[ApiController]
[Route("api/[controller]")]
public class StudentController : ControllerBase
{
    private readonly AppDbContext _context;

    public StudentController(AppDbContext context)
    {
        _context = context;
    }

    // GET: api/student
    [HttpGet]
    public async Task<ActionResult<List<Student>>> GetAll()
    {
        var students = await _context.Students.ToListAsync();

        return Ok(students);
    }

    // GET: api/student/1
    [HttpGet("{id}")]
    public async Task<ActionResult<Student>> GetById(int id)
    {
        var student = await _context.Students.FindAsync(id);

        if (student == null)
        {
            return NotFound();
        }

        return student;
    }

    // POST: api/student
    [HttpPost]
    public async Task<ActionResult<Student>> Create(Student student)
    {
        _context.Students.Add(student);
        await _context.SaveChangesAsync();

        return CreatedAtAction(
            nameof(GetById),
            new { id = student.Id },
            student);
    }

    // PUT: api/student/1
    [HttpPut("{id}")]
    public async Task<IActionResult> Update(int id, Student updatedStudent)
    {
        var student = await _context.Students.FindAsync(id);

        if (student == null)
        {
            return NotFound();
        }

        student.Name = updatedStudent.Name;
        student.Age = updatedStudent.Age;

        await _context.SaveChangesAsync();

        return NoContent();
    }

    // DELETE: api/student/1
    [HttpDelete("{id}")]
    public async Task<IActionResult> Delete(int id)
    {
        var student = await _context.Students.FindAsync(id);

        if (student == null)
        {
            return NotFound();
        }

        _context.Students.Remove(student);
        await _context.SaveChangesAsync();

        return NoContent();
    }
}
```

### CRUD code ki important baatein

- `FindAsync(id)`: Primary key ke through entity find karta hai.
- `Add(student)`: Entity ko insert ke liye track karta hai.
- `Remove(student)`: Entity ko delete ke liye mark karta hai.
- `SaveChangesAsync()`: Changes database mein persist karta hai.
- `CreatedAtAction(...)`: Naye resource ke liye HTTP 201 result aur location information return karta hai.
- `NoContent()`: Successful update/delete ke baad HTTP 204 return karta hai.

Production API mein `Student` entity ko directly POST/PUT ke liye accept karne ke bajaye request DTOs aur validation use karna behtar hai.

---

# 24. Common Controller Mistakes

### Mistake 1: ControllerBase aur Controller ko confuse karna

Web API controller: `ControllerBase`

MVC views controller: `Controller`

### Mistake 2: IActionResult ko method samajhna

`IActionResult` return type hai; `Ok()` helper method hai.

### Mistake 3: FromRoute aur FromQuery ko confuse karna

```text
/api/student/5       -> Route
/api/student?id=5    -> Query
```

### Mistake 4: FromBody ko query string ke liye use karna

`[FromBody]` request body ke liye hai; query string ke liye `[FromQuery]` use hota hai.

### Mistake 5: SaveChangesAsync() bhool jana

EF Core mein entity ko add/update/delete mark karne ke baad changes persist karne ke liye aam tor par `SaveChangesAsync()` call karna hota hai.

### Mistake 6: Har response ko HTTP 200 banana

- Resource nahi mila → `NotFound()`.
- Invalid request → `BadRequest()`.
- Resource create hua → `CreatedAtAction()`.
- Successful update/delete with no response body → `NoContent()`.

### Mistake 7: Har controller mein business logic rakh dena

Bari applications mein business logic ko services mein separate karna maintainability aur testing ke liye useful hai.

---

# 25. Interview Questions

**Q1. Controller kya hota hai?**

Controller HTTP requests ko handle karta hai, actions execute karta hai aur HTTP responses return karta hai.

**Q2. Controller aur action mein kya difference hai?**

Controller class hai; action us class ke andar method hai jo request handle karta hai.

**Q3. ControllerBase aur Controller mein difference?**

`ControllerBase` API functionality deta hai; `Controller` MVC view-related functionality bhi provide karta hai.

**Q4. IActionResult kya hai?**

Interface jo different HTTP action results return karne deta hai.

**Q5. ActionResult<T> kyun use karte hain?**

Expected response data type declare karne aur saath HTTP results return karne ke liye.

**Q6. Task<IActionResult> kya hai?**

Asynchronous action ka return type jo HTTP action result produce karta hai.

**Q7. FromBody aur FromQuery mein difference?**

`FromBody` request body se; `FromQuery` URL query string se data leta hai.

**Q8. FromRoute kya karta hai?**

URL path ke route parameters se values bind karta hai.

**Q9. ApiController attribute kya karta hai?**

API-specific conventions aur automatic invalid model state responses jaisi functionality enable karta hai.

**Q10. Model binding aur validation mein difference?**

Binding data ko request se model mein populate karti hai; validation data ke rules check karti hai.

**Q11. CreatedAtAction kya karta hai?**

Aam tor par HTTP 201 response return karta hai aur newly created resource ko locate karne ke liye route/location information generate karta hai.

**Q12. Dependency Injection kya hai?**

Dependencies ko class ke andar manually create karne ke bajaye configured container se provide karna.

**Q13. DbContext ka role kya hai?**

EF Core ke through entities aur database operations manage karta hai.

**Q14. HTTP 401 aur 403 mein difference?**

401 authentication credentials ki zaroorat ya failure ko indicate karta hai; 403 access forbidden hone ko.

**Q15. PUT aur PATCH mein difference?**

PUT resource ko replace/update karne ke liye hota hai; PATCH partial modification ke liye.

---

# 26. Quick Revision Cheat Sheet

| Concept | Yaad rakhne wali baat |
|---|---|
| Controller | HTTP requests handle karta hai |
| Action | Controller ka method |
| Routing | URL ko endpoint se match karti hai |
| `[HttpGet]` | Read |
| `[HttpPost]` | Create |
| `[HttpPut]` | Replace/update |
| `[HttpPatch]` | Partial update |
| `[HttpDelete]` | Delete |
| `ControllerBase` | API controller base class |
| `IActionResult` | Different HTTP results |
| `ActionResult<T>` | Typed data + HTTP results |
| `Task<T>` | Asynchronous result |
| `[FromRoute]` | URL path |
| `[FromQuery]` | Query string |
| `[FromBody]` | Request body |
| `[FromHeader]` | Request headers |
| `[FromForm]` | Form/file data |
| `[FromServices]` | DI container |
| Model binding | Request data ko populate karna |
| Model validation | Data rules check karna |
| DTO | Request/response data structure |
| DI | Dependencies provide karna |
| `DbContext` | EF Core database access |
| `SaveChangesAsync()` | Database changes persist karna |

## Golden Rules

1. Controller ka kaam HTTP request aur response handle karna hai.
2. `IActionResult` ek return type hai; `Ok()` ek helper method hai.
3. `ActionResult<T>` typed response contract ke liye useful hai.
4. `Task<IActionResult>` asynchronous action result ko represent karta hai.
5. `[FromRoute]`, `[FromQuery]` aur `[FromBody]` alag data sources se values bind karte hain.
6. Model binding aur validation do alag concepts hain.
7. Database operations ke liye EF Core aur dependency injection commonly use hote hain.
8. Real applications mein request DTOs, validation aur appropriate HTTP status codes ka use karo.

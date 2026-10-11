# ASP.NET Core Controllers — Beginner-Friendly Notes

> Learn MVC controllers, Web API controllers, actions, views, inheritance, return types, routing, attributes, HTTP status codes, and common architecture patterns.

## Table of Contents

1. [What Is a Controller?](#1-what-is-a-controller)
2. [What Is MVC?](#2-what-is-mvc)
3. [Common Controller Styles](#3-common-controller-styles)
4. [`Controller` vs. `ControllerBase`](#4-controller-vs-controllerbase)
5. [What Is an Action?](#5-what-is-an-action)
6. [What Is a View?](#6-what-is-a-view)
7. [MVC Action Return Types](#7-mvc-action-return-types)
8. [Web API Controllers and Responses](#8-web-api-controllers-and-responses)
9. [Common HTTP Status Codes](#9-common-http-status-codes)
10. [Important Controller Attributes](#10-important-controller-attributes)
11. [Model Binding](#11-model-binding)
12. [Inheritance: Which Class Should You Use?](#12-inheritance-which-class-should-you-use)
13. [Services and Dependency Injection](#13-services-and-dependency-injection)
14. [Recommended Project Structure](#14-recommended-project-structure)
15. [Quick Revision](#15-quick-revision)

## 1. What Is a Controller?

A controller is a class that handles incoming HTTP requests, coordinates application work, and returns an HTTP response.

For example, a Student Management System might need to get a list of students, retrieve one student's details, create a student, update a student, or delete a student.

A simplified request flow:

```text
Client (Browser / React / Mobile App)
                 |
                 v
       ASP.NET Core Middleware
                 |
                 v
              Routing
                 |
                 v
             Controller
                 |
                 v
            Action Method
                 |
                 v
        HTTP Response to Client
```

A controller may call services or repositories to perform business logic and access data. This diagram is simplified; actual application architecture varies.

## 2. What Is MVC?

MVC stands for **Model–View–Controller**.

| Part | Purpose | Example |
|---|---|---|
| Model | Represents application data or domain concepts | `Student.cs` |
| View | Presents a UI, usually using a Razor `.cshtml` file | `Index.cshtml` |
| Controller | Handles requests and coordinates results | `StudentController.cs` |

### Example model

```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
}
```

A model is a normal C# class in this example. It does not need to inherit from `Controller` or `ControllerBase`.

### Example MVC controller

```csharp
using Microsoft.AspNetCore.Mvc;

public class StudentController : Controller
{
    public IActionResult Index()
    {
        var students = new List<Student>
        {
            new Student
            {
                Id = 1,
                Name = "Ali",
                Email = "ali@example.com"
            },
            new Student
            {
                Id = 2,
                Name = "Ahmed",
                Email = "ahmed@example.com"
            }
        };

        return View(students);
    }
}
```

`Index()` is an action method. `View(students)` asks MVC to render a Razor View with the students as its model.

### Example View: `Views/Student/Index.cshtml`

```cshtml
@model List<Student>

<h1>Students</h1>

<table>
    @foreach (var student in Model)
    {
        <tr>
            <td>@student.Id</td>
            <td>@student.Name</td>
            <td>@student.Email</td>
        </tr>
    }
</table>
```

The controller passes the list to the View, and the View renders HTML for the client.

## 3. Common Controller Styles

### A. MVC Controller

Usually inherits from `Controller`. It is suitable for MVC applications that render Razor Views and HTML.

```csharp
public class StudentController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}
```

### B. Web API Controller

Usually inherits from `ControllerBase`, often with `[ApiController]`. It commonly returns data such as JSON, along with HTTP status codes.

```csharp
using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("api/[controller]")]
public class StudentController : ControllerBase
{
    [HttpGet]
    public IActionResult Get()
    {
        return Ok(new { Message = "Students" });
    }
}
```

These are common patterns, not absolute rules. ASP.NET Core can discover controllers through conventions and attributes; inheriting from these base classes is not mandatory in every possible controller design.

## 4. `Controller` vs. `ControllerBase`

`Controller` inherits from `ControllerBase`.

```text
ControllerBase
      |
      v
  Controller
```

| Feature | `Controller` | `ControllerBase` |
|---|---|---|
| Typical use | MVC with Views | Web APIs |
| `View()` | Yes | No |
| `PartialView()` | Yes | No |
| `Ok()` | Yes | Yes |
| `BadRequest()` | Yes | Yes |
| `NotFound()` | Yes | Yes |
| `Request`, `Response`, `User` | Yes | Yes |
| MVC-specific helpers | Yes | No |

**Rule of thumb:**

- Use `Controller` when you need Razor Views and MVC helpers.
- Use `ControllerBase` for API endpoints that do not need View rendering.
- `[ApiController]` is an attribute, not a base class.

## 5. What Is an Action?

An **action** is a controller method that can handle a request when selected by ASP.NET Core routing and action-selection rules.

```csharp
public class StudentController : Controller
{
    public IActionResult Index()
    {
        return View();
    }

    public IActionResult About()
    {
        return Content("About Page");
    }

    [NonAction]
    public int CalculateTotal(int a, int b)
    {
        return a + b;
    }
}
```

`Index()` and `About()` are action methods. `CalculateTotal()` is not exposed as an action because it has `[NonAction]`.

Not every public method is necessarily an action. Static methods are not actions, and ASP.NET Core has additional discovery and action-selection conventions.

### HTTP method attributes

```csharp
[HttpGet]
public IActionResult GetStudents()
{
    return Ok();
}

[HttpPost]
public IActionResult CreateStudent()
{
    return Ok();
}

[HttpPut("{id}")]
public IActionResult UpdateStudent(int id)
{
    return Ok();
}

[HttpPatch("{id}")]
public IActionResult PartiallyUpdateStudent(int id)
{
    return Ok();
}

[HttpDelete("{id}")]
public IActionResult DeleteStudent(int id)
{
    return NoContent();
}
```

Typical HTTP method meanings:

- `GET`: retrieve data.
- `POST`: create or submit data.
- `PUT`: replace a resource representation, commonly used for full updates.
- `PATCH`: partially update a resource.
- `DELETE`: delete a resource.

The action's method name does not have to match the HTTP verb. Attributes and route configuration control request mapping.

## 6. What Is a View?

A View is the presentation layer in ASP.NET Core MVC, commonly a `.cshtml` file containing HTML and Razor syntax.

Example folder structure:

```text
MyApp/
├── Controllers/
│   └── StudentController.cs
├── Models/
│   └── Student.cs
├── Views/
│   └── Student/
│       ├── Index.cshtml
│       └── Details.cshtml
└── Program.cs
```

### Common View helpers

```csharp
return View();
return View(student);
return View("Details");
return PartialView();
```

- `View()` renders the conventionally selected View.
- `View(student)` passes `student` as the View model.
- `View("Details")` selects a View named `Details`.
- `PartialView()` returns a partial View result.

For example, if `StudentController.Index()` returns `View()`, MVC conventionally looks for `Views/Student/Index.cshtml` or a shared View where applicable.

### Strongly typed View

Controller:

```csharp
public IActionResult Details()
{
    var student = new Student
    {
        Id = 1,
        Name = "Ali",
        Email = "ali@example.com"
    };

    return View(student);
}
```

View: `Views/Student/Details.cshtml`

```cshtml
@model Student

<h1>@Model.Name</h1>
<p>ID: @Model.Id</p>
<p>Email: @Model.Email</p>
```

`@model Student` declares the model type expected by the View.

**Important:** `return View(student)` normally renders HTML on the server. It does not return the student object as a raw JSON API response.

## 7. MVC Action Return Types

`IActionResult` is an interface representing an action result. It lets an action return different kinds of HTTP results.

| Helper | Purpose |
|---|---|
| `View()` | Render a Razor View |
| `PartialView()` | Render a partial View |
| `Json(data)` | Return JSON content |
| `Content("Hello")` | Return text content |
| `RedirectToAction("Index")` | Redirect to another action |
| `Redirect("/home")` | Redirect to a URL |
| `Ok(data)` | Return HTTP 200 |
| `BadRequest()` | Return HTTP 400 |
| `NotFound()` | Return HTTP 404 |
| `NoContent()` | Return HTTP 204 |
| `File(...)` | Return a file response |

Example:

```csharp
public IActionResult Details(int id)
{
    if (id <= 0)
    {
        return BadRequest();
    }

    return View();
}
```

### `IActionResult`, `ActionResult`, and `ActionResult<T>`

- `IActionResult`: interface for action results.
- `ActionResult`: abstract base class for action results.
- `ActionResult<T>`: generic result type, useful in APIs that return typed data on success and other HTTP results on failure.

Example:

```csharp
public ActionResult<Student> GetStudent()
{
    var student = new Student
    {
        Id = 1,
        Name = "Ali",
        Email = "ali@example.com"
    };

    return student;
}
```

For asynchronous actions, common return types include `Task<IActionResult>` and `Task<ActionResult<T>>`.

## 8. Web API Controllers and Responses

A Web API commonly returns structured data, often serialized as JSON, with an appropriate HTTP status code.

```csharp
using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("api/[controller]")]
public class StudentController : ControllerBase
{
    [HttpGet]
    public IActionResult GetStudents()
    {
        var students = new[]
        {
            new { Id = 1, Name = "Ali" },
            new { Id = 2, Name = "Ahmed" }
        };

        return Ok(students);
    }

    [HttpGet("{id:int}")]
    public IActionResult GetStudent(int id)
    {
        if (id <= 0)
        {
            return BadRequest();
        }

        var student = new { Id = id, Name = "Ali" };

        return Ok(student);
    }
}
```

With the route template above, example requests are:

```http
GET /api/student
GET /api/student/1
```

`Ok(students)` creates a 200 OK result with data that ASP.NET Core serializes according to the configured output formatters, commonly JSON.

Example JSON response:

```json
[
  {
    "id": 1,
    "name": "Ali"
  },
  {
    "id": 2,
    "name": "Ahmed"
  }
]
```

Property naming depends on the configured JSON serializer settings.

A Web API is not limited to JSON. It can also return files, text, binary data, and other HTTP responses.

## 9. Common HTTP Status Codes

| Helper | Status | Meaning |
|---|---:|---|
| `Ok(data)` | 200 | Successful request with a response |
| `CreatedAtAction(...)` | 201 | Resource created |
| `NoContent()` | 204 | Successful request without a response body |
| `BadRequest()` | 400 | Invalid request |
| `Unauthorized()` | 401 | Authentication is required or has failed |
| `Forbid()` | 403 | Access is forbidden |
| `NotFound()` | 404 | Resource not found |
| `Conflict()` | 409 | Request conflicts with the current resource state |
| `StatusCode(500)` | 500 | Internal server error response |

Example of creating a resource:

```csharp
[HttpPost]
public ActionResult<Student> CreateStudent(Student student)
{
    // In a real application, save the student to the database first.
    student.Id = 10;

    return CreatedAtAction(
        nameof(GetStudent),
        new { id = student.Id },
        student
    );
}
```

`CreatedAtAction()` returns HTTP 201 Created and can include a URL pointing to the action that retrieves the created resource.

The `401` vs. `403` distinction is important: 401 relates to authentication, while 403 means access is denied. Exact behavior depends on the authentication scheme and application configuration.

## 10. Important Controller Attributes

| Attribute | Purpose |
|---|---|
| `[ApiController]` | Enables API-specific conventions and behaviors |
| `[Route("...")]` | Defines a route template |
| `[HttpGet]` | Maps an action to GET requests |
| `[HttpPost]` | Maps an action to POST requests |
| `[HttpPut]` | Maps an action to PUT requests |
| `[HttpPatch]` | Maps an action to PATCH requests |
| `[HttpDelete]` | Maps an action to DELETE requests |
| `[FromRoute]` | Binds a value from the route |
| `[FromQuery]` | Binds a value from the query string |
| `[FromBody]` | Binds a value from the request body |
| `[FromForm]` | Binds a value from form data |
| `[FromServices]` | Gets a value from dependency injection |
| `[Authorize]` | Applies authorization requirements |
| `[AllowAnonymous]` | Allows access without the usual authorization requirement for that endpoint |
| `[NonAction]` | Prevents a method from being exposed as an action |

### Route binding example

```csharp
[HttpGet("{id:int}")]
public IActionResult GetStudent([FromRoute] int id)
{
    return Ok(new { Id = id });
}
```

Example request:

```http
GET /api/student/5
```

The route supplies `id = 5`.

### Query string binding example

```csharp
[HttpGet("search")]
public IActionResult Search([FromQuery] string name)
{
    return Ok(new { SearchName = name });
}
```

Example request:

```http
GET /api/student/search?name=Ali
```

### Request body binding example

```csharp
[HttpPost]
public IActionResult Create([FromBody] Student student)
{
    return Ok(student);
}
```

The client can send a JSON representation of a student in the request body. With `[ApiController]`, complex parameters commonly use body-binding conventions automatically, so explicitly adding `[FromBody]` is not always necessary.

## 11. Model Binding

Model binding maps incoming request data to action parameters or model objects.

Common sources:

- Route values: `/api/student/5`
- Query string: `/api/student/search?name=Ali`
- Request body: JSON or another configured input format
- Form data: submitted forms or file uploads

Example:

```csharp
[HttpGet("{id:int}")]
public IActionResult GetStudent(
    [FromRoute] int id,
    [FromQuery] bool includeEmail)
{
    return Ok(new { Id = id, IncludeEmail = includeEmail });
}
```

Example request:

```http
GET /api/student/5?includeEmail=true
```

The action receives `id = 5` and `includeEmail = true`.

## 12. Inheritance: Which Class Should You Use?

### MVC controller

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}
```

Use `Controller` when you need MVC View rendering and related helpers.

### Web API controller

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    [HttpGet]
    public IActionResult Get()
    {
        return Ok("Products");
    }
}
```

Use `ControllerBase` for API endpoints that do not need MVC View helpers.

### Normal model class

```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

A model or DTO generally does not need to inherit from a controller base class.

**Remember:**

- `Controller` inherits from `ControllerBase`.
- `[ApiController]` is an attribute, not a base class.
- Inheriting from `Controller` or `ControllerBase` is a common pattern, but not an absolute requirement in every controller design.
- Use inheritance to access framework-provided functionality, not simply because a class exists in the project.

## 13. Services and Dependency Injection

In larger applications, controllers should usually focus on HTTP concerns and delegate business operations to services. Services may use Entity Framework Core's `DbContext` or a repository to access data.

```text
Controller
    |
    v
Service (business logic)
    |
    v
DbContext / Repository (data access)
    |
    v
Database
```

A repository layer is optional; many applications use a service directly with `DbContext`.

### Example service injection

```csharp
public class StudentController : ControllerBase
{
    private readonly StudentService _studentService;

    public StudentController(StudentService studentService)
    {
        _studentService = studentService;
    }

    [HttpGet]
    public async Task<IActionResult> GetStudents()
    {
        var students = await _studentService.GetStudentsAsync();

        return Ok(students);
    }
}
```

Register the service in `Program.cs`:

```csharp
builder.Services.AddScoped<StudentService>();
```

Register any dependencies required by `StudentService` as well. `AddScoped` creates one service instance per request scope.

## 14. Recommended Project Structure

A simple MVC/API project may use a structure like:

```text
MyApp/
├── Controllers/
│   └── StudentController.cs
├── Models/
│   └── Student.cs
├── DTOs/
│   └── CreateStudentDto.cs
├── Services/
│   └── StudentService.cs
├── Views/
│   └── Student/
│       ├── Index.cshtml
│       └── Details.cshtml
├── Program.cs
└── MyApp.csproj
```

Not every project needs all these folders. An API-only project may not have a `Views` folder, and a small project may organize code differently.

## 15. Quick Revision

| Question | Short answer |
|---|---|
| What is a controller? | A class that handles HTTP requests and produces responses |
| What does MVC stand for? | Model–View–Controller |
| What is an action? | A controller method selected to handle a request |
| What is a View? | Usually a `.cshtml` file that renders HTML |
| Which base class is common for MVC? | `Controller` |
| Which base class is common for Web APIs? | `ControllerBase` |
| Does `Controller` inherit from `ControllerBase`? | Yes |
| Does `ControllerBase` have `View()`? | No |
| What is `IActionResult`? | An interface for action results |
| What is `ActionResult<T>`? | A typed action result useful for data and HTTP errors |
| What does `Ok()` return? | HTTP 200 |
| What does `NotFound()` return? | HTTP 404 |
| What does `View(model)` do? | Renders a View with a model |
| What is `[ApiController]`? | An attribute enabling API-specific behavior |
| What does `[HttpGet]` do? | Maps an action to GET requests |
| Does a model need to inherit from a controller? | No |

## Further Learning

After controllers, useful next topics are:

1. Model binding and validation.
2. DTOs and data annotations.
3. Dependency Injection and service lifetimes.
4. Entity Framework Core CRUD operations.
5. Routing and REST API design.
6. Authentication and authorization.

# ASP.NET Core Middleware — Notes from Zero

> Beginner-friendly notes on what middleware is, how the request pipeline works, common categories, and how to recognize middleware in `Program.cs`.

## Table of Contents

- [1. What Is Middleware?](#1-what-is-middleware)
- [2. How the Middleware Pipeline Works](#2-how-the-middleware-pipeline-works)
- [3. Common Middleware Categories](#3-common-middleware-categories)
- [4. Inline Middleware](#4-inline-middleware)
- [5. Class-Based Custom Middleware](#5-class-based-custom-middleware)
- [6. Built-in Middleware](#6-built-in-middleware)
- [7. `Use`, `Run`, and `Map`](#7-use-run-and-map)
- [8. Services, Middleware, and Endpoints](#8-services-middleware-and-endpoints)
- [9. Middleware Ordering](#9-middleware-ordering)
- [10. How to Identify Middleware](#10-how-to-identify-middleware)
- [11. Quick Revision](#11-quick-revision)

## 1. What Is Middleware?

Middleware is a component in an ASP.NET Core application's HTTP request pipeline. It can inspect or modify an HTTP request, call the next component, inspect or modify the response, or stop the pipeline and return a response.

For example, a request to:

```http
GET /api/students
```

may pass through exception handling, HTTPS redirection, routing, authentication, authorization, and finally an endpoint.

Middleware can:

- Log requests and responses.
- Handle exceptions.
- Redirect HTTP requests to HTTPS.
- Authenticate users.
- Check authorization policies.
- Serve static files.
- Apply CORS or rate-limiting policies.
- Short-circuit a request by returning a response without calling the next component.

## 2. How the Middleware Pipeline Works

A simplified pipeline:

```text
Client
  |
  v
Exception Handling
  |
  v
HTTPS Redirection
  |
  v
Routing
  |
  v
Authentication
  |
  v
Authorization
  |
  v
Endpoint / Controller
  |
  v
HTTP Response back to Client
```

This is an illustrative example, not a mandatory order for every application. The actual pipeline depends on the application's configuration and requirements.

### Request and response flow

Middleware generally runs in registration order on the way in. If middleware calls the next delegate and then executes more code, that later code runs while the response unwinds back through the pipeline.

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("A - Before");
    await next(context);
    Console.WriteLine("A - After");
});

app.Use(async (context, next) =>
{
    Console.WriteLine("B - Before");
    await next(context);
    Console.WriteLine("B - After");
});

app.Run(async context =>
{
    Console.WriteLine("C - Endpoint");
    await context.Response.WriteAsync("Hello");
});
```

Expected console output:

```text
A - Before
B - Before
C - Endpoint
B - After
A - After
```

The exact `next` delegate signature depends on the overload being used. In common modern `app.Use` examples, `next(context)` is valid; some overloads expose a parameterless `next()` instead.

## 3. Common Middleware Categories
<img width="1380" height="674" alt="image" src="https://github.com/user-attachments/assets/2f95233d-739c-49dd-9385-5976fe680865" />


There is **no single official list with an exact number of middleware types**. These labels classify middleware in different ways and can overlap.

| Category | Meaning | Example |
|---|---|---|
| Inline middleware | Logic is written directly in the application pipeline setup | `app.Use(...)` |
| Class-based custom middleware | A developer creates a middleware class | `app.UseMiddleware<RequestLoggingMiddleware>()` |
| Built-in middleware | Functionality provided by ASP.NET Core or its middleware packages | `app.UseAuthentication()` |
| Branching / mapping | Routes a matching request into a separate pipeline branch | `app.Map("/admin", ...)` |

A custom middleware class is also class-based. Built-in middleware is also registered into the pipeline. These are useful learning categories, not mutually exclusive official types.

## 4. Inline Middleware

Inline middleware is written directly in `Program.cs`, usually as a delegate or lambda.

### A. `app.Use(...)`

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("Request received");

    await next(context);

    Console.WriteLine("Response completed");
});
```

- `context`: the current `HttpContext`, containing request and response information.
- `next`: the delegate that invokes the next middleware.
- `await next(context)`: passes processing to the next component.
- Code after `await next(context)`: runs after downstream processing completes.

Calling `next` is optional. If middleware does not call it and instead writes a response, it can short-circuit the pipeline.

### B. `app.Run(handler)`

```csharp
app.Run(async context =>
{
    await context.Response.WriteAsync("Hello!");
});
```

This overload registers terminal middleware. It does not call a next middleware delegate, so later middleware registered after it will not be reached through this pipeline path.

**Important:** `app.Run(handler)` is middleware, but `app.Run()` with no handler starts the web application. They are different uses of the same method name.

### C. `app.Map(...)`

```csharp
app.Map("/admin", adminApp =>
{
    adminApp.Run(async context =>
    {
        await context.Response.WriteAsync("Admin Area");
    });
});
```

`app.Map("/admin", ...)` creates a path-based branch. A request matching the branch path can be handled by that separate pipeline.

Do not confuse it with `app.MapGet(...)`, which maps a Minimal API endpoint.

## 5. Class-Based Custom Middleware

Use a custom middleware class when you want to reuse or organize middleware logic outside `Program.cs`.

### Step 1: Create the middleware class

```csharp
public class RequestLoggingMiddleware
{
    private readonly RequestDelegate _next;

    public RequestLoggingMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        Console.WriteLine($"Request path: {context.Request.Path}");

        await _next(context);
    }
}
```

What each part means:

| Code | Meaning |
|---|---|
| `RequestLoggingMiddleware` | The custom middleware class |
| `RequestDelegate _next` | Holds the next middleware delegate |
| Constructor | Receives the next delegate when the middleware is constructed |
| `InvokeAsync(HttpContext context)` | Method called to process an HTTP request |
| `await _next(context)` | Passes the request to the next component |

### Step 2: Register it

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.UseMiddleware<RequestLoggingMiddleware>();

app.MapGet("/", () => "Hello World!");

app.Run();
```

The middleware logs the request path and then passes the request onward.

**Recognition tip:** A class, a constructor accepting `RequestDelegate`, an `Invoke`/`InvokeAsync` method, and registration via `UseMiddleware<T>()` are common signs of class-based middleware. Valid middleware can use other patterns too.

## 6. Built-in Middleware

ASP.NET Core provides middleware for common web application needs. Some features require service registration or additional configuration.

| Registration method | Purpose |
|---|---|
| `app.UseExceptionHandler(...)` | Handles unhandled exceptions using the configured error-handling pipeline |
| `app.UseHttpsRedirection()` | Redirects eligible HTTP requests to HTTPS |
| `app.UseStaticFiles()` | Serves static files such as CSS, JavaScript, and images |
| `app.UseRouting()` | Matches requests to endpoints |
| `app.UseCors(...)` | Applies configured Cross-Origin Resource Sharing policies |
| `app.UseAuthentication()` | Attempts to establish the current user's identity |
| `app.UseAuthorization()` | Enforces authorization policies |
| `app.UseSession()` | Enables configured session state |
| `app.UseResponseCompression()` | Compresses eligible responses |
| `app.UseRateLimiter()` | Applies configured rate-limiting policies |
| `app.UseAntiforgery()` | Applies configured antiforgery protections in supported scenarios |

Availability and setup can depend on the ASP.NET Core version and application configuration.

### Authentication vs. Authorization

- **Authentication:** Who are you?
- **Authorization:** Are you allowed to access this resource?

For example, a user may be authenticated but not authorized to access an admin-only endpoint.

## 7. `Use`, `Run`, and `Map`

| Method | Main purpose | Can continue to the next middleware? |
|---|---|---|
| `app.Use(...)` | Runs logic around downstream processing | Yes, if it calls `next` |
| `app.Run(handler)` | Registers terminal middleware | No next delegate is provided |
| `app.Map("/path", ...)` | Creates a path-based branch | Matching requests use the branch pipeline |
| `app.MapGet("/path", ...)` | Maps a Minimal API GET endpoint | It is endpoint mapping, not the same as `app.Map` branching |

### Example

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("Before");
    await next(context);
    Console.WriteLine("After");
});

app.Map("/hello", branch =>
{
    branch.Run(async context =>
    {
        await context.Response.WriteAsync("Hello");
    });
});
```

Use `Use` when you need to run logic before and/or after downstream middleware. Use terminal `Run(handler)` when that component should finish the request. Use `Map` when a matching path needs a separate pipeline branch.

## 8. Services, Middleware, and Endpoints

These are related but different concepts.

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();
builder.Services.AddAuthentication();
builder.Services.AddAuthorization();

var app = builder.Build();

app.UseHttpsRedirection();
app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();

app.Run();
```

- `builder.Services.Add...()` registers services and configuration in dependency injection. It does **not**, by itself, add middleware to the HTTP pipeline.
- `app.Use...()` commonly registers middleware.
- `app.MapControllers()` maps controller endpoints.
- `app.Run()` starts the web application.
- `app.Run(handler)` registers terminal middleware.

## 9. Middleware Ordering

Order matters because each component may depend on work done by an earlier component.

A common simplified API setup is:

```csharp
app.UseHttpsRedirection();

app.UseRouting();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();
```

Authentication is generally placed before authorization so the authorization system can use the established user identity. Real applications may need additional middleware, services, and ordering based on their requirements.

## 10. How to Identify Middleware

| What you see | Likely meaning |
|---|---|
| `app.Use(async (context, next) => ...)` | Inline middleware |
| `app.Run(async context => ...)` | Inline terminal middleware |
| `app.Map("/path", ...)` | Path-based branching |
| `app.UseMiddleware<MyMiddleware>()` | Registration of class-based middleware |
| `class MyMiddleware` with `Invoke` or `InvokeAsync` | Common custom middleware class pattern |
| `app.UseAuthentication()` | Built-in authentication middleware |
| `app.UseAuthorization()` | Built-in authorization middleware |
| `app.UseRouting()` | Built-in routing middleware |
| `builder.Services.AddControllers()` | Service registration, not middleware registration |
| `app.MapControllers()` | Controller endpoint mapping |

**Remember:** A single component may fit more than one category. For example, custom middleware can be both custom and class-based.

## 11. Quick Revision

- **Middleware:** A component in the HTTP request pipeline.
- **Inline middleware:** Logic written directly in the pipeline setup.
- **Class-based custom middleware:** Middleware logic placed in a class.
- **Built-in middleware:** Middleware functionality provided by ASP.NET Core or its packages.
- **`Use`:** Can call the next middleware.
- **`Run(handler)`:** Terminal middleware.
- **`Map`:** Creates a path-based branch.
- **Order matters:** Registration order affects request and response processing.
- **Services are not middleware:** `builder.Services.Add...()` configures services; `app.Use...()` commonly configures the pipeline.

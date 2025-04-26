## 🔵 What are Integration Tests?
Integration tests check **how different parts (modules, components, services)** of your application work **together**.  
Unlike **unit tests**, which test a small, isolated piece of code (like a single method), **integration tests** verify **multiple units** interacting **in combination**.

🔹 **Goal:**  
- To catch bugs that happen when components/modules are **combined**.
- To ensure **real-world scenarios** work as expected.

---

## 🧩 Why are Integration Tests Important?
Because even if all your units are perfect *individually*, **problems can occur when they are wired together**.  
Examples of such problems:
- Incorrect data mapping between layers.
- Communication issues between services (like APIs).
- Misconfigured databases.
- Authentication or authorization failures.
- API contracts between services not matching.

---

## 🔵 Where Do Integration Tests Fit? (Test Pyramid)

You may have heard of the **Test Pyramid**:  
```
       UI Tests (few, slow, expensive)
     Integration Tests (moderate amount)
    Unit Tests (lots, fast, cheap)
```
- **Unit tests:** test tiny pieces.
- **Integration tests:** test the interaction between pieces.
- **UI/end-to-end tests:** test the entire system.

**👉 Integration tests** are in the middle: they are heavier than unit tests, but still lighter than full end-to-end (UI) tests.

---

## 🔵 Examples of Integration Testing

- Testing a **Web API controller** that calls a **service** and **repository** (and maybe hits a **real database** or a **test database**).
- Testing a **microservice** that calls another **microservice** using HTTP or gRPC.
- Testing a **repository** class that interacts with a **real** (or **in-memory**) database (e.g., PostgreSQL, SQLite).
- Testing **background jobs** that consume a **queue** and write to a database.

---

## 🔵 Key Concepts in Integration Testing

| Concept | Meaning |
|:---|:---|
| **System Under Test (SUT)** | A combination of real components. |
| **Real vs Mocked Dependencies** | You often use real services, databases, queues — or lightweight fakes (like TestContainers, in-memory DBs). |
| **Setup and Teardown** | Preparing the environment before tests and cleaning it afterward. |
| **Environment Control** | Ensure tests are predictable: use test configurations, local DBs, mock external services if needed. |
| **Idempotence** | Tests should be re-runnable; running them twice shouldn't break anything. |

---

## 🔵 How to Write an Integration Test (General Steps)

1. **Arrange:**
   - Start your application in a **test mode**.
   - Seed the database (optional).
   - Mock or fake external services if needed (optional).

2. **Act:**
   - Perform real operations: call HTTP endpoints, run service methods, simulate queues, etc.

3. **Assert:**
   - Check the result: status codes, database records, queue messages, etc.
   - Make sure the system behaves as expected.

4. **Clean up:**
   - Reset database/queues to avoid test pollution.

---

## 🔵 Example (ASP.NET Web API Integration Test)

Imagine you have an endpoint like:
```csharp
POST /api/products
```
which saves a product to a database.

✅ Integration test could:
- Spin up a real test server.
- POST a real HTTP request to `/api/products`.
- Check the HTTP response.
- Query the database directly and confirm that the product was saved correctly.

Example using `WebApplicationFactory` + `HttpClient` + `Testcontainers` (in-memory database):
```csharp
[Fact]
public async Task CreateProduct_Should_SaveProductToDatabase()
{
    // Arrange
    var client = _factory.CreateClient();
    var newProduct = new { Name = "Laptop", Price = 1200 };

    // Act
    var response = await client.PostAsJsonAsync("/api/products", newProduct);

    // Assert
    response.EnsureSuccessStatusCode(); // Status 200-299
    var productInDb = await _dbContext.Products.FirstOrDefaultAsync(p => p.Name == "Laptop");
    Assert.NotNull(productInDb);
    Assert.Equal(1200, productInDb.Price);
}
```

Here:
- `client` sends a **real HTTP request**.
- **Database** is a real (but isolated) test database.
- We **assert** that after calling the API, the DB state is updated.

---

## 🔵 Integration Tests: Best Practices

| Best Practice | Reason |
|:---|:---|
| Use a separate test database or container (like Testcontainers) | Keep tests isolated and clean. |
| Keep tests **fast** | Integration tests are slower than unit tests — but they should still be reasonably quick. |
| Reset state between tests | Avoid "test pollution" where one test affects another. |
| Run integration tests in CI/CD pipelines | Catch issues early. |
| Prefer **real services** over mocks where possible | Higher confidence that the system works end-to-end. |

---

## 🔵 Tools Commonly Used

| Platform | Tools |
|:---|:---|
| .NET / C# | xUnit, MSTest, NUnit, WebApplicationFactory, Testcontainers, Respawn (reset DB) |
| Java | JUnit, SpringBootTest, Testcontainers |
| Node.js | Jest, Supertest |
| Python | Pytest, requests, testcontainers |

---

## 🔵 Integration Testing vs Other Types

| Type | What it tests | Scale | Example |
|:---|:---|:---|:---|
| Unit Test | Single function/class | Tiny | Test `AddProductService.Add()` logic alone. |
| Integration Test | Multiple components working together | Medium | Test `ProductController → Service → Repository → DB` all together. |
| End-to-End (E2E) Test | Full application (UI + backend + DB) | Huge | Open browser, click "Add Product", verify in UI and DB. |

---

## 🎯 Quick Summary

- **Unit tests** = *Is this small piece correct?*
- **Integration tests** = *Do these pieces work together correctly?*
- **E2E tests** = *Does the entire system behave as expected for a real user?*

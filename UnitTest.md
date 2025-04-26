---

# 🧠 What are Unit Tests?
**Unit tests** check **individual, isolated pieces of code** — typically a **single function, method, or class** — to ensure they behave **exactly** as expected.

🔹 **Goal:**  
- Verify that one "unit" of your program (smallest testable part) **works correctly in isolation**.
- Catch **small logic bugs** immediately.
- Give you confidence when **refactoring**.

**Unit = One function/method/class at a time**  
👉 No database, no network, no disk access — **pure code**.

---

# 🧩 Why are Unit Tests Important?
- **Fast feedback**: You find bugs instantly.
- **Safe refactoring**: You can change code without fear if tests still pass.
- **Living documentation**: Other developers can understand what your code does by looking at your tests.
- **Prevent regressions**: If something breaks, you find out right away.

---

# 🔵 Characteristics of Good Unit Tests

| Characteristic | Description |
|:---|:---|
| **Fast** | Run in milliseconds. |
| **Isolated** | Only test one thing, no external systems involved. |
| **Repeatable** | Same result every time you run them. |
| **Clear and Focused** | Only one reason for a test to fail. |
| **Independent** | Tests do not depend on each other. |

---

# 🔵 Anatomy of a Unit Test

Almost all unit tests follow the **AAA** pattern:

### 1. Arrange
- Set up the system under test (SUT) and its dependencies.
- Prepare inputs.

### 2. Act
- Call the method or function you're testing.

### 3. Assert
- Verify that the output/result is what you expect.

---

# 🔵 Simple Example (C#)

Let's say you have this method:
```csharp
public class Calculator
{
    public int Add(int a, int b)
    {
        return a + b;
    }
}
```

✅ A unit test could be:
```csharp
[Fact]
public void Add_ShouldReturnSum_WhenTwoNumbersAreGiven()
{
    // Arrange
    var calculator = new Calculator();

    // Act
    var result = calculator.Add(2, 3);

    // Assert
    Assert.Equal(5, result);
}
```

Notice:
- No databases, no APIs, no external stuff.
- Just pure logic.

---

# 🔵 When to Mock / Stub / Fake

If your **unit** depends on something "external" (e.g., database, API, file system), you **mock** it to keep the unit isolated.

| Term | Meaning |
|:---|:---|
| **Mock** | A fake object that can verify how it was used. |
| **Stub** | A fake object that just returns predefined values. |
| **Fake** | A working lightweight replacement (like an in-memory database). |

**Example:**  
If your `OrderService` depends on `IPaymentGateway`, you mock `IPaymentGateway` when testing `OrderService` — you don't call real APIs during unit tests.

Using a mock framework like **Moq** (in .NET):
```csharp
var paymentGatewayMock = new Mock<IPaymentGateway>();
paymentGatewayMock.Setup(pg => pg.Charge(It.IsAny<decimal>())).Returns(true);
```

Then pass `paymentGatewayMock.Object` to your service.

---

# 🔵 How Unit Tests Fit into the Test Pyramid

```
       UI Tests (fewest, slowest)
     Integration Tests (moderate)
    Unit Tests (most, fastest)
```
- **Unit tests** are the **base** of the pyramid: you should have **lots** of them!
- They're **easy to write** and **cheap to run**.

---

# 🔵 Example: Realistic Service Unit Test (C#)

Suppose you have a `UserService`:

```csharp
public class UserService
{
    private readonly IUserRepository _userRepository;

    public UserService(IUserRepository userRepository)
    {
        _userRepository = userRepository;
    }

    public async Task<bool> IsUsernameAvailable(string username)
    {
        var user = await _userRepository.FindByUsernameAsync(username);
        return user == null;
    }
}
```

✅ Unit Test:

```csharp
[Fact]
public async Task IsUsernameAvailable_ShouldReturnTrue_WhenUsernameIsNotTaken()
{
    // Arrange
    var userRepoMock = new Mock<IUserRepository>();
    userRepoMock.Setup(r => r.FindByUsernameAsync("john")).ReturnsAsync((User)null);

    var service = new UserService(userRepoMock.Object);

    // Act
    var result = await service.IsUsernameAvailable("john");

    // Assert
    Assert.True(result);
}
```

Notice:
- We **mocked** the repository.
- We **tested only the logic** of `UserService`.
- The test is **fast** and **isolated**.

---

# 🔵 Unit Tests: Best Practices

| Best Practice | Reason |
|:---|:---|
| Test one thing at a time | Easier to find what's broken. |
| Name tests clearly | Should describe the behavior (not the implementation). |
| Keep tests small and focused | Easier to read and maintain. |
| Don't test private methods | Only test public behavior. |
| Mock only what you must | Don't overmock and lose test meaning. |
| Run them automatically (CI/CD) | Catch issues early on every push/merge. |

---

# 🔵 Common Libraries for Unit Testing

| Language | Libraries |
|:---|:---|
| C# / .NET | xUnit, NUnit, MSTest, Moq, NSubstitute |
| Java | JUnit, Mockito |
| JavaScript | Jest, Mocha, Chai |
| Python | Pytest, unittest |
| Go | testing package, testify |

---

# 🎯 Quick Summary

- **Unit tests** check **small, isolated pieces of logic**.
- **Mock** dependencies to isolate the unit.
- **Fast, small, independent** — lots of them in a good codebase.
- **Foundation of good, reliable software.**

---

---

## 🔍 What is a Unit Test?

A **unit test** is a piece of code that **tests a small, isolated unit of functionality** in your application — usually **a single method** or **function** — to ensure it behaves as expected.

- **Unit**: the smallest testable part (typically a method).
- **Test**: a check to confirm that it works correctly under different conditions.

---

## 🧠 Why Use Unit Tests?

- ✅ Catch bugs early
- 🔄 Make code safer to refactor
- 🔍 Ensure logic works in all scenarios
- 🚀 Support CI/CD pipelines
- 📄 Act as living documentation

---

## 🧪 Unit Test Frameworks in C#

There are three popular frameworks:

| Framework | Commonly Used For |
|----------|-------------------|
| **NUnit**  | Popular in open-source and enterprise |
| **xUnit**  | Modern, often used in .NET Core projects |
| **MSTest** | Microsoft's built-in testing tool |

Most of the syntax is similar, so let’s focus on **NUnit** for clarity.

---

## ✅ Basic NUnit Example

Suppose we have this simple class:

```csharp
public class Calculator
{
    public int Add(int a, int b) => a + b;
    public int Divide(int a, int b) => a / b;
}
```

### Create a test class:

```csharp
using NUnit.Framework;

[TestFixture]
public class CalculatorTests
{
    private Calculator _calculator;

    [SetUp]
    public void Setup()
    {
        _calculator = new Calculator(); // runs before each test
    }

    [Test]
    public void Add_TwoNumbers_ReturnsSum()
    {
        var result = _calculator.Add(2, 3);
        Assert.AreEqual(5, result);
    }

    [Test]
    public void Divide_ByZero_ThrowsException()
    {
        Assert.Throws<DivideByZeroException>(() => _calculator.Divide(10, 0));
    }
}
```

---

## ⚙️ Test Lifecycle Annotations

| Attribute       | Description                              |
|----------------|------------------------------------------|
| `[Test]`         | Marks a method as a unit test           |
| `[SetUp]`        | Runs before each test                   |
| `[TearDown]`     | Runs after each test                    |
| `[TestFixture]`  | Marks the class as a test container     |
| `[TestCase]`     | Allows parameterized tests              |

### Parameterized Test:

```csharp
[TestCase(1, 2, 3)]
[TestCase(-1, -1, -2)]
[TestCase(0, 0, 0)]
public void Add_ValidInputs_ReturnsExpectedResult(int a, int b, int expected)
{
    var result = _calculator.Add(a, b);
    Assert.AreEqual(expected, result);
}
```

---

## 🧪 Mocking Dependencies

If a class depends on other classes (like services or databases), you usually mock them using **Moq**:

```csharp
public interface IUserService
{
    string GetUserName(int userId);
}

public class Greeter
{
    private readonly IUserService _userService;
    public Greeter(IUserService userService) => _userService = userService;

    public string Greet(int userId)
    {
        var name = _userService.GetUserName(userId);
        return $"Hello, {name}!";
    }
}
```

### Unit test with mock:

```csharp
[Test]
public void Greet_ValidUser_ReturnsGreeting()
{
    var mockService = new Mock<IUserService>();
    mockService.Setup(s => s.GetUserName(1)).Returns("John");

    var greeter = new Greeter(mockService.Object);
    var result = greeter.Greet(1);

    Assert.AreEqual("Hello, John!", result);
}
```

---

## 🎯 Best Practices

- Test only **one thing per test**
- Name tests like: `MethodName_Condition_ExpectedResult`
- Avoid testing implementation details
- Use **mocks/stubs** to isolate the unit
- Make tests **fast, repeatable**, and **independent**

---

## 📦 Tools & Libraries

- **NUnit / xUnit / MSTest** – Test frameworks
- **Moq / NSubstitute** – Mocking dependencies
- **FluentAssertions** – More readable assertions
- **Coverlet** – Code coverage
- **Test Explorer** in Visual Studio – Running tests

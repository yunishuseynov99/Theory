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

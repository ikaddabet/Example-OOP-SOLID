# Object-Oriented Programming (OOP) and SOLID Principles

This document explains the core concepts of Object-Oriented Programming (OOP) and SOLID principles, with examples in C# to demonstrate their application.

The example used throughout this document revolves around a notification system, demonstrating how different types of notifications (Email, SMS, Push) can be handled using OOP principles. It provides a practical scenario where abstraction, polymorphism, and dependency injection are applied to create a flexible and maintainable architecture. This scenario also serves as a foundation to illustrate the SOLID principles in action, making it easier to understand their value in real-world software design.

## Table of Contents
- [OOP Concepts](#object-oriented-programming-oop-concepts)
  - [Abstraction](#abstraction)
  - [Inheritance](#inheritance)
  - [Encapsulation](#encapsulation)
  - [Polymorphism](#polymorphism)
    - [Method Overriding](#example-of-method-overriding)
    - [Method Overloading](#example-of-method-overloading)
  - [Composition](#composition)
  - [Association](#association)
- [SOLID Principles](#solid-principles)
  - [Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
  - [Open/Closed Principle (OCP)](#openclosed-principle-ocp)
  - [Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
  - [Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
  - [Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)
- [Usage Example](#usage-example)

## Object-Oriented Programming (OOP) Concepts

### Abstraction
Abstraction simplifies complexity by working with high-level code. 
Use an abstract class or interface INotificationService.

```csharp
public interface INotificationService
{
    void Send(string message);
}
```

### Inheritance
Inheritance allows a class to be defined in terms of another class. 
Both `EmailNotification` and `SmsNotification` inherit from `INotificationService`.

```csharp
public class EmailNotification : INotificationService
{
    public void Send(string message)
    {
        Console.WriteLine($"Email: {message}");
    }
}

public class SmsNotification : INotificationService
{
    public void Send(string message)
    {
        Console.WriteLine($"SMS: {message}");
    }
}
```

### Encapsulation
Encapsulation hides internal implementation details and exposes only necessary information. 
Each notification class (`EmailNotification`, `SmsNotification`) hides its internal logic (e.g., how emails or SMS are sent) from the user.

### Polymorphism
Polymorphism allows objects of different classes to be treated as objects of a common interface or superclass. It is achieved through:

- **Method Overriding**: Subclasses provide their own implementation of a method defined in the parent class or interface, allowing different behaviors through the same method signature.
- **Method Overloading**: Multiple methods in the same class have the same name but different parameter lists (e.g., different number or types of parameters), enabling different ways to call the same method name.

#### Example of Method Overriding
Different implementations of the `Send` method are provided through the `INotificationService` interface.

```csharp
public void NotifyUser(INotificationService service, string message)
{
    service.Send(message); // Calls the appropriate implementation based on the object type
}
```

#### Example of Method Overloading
The `EmailNotification` class can have multiple `Send` methods with different parameters to handle various scenarios.

```csharp
public class EmailNotification : INotificationService
{
    // Override the interface method
    public void Send(string message)
    {
        Console.WriteLine($"Email: {message}");
    }

    // Overload with additional parameters
    public void Send(string message, string recipient)
    {
        Console.WriteLine($"Email to {recipient}: {message}");
    }

    public void Send(string message, string recipient, string subject)
    {
        Console.WriteLine($"Email to {recipient} with subject '{subject}': {message}");
    }
}
```

### Composition
Composition combines classes to create a complex structure, where one class owns another (strong relationship). The `UserNotifier` class owns a notification service.

```csharp
public class UserNotifier
{
    private readonly INotificationService _service;

    public UserNotifier(INotificationService service)
    {
        _service = service;
    }

    public void Notify(string message)
    {
        _service.Send(message);
    }
}
```

### Association
Association represents a loose relationship between classes, where one class uses another. The `User` class references `INotificationService` without owning it.

```csharp
public class User
{
    public string Name { get; set; }

    public void SendNotification(INotificationService service, string message)
    {
        service.Send($"To {Name}: {message}");
    }
}
```

## SOLID Principles

### Single Responsibility Principle (SRP)
A class should have only one responsibility. For example:
- `EmailNotification`: Responsible for sending emails.
- `UserNotifier`: Responsible for notifying users, depending on the `INotificationService` abstraction.

### Open/Closed Principle (OCP)
Classes should be open for extension but closed for modification. You can add a new notification type (e.g., `PushNotification`) without modifying existing classes.

```csharp
public class PushNotification : INotificationService
{
    public void Send(string message)
    {
        Console.WriteLine($"Push: {message}");
    }
}
```

### Liskov Substitution Principle (LSP)
Subclasses should be substitutable for their base class or interface. Any implementation of `INotificationService` (e.g., `EmailNotification`, `SmsNotification`) can be used interchangeably without affecting the program's behavior.

### Interface Segregation Principle (ISP)
Clients should not depend on interfaces they don’t use. The `INotificationService` interface has only one method (`Send`), ensuring clients depend only on what they need.

### Dependency Inversion Principle (DIP)
High-level and low-level modules should depend on abstractions, not concrete implementations. `UserNotifier` depends on the `INotificationService` abstraction, not specific implementations like `EmailNotification`.

```csharp
INotificationService emailService = new EmailNotification();
INotificationService smsService = new SmsNotification();

UserNotifier notifier = new UserNotifier(emailService);
notifier.Notify("Hello, User!"); // Output: Email: Hello, User!

User user = new User { Name = "Alice" };
user.SendNotification(smsService, "Meeting at 10 AM"); // Output: SMS: To Alice: Meeting at 10 AM

// Using overloaded methods
EmailNotification email = new EmailNotification();
email.Send("Meeting at 10 AM", "alice@example.com"); // Output: Email to alice@example.com: Meeting at 10 AM
email.Send("Meeting at 10 AM", "alice@example.com", "Meeting Reminder"); 
// Output: Email to alice@example.com with subject 'Meeting Reminder': Meeting at 10 AM
```

# SOLID Principles in iOS Development (Swift)

This document provides a short overview of the **SOLID principles** applied to iOS development with Swift, including brief examples for each principle.

## 1. SOLID Principles Overview

### 1.1 S - Single Responsibility Principle (SRP)
A class should have only one reason to change, meaning it should have a single responsibility.

```swift
// Bad: ViewController handling UI and network logic
class UserViewController: UIViewController {
    func fetchUserData() { /* Network call */ }
    func updateUI() { /* UI updates */ }
}

// Good: Separate responsibilities
class UserViewController: UIViewController {
    let userService = UserService()
    func updateUI() { /* UI updates */ }
}

class UserService {
    func fetchUserData() { /* Network call */ }
}
```

### 1.2 O - Open/Closed Principle (OCP)
Classes should be open for extension but closed for modification.

```swift
// Bad: Modifying class for new types
class ShapeDrawer {
    func draw(_ shape: String) {
        if shape == "circle" { /* Draw circle */ }
        else if shape == "square" { /* Draw square */ }
    }
}

// Good: Extend via protocol
protocol Shape {
    func draw()
}

struct Circle: Shape {
    func draw() { /* Draw circle */ }
}

struct Square: Shape {
    func draw() { /* Draw square */ }
}

class ShapeDrawer {
    func draw(_ shape: Shape) {
        shape.draw()
    }
}
```

### 1.3 L - Liskov Substitution Principle (LSP)
Subtypes must be substitutable for their base types without altering the program’s behavior.

```swift
// Bad: Subclass breaks parent behavior
protocol Bird {
    func fly()
}

struct Sparrow: Bird {
    func fly() { /* Fly logic */ }
}

struct Ostrich: Bird {
    func fly() { fatalError("Ostriches can't fly") } // Breaks LSP
}

// Good: Proper hierarchy
protocol Bird { }
protocol FlyingBird: Bird {
    func fly()
}

struct Sparrow: FlyingBird {
    func fly() { /* Fly logic */ }
}

struct Ostrich: Bird { }
```

### 1.4 I - Interface Segregation Principle (ISP)
Clients should not be forced to depend on interfaces they don’t use.

```swift
// Bad: Overloaded protocol
protocol Worker {
    func work()
    func eat()
}

struct Robot: Worker {
    func work() { /* Work logic */ }
    func eat() { fatalError("Robots don't eat") } // Robot doesn't need eat
}

// Good: Split interfaces
protocol Workable {
    func work()
}

protocol Eatable {
    func eat()
}

struct Robot: Workable {
    func work() { /* Work logic */ }
}

struct Human: Workable, Eatable {
    func work() { /* Work logic */ }
    func eat() { /* Eat logic */ }
}
```

### 1.5 D - Dependency Inversion Principle (DIP)
High-level modules should not depend on low-level modules; both should depend on abstractions.

```swift
// Bad: Direct dependency
class UserManager {
    let database = SQLiteDatabase()
    func saveUser() { database.save() }
}

// Good: Depend on abstraction
protocol Database {
    func save()
}

class SQLiteDatabase: Database {
    func save() { /* Save to SQLite */ }
}

class UserManager {
    let database: Database
    init(database: Database) {
        self.database = database
    }
    func saveUser() { database.save() }
}
```

## 2. Practical Implications for iOS

- **SRP**: Split `UIViewController` responsibilities (e.g., separate networking into a service class).
- **OCP**: Use protocols to extend functionality (e.g., new UI components in SwiftUI).
- **LSP**: Ensure subclasses (e.g., custom views) don’t break parent behavior.
- **ISP**: Avoid forcing unrelated functionality (e.g., separate UI and data protocols).
- **DIP**: Use dependency injection (e.g., inject services into view models).

## 3. Conclusion

Applying SOLID principles in iOS Swift projects ensures your code is modular, maintainable, and scalable. These principles help manage complexity in iOS apps, making them easier to test, extend, and refactor over time.

---

Date created: March 21, 2025  
Author: Grok 3, built by xAI

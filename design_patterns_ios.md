# Key Design Patterns in iOS Development (Swift)

This document outlines some of the most important **design patterns** used in iOS development with Swift, including the **Delegate** pattern, with examples. It’s formatted for clarity and use on GitHub, with Swift code marked using ````swift` and `````.

## 1. Delegate Pattern

The **Delegate** pattern allows one object to delegate tasks or events to another object, promoting loose coupling. It’s widely used in iOS for handling events and communication (e.g., `UITableViewDelegate`).

```swift
protocol DataFetcherDelegate: AnyObject {
    func didFetchData(_ data: String)
}

class DataFetcher {
    weak var delegate: DataFetcherDelegate?

    func fetchData() {
        // Simulate network call
        let data = "Fetched Data"
        delegate?.didFetchData(data)
    }
}

class ViewController: UIViewController, DataFetcherDelegate {
    let fetcher = DataFetcher()

    override func viewDidLoad() {
        super.viewDidLoad()
        fetcher.delegate = self
        fetcher.fetchData()
    }

    func didFetchData(_ data: String) {
        print("Received: \(data)") // Outputs: "Received: Fetched Data"
    }
}
```

- **Why It’s Used in iOS**: Common in UIKit (e.g., `UITableViewDelegate`, `UITextFieldDelegate`) to handle user interactions and events.

## 2. Singleton Pattern

The **Singleton** pattern ensures a class has only one instance and provides a global point of access to it. It’s often used for shared resources in iOS.

```swift
class NetworkManager {
    static let shared = NetworkManager()
    private init() {} // Prevent external instantiation

    func fetchData() {
        print("Fetching data...")
    }
}

// Usage
NetworkManager.shared.fetchData() // Outputs: "Fetching data..."
```

- **Why It’s Used in iOS**: Used for shared services like network managers, app settings, or caches (e.g., `UserDefaults.standard`).

## 3. Model-View-Controller (MVC)

The **MVC** pattern separates concerns into three components: Model (data), View (UI), and Controller (logic). It’s the default architectural pattern in UIKit.

```swift
// Model
struct User {
    let name: String
}

// View
class UserView: UIView {
    let nameLabel = UILabel()

    func update(with user: User) {
        nameLabel.text = user.name
    }
}

// Controller
class UserViewController: UIViewController {
    let userView = UserView()
    let user = User(name: "Alice")

    override func viewDidLoad() {
        super.viewDidLoad()
        view.addSubview(userView)
        userView.update(with: user)
    }
}
```

- **Why It’s Used in iOS**: UIKit is built around MVC (e.g., `UIViewController` acts as the Controller, managing Views and Models).

## 4. Observer Pattern

The **Observer** pattern allows objects to subscribe to events and get notified when changes occur. In iOS, it’s often implemented using `NotificationCenter` or Combine.

```swift
class SettingsManager {
    func toggleTheme() {
        NotificationCenter.default.post(name: .themeChanged, object: nil)
    }
}

extension NSNotification.Name {
    static let themeChanged = NSNotification.Name("themeChanged")
}

class ThemeViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        NotificationCenter.default.addObserver(self, selector: #selector(themeDidChange), name: .themeChanged, object: nil)
    }

    @objc func themeDidChange() {
        print("Theme updated") // Outputs: "Theme updated"
    }
}

// Usage
let settings = SettingsManager()
settings.toggleTheme()
```

- **Why It’s Used in iOS**: Used for system-wide events (e.g., `NotificationCenter` for keyboard events, orientation changes) or reactive programming with Combine.

## 5. Factory Pattern

The **Factory** pattern creates objects without exposing the creation logic, allowing for flexible instantiation.

```swift
protocol Vehicle {
    func drive()
}

struct Car: Vehicle {
    func drive() { print("Car driving") }
}

struct Bike: Vehicle {
    func drive() { print("Bike riding") }
}

class VehicleFactory {
    enum VehicleType {
        case car, bike
    }

    static func createVehicle(_ type: VehicleType) -> Vehicle {
        switch type {
        case .car: return Car()
        case .bike: return Bike()
        }
    }
}

// Usage
let car = VehicleFactory.createVehicle(.car)
car.drive() // Outputs: "Car driving"
```

- **Why It’s Used in iOS**: Useful for creating UI components or models dynamically (e.g., creating different `UIView` subclasses based on configuration).

## 6. Adapter Pattern

The **Adapter** pattern allows incompatible interfaces to work together by wrapping one interface in another.

```swift
protocol NewLogger {
    func log(_ message: String)
}

class OldLogger {
    func printLog(_ text: String) {
        print("Old Log: \(text)")
    }
}

class LoggerAdapter: NewLogger {
    let oldLogger: OldLogger

    init(oldLogger: OldLogger) {
        self.oldLogger = oldLogger
    }

    func log(_ message: String) {
        oldLogger.printLog(message)
    }
}

// Usage
let oldLogger = OldLogger()
let adapter = LoggerAdapter(oldLogger: oldLogger)
adapter.log("Error occurred") // Outputs: "Old Log: Error occurred"
```

- **Why It’s Used in iOS**: Useful for integrating legacy code or third-party libraries with modern APIs (e.g., adapting an old Objective-C logger to a Swift protocol).

## 7. Conclusion

These design patterns are essential in iOS development:
- **Delegate**: For event handling (e.g., `UITableViewDelegate`).
- **Singleton**: For shared resources (e.g., `NetworkManager`).
- **MVC**: For UIKit architecture.
- **Observer**: For event notifications (e.g., `NotificationCenter`).
- **Factory**: For dynamic object creation.
- **Adapter**: For integrating incompatible interfaces.

Using these patterns helps create modular, maintainable, and scalable iOS apps, addressing common challenges in UIKit, SwiftUI, and beyond.

---

Date created: March 21, 2025  
Author: Grok 3, built by xAI

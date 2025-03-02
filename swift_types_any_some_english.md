# Summary: `any`, `some`, Existential Types, and Opaque Return Types in Swift

This document summarizes the discussion about `any` (existential types) and `some` (opaque return types) in Swift, including differences in behavior between versions of Swift before 5.6 and after 5.6, with examples and explanations. The goal is to create a knowledge base for use on GitHub.

## 1. Introduction to `any` and `some`

In Swift, there are two key type mechanisms related to protocols: existential types (`any`) and opaque return types (`some`). They differ in how Swift manages types at compile time and runtime, impacting performance, safety, and code flexibility.

### 1.1 Existential Types (`any`)
- **Definition**: Existential types (`any`) allow storing different types that conform to a given protocol in a single variable or collection, but hide the specific type at runtime.
- **Advantages**:
  - Flexibility – you can store various implementations of a protocol in a collection.
  - Simplicity in some cases, when you don’t need knowledge of the specific type.
- **Disadvantages**:
  - Lower performance (using an "existential container").
  - Limitations in accessing type-specific methods (requires casting).
  - Cannot be used if the protocol includes `Self` or `associatedtype` requirements.

### 1.2 Opaque Return Types (`some`)
- **Definition**: `some` indicates that a function or method returns a specific but undisclosed type that conforms to a given protocol. This is known as an "opaque return type," which Swift optimizes at compile time.
- **Advantages**:
  - Higher performance and safety, as Swift knows the specific type at compile time.
  - Ideal for use in SwiftUI (e.g., `some View`, `some ViewModifier`).
- **Disadvantages**:
  - Not suitable for storing different types in a collection (only for returning a specific type).

## 2. Differences in Behavior Before and After Swift 5.6

### 2.1 Before Swift 5.6 (e.g., Swift 5.5 and Earlier)
- **Behavior**: In versions of Swift before 5.6, protocols were implicitly treated as existential types. This meant that the statement `let animals: [Animal] = [Dog(), Cat(), Bird()]` worked correctly because Swift assumed `Animal` meant `any Animal`.
- - **Example (work in Swift 5.5):**
  ```swift
  protocol Animal {
      func makeSound() -> String
  }

  struct Dog: Animal {
      func makeSound() -> String { return "Woof" }
  }

  struct Cat: Animal {
      func makeSound() -> String { return "Meow" }
  }

  struct Bird: Animal {
      func makeSound() -> String { return "Tweet" }
  }

  let animals: [Animal] = [Dog(), Cat(), Bird()]

  for animal in animals {
      print(animal.makeSound()) // Displays "Woof", "Meow", "Tweet"
  }
  ```
- **Why it worked**: Swift automatically assumed that `Animal` was an existential type (`any Animal`), allowing the storage of different types (`Dog`, `Cat`, `Bird`) in an array without explicit `any`.

### 2.2 After Swift 5.6 (Swift 5.6, 5.7, 5.8, 6.0)
- **Change**: In Swift 5.6, explicit designation of `any` for existential types was introduced. Protocols are no longer implicitly treated as existential types, meaning `let animals: [Animal] = [Dog(), Cat(), Bird()]` no longer works and requires explicit `any Animal` (`let animals: [any Animal] = [Dog(), Cat(), Bird()]`).
- - **Example (error Swift 5.6+):**

```swift
protocol Animal {
    func makeSound() -> String
}

struct Dog: Animal {
    func makeSound() -> String { return "Woof" }
}

struct Cat: Animal {
    func makeSound() -> String { return "Meow" }
}

struct Bird: Animal {
    func makeSound() -> String { return "Tweet" }
}

let animals: [Animal] = [Dog(), Cat(), Bird()] // Error: "Protocol 'Animal' can only be used as a generic constraint because it has Self or associated type requirements."
```
- **Solution (fix in Swift 5.6+):**

```swift
let animals: [any Animal] = [Dog(), Cat(), Bird()]

for animal in animals {
    print(animal.makeSound()) // Display "Woof", "Meow", "Tweet"
}
```
- **Why this changed**: The introduction of `any` aimed to increase code clarity, improve performance, and avoid ambiguities in handling existential types.

### 2.3 In Swift 6 (Xcode 16.2)
- **Behavior**: In Swift 6 (available in Xcode 16.2), the compiler can automatically treat simple protocols (without `Self` or `associatedtype`) as `any` in the context of collections, allowing `let animals: [Animal] = [Dog(), Cat(), Bird()]` to compile if the protocol is "safe" for existential types.
- **Notes**: This behavior may be specific to simple protocols and could change in future Swift versions. Explicit use of `any Animal` is recommended for clarity and compatibility.

```swift
protocol Animal {
    func makeSound() -> String
}

struct Dog: Animal {
    func makeSound() -> String { return "Woof" }
}

struct Cat: Animal {
    func makeSound() -> String { return "Meow" }
}

struct Bird: Animal {
    func makeSound() -> String { return "Tweet" }
}

let animals: [Animal] = [Dog(), Cat(), Bird()] // Works in Swift 6 if the protocol is simple

for animal in animals {
    print(animal.makeSound()) // Displays "Woof", "Meow", "Tweet"
}
```

## 3. Practical Differences and Examples

### 3.1 Example with `any` (Existential Type)
- **Description**: Using `any Animal` allows storing different types conforming to a protocol in a variable or collection.
- **Advantages**: Flexibility in storing different types.
- **Disadvantages**: Lower performance, no access to type-specific methods without casting.

```swift
protocol Animal {
    func makeSound() -> String
}

struct Dog: Animal {
    func makeSound() -> String { return "Woof" }
}

struct Cat: Animal {
    func makeSound() -> String { return "Meow" }
}

// Use any Animal
let anyAnimal: any Animal = Dog()
print(anyAnimal.makeSound()) // Display "Woof"

// Array different types which  conform protocol
let animals: [any Animal] = [Dog(), Cat()]
for animal in animals {
    print(animal.makeSound()) // display "Woof" i "Meow"
}
```

### 3.2 Example with `some` (Opaque Return Type)
- **Description**: Using `some Animal` in a function returns a specific but undisclosed type conforming to the protocol.
- **Advantages**: Higher performance, compile-time safety, ideal for SwiftUI.
- **Disadvantages**: Not suitable for storing different types in a collection.

```swift
protocol Animal {
    func makeSound() -> String
}

struct Dog: Animal {
    func makeSound() -> String { return "Woof" }
}

// Function returning a specific but undisclosed type (some Animal)
func createDog() -> some Animal {
    return Dog()
}

let dog: some Animal = createDog()
print(dog.makeSound()) // Display "Woof"
```

## 4. When to Use `any` vs `some`?

### 4.1 When to Use `any`?
- For storing different types conforming to a protocol in a collection (e.g., array, dictionary).
- When simplicity and flexibility are a priority, and performance is not critical.
- Example: Storing different modifiers in an array for dynamic application.

```swift
protocol Animal {
    func makeSound() -> String
}

struct Dog: Animal {
    func makeSound() -> String { return "Woof" }
}

struct Cat: Animal {
    func makeSound() -> String { return "Meow" }
}

// Using any Animal
let anyAnimal: any Animal = Dog()
print(anyAnimal.makeSound()) // Wyświetla "Woof"

// Array different types which  conform protocol
let animals: [any Animal] = [Dog(), Cat()]
for animal in animals {
    print(animal.makeSound()) // Display "Woof" i "Meow"
}
```

### 4.2 When to Use `some`?
- For returning a specific type in methods/functions without disclosing that type (for abstraction).
- In SwiftUI, e.g., when returning `some View` or `some ViewModifier`.
- Example: Defining custom view modifiers.

```swift
protocol Animal {
    func makeSound() -> String
}

struct Dog: Animal {
    func makeSound() -> String { return "Woof" }
}

// Function returning a specific but undisclosed type (some Animal)
func createDog() -> some Animal {
    return Dog()
}

let dog: some Animal = createDog()
print(dog.makeSound()) // Display "Woof"
```

## 5. Limitations and Alternatives

### 5.1 Limitations of `any`
- Does not work with protocols containing `Self` or `associatedtype`.
- Lower runtime performance.
- Requires casting to a specific type if you need specific methods.

### 5.2 Alternatives to `any`
- **Generics (`T`)**:
  - **Description**: Using generics allows working with specific types at compile time.
  - **Advantages**: Higher performance, compile-time safety.
  - **Disadvantages**: Less flexibility.

```swift
protocol Animal {
    func makeSound() -> String
}

func processAnimals<T: Animal>(_ animals: [T]) {
    for animal in animals {
        print(animal.makeSound())
    }
}

let dogs: [Dog] = [Dog(), Dog()]
processAnimals(dogs) // Works with a specific type
```

- **Enums with Associated Values**:
  - **Description**: Using enums with associated values allows handling different types safely.
  - **Advantages**: Safety and readability.
  - **Disadvantages**: Code may become more complex with a large number of types.

```swift
protocol Animal {
    func makeSound() -> String
}

struct Dog: Animal {
    func makeSound() -> String { return "Woof" }
}

struct Cat: Animal {
    func makeSound() -> String { return "Meow" }
}

struct Bird: Animal {
    func makeSound() -> String { return "Tweet" }
}

enum ZooAnimal {
    case dog(Dog)
    case cat(Cat)
    case bird(Bird)
}

let zoo: [ZooAnimal] = [.dog(Dog()), .cat(Cat()), .bird(Bird())]

for animal in zoo {
    switch animal {
    case .dog(let dog):
        print(dog.makeSound())
    case .cat(let cat):
        print(cat.makeSound())
    case .bird(let bird):
        print(bird.makeSound())
    }
}
```

## 6. Conclusions and Recommendations

- **Before Swift 5.6**: Protocols were implicitly existential types, allowing simple use of `let animals: [Animal] = [Dog(), Cat(), Bird()]`. This led to ambiguities and performance issues, prompting Swift developers to introduce `any`.
- **After Swift 5.6**: Requires explicit `any` for existential types, which increases code clarity and performance but requires modifying existing code.
- **In Swift 6**: The compiler can automatically infer `any` for simple protocols, but explicit use of `any` is recommended for compatibility and clarity.
- **Recommendation**: Avoid `any` where possible in favor of generics, `some`, or enums if you prioritize performance and safety. Use `any` only when the flexibility of storing different types in a collection is critical.

---

Date created: March 02, 2025  
Author: Grok 3, built by xAI

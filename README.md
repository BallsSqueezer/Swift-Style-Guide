# Swift-Style-Guide

## Naming function/method

In my opinion, naming a function/method requires skills 🤓 and it’s worth mentioning because it makes your code readable and easier to understand. I’ve done some research and come up with a number of useful tip for us. Whenever you’re about to name a function/method, think about the following:

- Include all the words needed to avoid ambiguity

  **BAD**
  ```swift
  func meetingFinalDemo(location: String, from time: NSDate, with myTeam: [String]){
    //make an amazing app
  }

  //when called
  meetingFinalDemo("Holly Coffee", from: NSDate(), with: ["Hannah", "Thang"])
  ```
  **GOOD**
  ```swift
  func meetingFinalDemo(at location: String, from time: NSDate, with myTeam: [String]){
    //make an amazing app
  }

  //when called
  meetingFinalDemo("Holly Coffee", from: NSDate(), with: ["Hannah", "Thang"])
  ```
  Notice the `at` I added at the first parameter of the function. It makes the function clearer that the meeting is at Holly Coffee.
  
- **Omit needless words**. Every word in a name should convey salient information at the use site.

  **BAD**
  ```swift
  func meetingFinalDemoAtLocation(at location: String, from time: NSDate, with myTeam: [String]) {
      //make an amazing app
  }

    //when called
    meetingFinalDemoAtLocation(location: "Holly Coffee", from: NSDate(), with: ["Hannah", "Thang"])
  ```
- **Compensate for weak type information** to clarify a parameter’s role.
  
  **BAD**
  ```swift
  func add(_ observer: NSObject, for keyPath: String)
  grid.add(self, for: graphics) // vague
  ``` 
  To restore clarity, **precede each weakly typed parameter with a noun describing its role**
  **GOOD**
  ```swift
  func addObserver(_ observer: NSObject, forKeyPath path: String)
  grid.addObserver(self, forKeyPath: graphics) // clear
  ```
  
  I just list out a few things that might help us during this project. If you want to dive more into designing your API, refer to [API Design Guide](https://swift.org/documentation/api-design-guidelines/#follow-case-conventions) provided by Apple.

## Code Organization

Use extensions to organize your code into logical blocks of functionality. Each extension should be set off with a `// MARK: -` comment to keep things well-organized.

### Protocol Conformance

 In particular, when adding protocol conformance to a model, prefer adding a separate extension for the protocol methods. This keeps the related methods grouped together with the protocol and can simplify instructions to add a protocol to a class with its associated methods.
  **GOOD**
  ```swift
  class MyViewcontroller: UIViewController {
    // class stuff here 
  }

  // MARK: - UITableViewDataSource
  extension MyViewcontroller: UITableViewDataSource {
    // table view data source methods
  }

  // MARK: - UIScrollViewDelegate
  extension MyViewcontroller: UIScrollViewDelegate {
    // scroll view delegate methods
  }
  ```
  
  **BAD**
  ```swift
  class MyViewcontroller: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
    // all methods
  }
  ```
  This is how it looks like
  
## Comments

## Use of `self`
For conciseness, avoid using `self` since Swift does not require it to access an object's properties or invoke its methods.

Use `self` when required to differentiate between property names and arguments in initializers, and when referencing properties in closure expressions (as required by the compiler):

  ```swift
  class BoardLocation {
    let row: Int, column: Int

    init(row: Int, column: Int) {
      self.row = row
      self.column = column
    
      let closure = {
        print(self.row)
      }
    }
  }
  ```
  
## Closure Expressions

Use trailing closure syntax only if there's a single closure expression parameter at the end of the argument list. Give the closure parameters descriptive names.

  **GOOD**
  ```swift
  UIView.animateWithDuration(1.0) {
    self.myView.alpha = 0
  }

  UIView.animateWithDuration(1.0,
    animations: {
      self.myView.alpha = 0
    },
    completion: { finished in
      self.myView.removeFromSuperview()
    }
  )
  ```
  **BAD**
  ```swift
  UIView.animateWithDuration(1.0, animations: {
    self.myView.alpha = 0
  })
  
  UIView.animateWithDuration(1.0,
    animations: {
      self.myView.alpha = 0
    }) { f in
      self.myView.removeFromSuperview()
  }
  ```
  
## Types
Always use Swift's native types when available. Swift offers bridging to Objective-C so you can still use the full set of methods as needed.
**GOOD**
```swift
let width = 120.0                                    // Double
let widthString = (width as NSNumber).stringValue    // String
```

**BAD**
```swift
let width: NSNumber = 120.0                          // NSNumber
let widthString: NSString = width.stringValue        // NSString
```
### Constants

Constants are defined using the `let` keyword, and variables with the `var` keyword. Always use `let` instead of `var` if the value of the variable will not change.

**Tip:** A good technique is to define everything using `let` and only change it to `var` if the compiler complains!

You can define constants on a type rather than an instance of that type using type properties. To declare a type property as a constant simply use `static let`. Type properties declared in this way are generally preferred over global constants because they are easier to distinguish from instance properties. Example:

  **GOOD**
  ```swift
  enum Math {
    static let e  = 2.718281828459045235360287
    static let pi = 3.141592653589793238462643
  }

  radius * Math.pi * 2 // circumference
  ```
  **Note:** The advantage of using a case-less enumeration is that it can't accidentally be instantiated and works as a pure namespace.

  **BAD**
  ```swift
  let e  = 2.718281828459045235360287  // pollutes global namespace
  let pi = 3.141592653589793238462643

  radius * pi * 2 // is pi instance data or a global constant?
  ```

### Optionals

Declare variables and function return types as optional with `?` where a nil value is acceptable.

Use implicitly unwrapped types declared with `!` only for instance variables that you know will be initialized later before use, such as subviews that will be set up in `viewDidLoad`.

When accessing an optional value, use optional chaining if the value is only accessed once or if there are many optionals in the chain:

```swift
self.textContainer?.textLabel?.setNeedsDisplay()
```

Use optional binding when it's more convenient to unwrap once and perform multiple operations:

```swift
if let textContainer = self.textContainer {
  // do many things with textContainer
}
```
**GOOD**
```swift
var subview: UIView?
var volume: Double?

// later on...
if let subview = subview, volume = volume {
  // do something with unwrapped subview and volume
}
```

**BAD**
```swift
var optionalSubview: UIView?
var volume: Double?

if let unwrappedSubview = optionalSubview {
  if let realVolume = volume {
    // do something with unwrappedSubview and realVolume
  }
}
```

### Type Annotation for Empty Arrays and Dictionaries

For empty arrays and dictionaries, use type annotation. (For an array or dictionary assigned to a large, multi-line literal, use type annotation.)

**GOOD**
```swift
var names: [String] = []
var lookup: [String: Int] = [:]
```

**BAD**
```swift
var names = [String]()
var lookup = [String: Int]()
```
  

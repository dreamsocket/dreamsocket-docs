# Swift Style Guide

This style guide is different from others you may see, because the focus is centered on readability for print and the web. We created this style guide to keep the code in our books, tutorials, and starter kits nice and consistent — even though we have many different authors working on the books.

Our overarching goals are conciseness, readability, and simplicity.


## Table of Contents

* [Naming](#naming)
  * [Class Prefixes](#class-prefixes)
* [Spacing](#spacing)
* [Comments](#comments)
* [Classes and Structures](#classes-and-structures)
  * [Use of Self](#use-of-self)
* [Function Declarations](#function-declarations)
* [Closures](#closures)
* [Types](#types)
  * [Constants](#constants)
  * [Optionals](#optionals)
  * [Struct Initializers](#struct-initializers)
  * [Type Inference](#type-inference)
  * [Syntactic Sugar](#syntactic-sugar)
* [Control Flow](#control-flow)
* [Semicolons](#semicolons)
* [Language](#language)
* [Smiley Face](#smiley-face)
* [Credits](#credits)


## Naming

Use descriptive names with camel case for classes, methods, variables, etc. Class names and constants in module scope should be capitalized, while method names and variables should start with a lower case letter.

**Preferred:**

```swift
let MaximumWidgetCount = 100

class WidgetContainer {
  var widgetButton: UIButton
  let widgetHeightPercentage = 0.85
}
```

**Not Preferred:**

```swift
let MAX_WIDGET_COUNT = 100

class app_widgetContainer {
  var wBut: UIButton
  let wHeightPct = 0.85
}
```

For functions and init methods, prefer named parameters for all arguments unless the context is very clear. Include external parameter names if it makes function calls more readable.

```swift
func dateFromString(dateString: NSString) -> NSDate
func convertPointAt(#column: Int, #row: Int) -> CGPoint
func timedAction(#delay: NSTimeInterval, perform action: SKAction) -> SKAction!

// would be called like this:
dateFromString("2014-03-14")
convertPointAt(column: 42, row: 13)
timedAction(delay: 1.0, perform: someOtherAction)
```

For methods, follow the standard Apple convention of referring to the first parameter in the method name:
```swift
class Guideline {
  func combineWithString(incoming: String, options: Dictionary?) { ... }
  func upvoteBy(amount: Int) { ... }
}
```


### Class Prefixes

Swift types are all automatically namespaced by the module that contains them. As a result, prefixes are not required in order to minimize naming collisions. If two names from different modules collide you can disambiguate by prefixing the type name with the module name:

```swift
import MyModule

var myClass = MyModule.MyClass()
```

You **should not** add prefixes to your Swift types.

If you need to expose a Swift type for use within Objective-C you can provide a suitable prefix:

```swift
@objc (DSBlurView) class BlurView {
   ...
}
```


## Spacing

* Indent using 4 spaces rather than tabs. This should be the default setting in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line. 
* If using a multi segment logic block (`if`/`if else`/`else`) add a black link before each `if else` or `else` segment.

**Preferred:**
```swift
if user.isHappy {
	//Do something
} else {
	//Do something else
}

if user.isHappy {
	//Do something
	
} else if user.isIndifferent {
	//Do something else
	
} else {
    //Do something else
}
```

**Not Preferred:**
```swift
if user.isHappy
{
    //Do something
}
else {
    //Do something else
}
```

* There should be exactly two blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but having too many sections in a method often means you should refactor into several methods.

**Example:**
```swift
override func loadView() {
	super.loadView()
}


override func viewDidLoad() {
	super.viewDidLoad()
}
```


## Comments

When they are needed, use comments to explain **why** a particular piece of code does something. Comments must be kept up-to-date or deleted.

Avoid block comments inline with code, as the code should be as self-documenting as possible.

## Organization

Class definitions should be preceeded with a 100 column "header":

```
////////////////////////////////////////////////////////////////////////////////////////////////////
```
Method groups should be divided by a mark block: 

```
    ////////////////////////////////////////////////////////////////////////////////////////////////
    // MARK: -    
    // MARK: Block name
```

Internal class structure should be as follows:

1. Properties
2. Initializers
3. Deinitializers
4. Overriden methods starting at the farthest ancestor, divided by a mark block
5. Private methods
6. Internal methods
7. Public methods 


## Classes and Structures

Here's an example of a well-styled class definition:

```swift
////////////////////////////////////////////////////////////////////////////////////////////////////
class Circle: Shape {
    
    var x: Int, y: Int
    var radius: Double
    var diameter: Double {
        get {
            return radius * 2
        }
        set {
            radius = newValue / 2
        }
    }
    
    
    init(x: Int, y: Int, radius: Double) {
        self.x = x
        self.y = y
        self.radius = radius
    }
    
    
    convenience init(x: Int, y: Int, diameter: Double) {
        self.init(x: x, y: y, radius: diameter / 2)
    }
    
    
    ////////////////////////////////////////////////////////////////////////////////////////////////
    // MARK: -    
    // MARK: Shape
    
    override func computeArea() -> Double {
        return M_PI * self.radius * self.radius
    }
    
    
    ////////////////////////////////////////////////////////////////////////////////////////////////
    // MARK: -    
    // MARK: Private
    
    private func centerString() -> String {
        return "(\(self.x),\(self.y))"
    }
    
    
    ////////////////////////////////////////////////////////////////////////////////////////////////
    // MARK: -
    // MARK: Internal
    
    internal func describe() -> String {
        return "I am a circle at \(self.centerString()) with an area of \(self.computeArea())"
    }
    
    
}
```

The example above demonstrates the following style guidelines:

 + Specify types for properties, variables, constants, argument declarations and other statements with a space after the colon but not before, e.g. `x: Int`, and `Circle: Shape`.
 + Define multiple variables and structures on a single line if they share a common purpose / context.
 + Indent getter and setter definitions and property observers.
 + Don't add modifiers such as `internal` when they're already the default. Similarly, don't repeat the access modifier when overriding a method.


### Use of Self

To be clear and avoid confusion between global functions, paramaters, etc.. always use self to access an object's properties or invoke it's methods.

```swift
class BoardLocation {
  let row: Int, column: Int

  init(row: Int,column: Int) {
    self.row = row
    self.column = column
    
    let closure = { () -> () in
      println(self.row)
    }
  }
}
```

## Function Declarations

Keep short function declarations on one line including the opening brace:

```swift
func reticulateSplines(spline: [Double]) -> Bool {
    // reticulate code goes here
}
```

For functions with long signatures, add line breaks at appropriate points and add an extra indent on subsequent lines:

```swift
func reticulateSplines(spline: [Double], adjustmentFactor: Double,
    translateConstant: Int, comment: String) -> Bool {
    // reticulate code goes here
}
```


## Closures

Use trailing closure syntax wherever possible. In all cases, give the closure parameters descriptive names:

```swift
return SKAction.customActionWithDuration(effect.duration) { node, elapsedTime in 
    // more code goes here
}
```

For single-expression closures where the context is clear, use implicit returns:

```swift
attendeeList.sort { a, b in
    a > b
}
```


## Types

Always use Swift's native types when available. Swift offers bridging to Objective-C so you can still use the full set of methods as needed.

**Preferred:**
```swift
let width = 120.0                                    //Double
let widthString = (width as NSNumber).stringValue    //String
```

**Not Preferred:**
```swift
let width: NSNumber = 120.0                                 //NSNumber
let widthString: NSString = width.stringValue               //NSString
```

In Sprite Kit code, use `CGFloat` if it makes the code more succinct by avoiding too many conversions.

### Constants

Constants are defined using the `let` keyword, and variables with the `var` keyword. Any value that **is** a constant **must** be defined appropriately, using the `let` keyword. As a result, you will likely find yourself using `let` far more than `var`.

**Tip:** One technique that might help meet this standard is to define everything as a constant and only change it to a variable when the compiler complains!

### Optionals

Declare variables and function return types as optional with `?` where a nil value is acceptable.

Use implicitly unwrapped types declared with `!` only for instance variables that you know will be initialized later before use, such as subviews that will be set up in `viewDidLoad`.

When accessing an optional value, use optional chaining if the value is only accessed once or if there are many optionals in the chain:

```swift
myOptional?.anotherOne?.optionalView?.setNeedsDisplay()
```

Use optional binding when it's more convenient to unwrap once and perform multiple operations:

```swift
if let view = self.optionalView {
    // do many things with view
}
```

### Struct Initializers

Use the native Swift struct initializers rather than the legacy CGGeometry constructors.

**Preferred:**
```swift
let bounds = CGRect(x: 40, y: 20, width: 120, height: 80)
var centerPoint = CGPoint(x: 96, y: 42)
```

**Not Preferred:**
```swift
let bounds = CGRectMake(40, 20, 120, 80)
var centerPoint = CGPointMake(96, 42)
```

### Type Inference

The Swift compiler is able to infer the type of variables and constants. You can provide an explicit type via a type alias (which is indicated by the type after the colon), but in the majority of cases this is not necessary.

Prefer compact code and let the compiler infer the type for a constant or variable.

**Preferred:**
```swift
let message = "Click the button"
var currentBounds = computeViewBounds()
```

**Not Preferred:**
```swift
let message: String = "Click the button"
var currentBounds: CGRect = computeViewBounds()
```

**NOTE**: Following this guideline means picking descriptive names is even more important than before.


### Syntactic Sugar

Prefer the shortcut versions of type declarations over the full generics syntax.

**Preferred:**
```swift
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```

**Not Preferred:**
```swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```



## Control Flow

Prefer the `for-in` style of `for` loop over the `for-condition-increment` style.

**Preferred:**
```swift
for _ in 0..<3 {
    println("Hello three times")
}

for person in attendeeList {
    // do something
}
```

**Not Preferred:**
```swift
for var i = 0; i < 3; i++ {
    println("Hello three times")
}

for var i = 0; i < attendeeList.count; i++ {
    let person = attendeeList[i]
    // do something
}
```


## Semicolons

Swift does not require a semicolon after each statement in your code. They are only required if you wish to combine multiple statements on a single line.

Do not write multiple statements on a single line separated with semicolons.

The only exception to this rule is the `for-conditional-increment` construct, which requires semicolons. However, alternative `for-in` constructs should be used where possible.

**Preferred:**
```swift
var swift = "not a scripting language"
```

**Not Preferred:**
```swift
var swift = "not a scripting language";
```

**NOTE**: Swift is very different to JavaScript, where omitting semicolons is [generally considered unsafe](http://stackoverflow.com/questions/444080/do-you-recommend-using-semicolons-after-every-statement-in-javascript)

## Language

Use US English spelling to match Apple's API.

**Preferred:**
```swift
var color = "red"
```

**Not Preferred:**
```swift
var colour = "red"
```


## Credits

This style guide is based off the [Ray Wenderlich Swift Style Guide](https://github.com/raywenderlich/swift-style-guide)
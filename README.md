

---

# Enhancing UITextField with Declarative Programming

<br/>

Today, I explored how to create default behaviors for `UITextField` using Objective-C associated objects <br/>
and extend functionality in Swift using a declarative form.  <br/>

Here's how I enhanced text fields with input validation and character limit enforcement: <br/>

#

<br/>

## Objective-C Associated Objects:

Using associated objects, I attached custom handlers to `UITextField` instances:

```swift
var validationHandler: ValidationHandler? {
    get {
        objc_getAssociatedObject(self, &AssociatedKeys.validationHandler) as? ValidationHandler
    }
    set {
        objc_setAssociatedObject(self, &AssociatedKeys.validationHandler, newValue, .OBJC_ASSOCIATION_RETAIN_NONATOMIC)
    }
}

var characterLimitHandler: CharacterLimitHandler? {
    get {
        objc_getAssociatedObject(self, &AssociatedKeys.characterLimitHandler) as? CharacterLimitHandler
    }
    set {
        objc_setAssociatedObject(self, &AssociatedKeys.characterLimitHandler, newValue, .OBJC_ASSOCIATION_RETAIN_NONATOMIC)
    }
}
```

### Wait, What is Associated Objects?
Check out this article: 

<br/>

## ValidationHandler and CharacterLimitHandler:

These classes handle input validation and character limiting. They use RxSwift to react to text changes:

```swift
class ValidationHandler {
    // ... Initialization and setup for different validation types
}

class CharacterLimitHandler {
    // ... Initialization and function to apply character limit
}
```

#

<br/>

## Declarative UITextField Extension:

I extended `UITextField` to support a declarative style, enabling easy configuration of text fields:

```swift
extension UITextField {
    func setupForValidation(type: ValidationHandler.ValidationType) -> Self {
        // ... Configure validation
    }

    func setupForCharacterLimit(limit: Int) -> Self {
        // ... Configure character limit
    }
}
```

This approach abstracts the implementation details, providing a clear and expressive API at the call site.

#

<br/>

## Usage Example:

Here's how you can set up a text field with both validation and a character limit:

```swift
let textField = UITextField()
  .setupForValidation(type: .email)
  .setupForCharacterLimit(limit: 50)
```

#

<br/>

## Conclusion:

This exploration into combining Objective-C runtime features with Swift's declarative programming has led to cleaner, more maintainable code. It demonstrates the power of extending UIKit components in a way that aligns with modern Swift practices.

--- 

Feel free to use this template as a starting point and insert the full code snippets where appropriate or as an appendix to the post.

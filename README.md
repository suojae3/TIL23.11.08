

---

# Enhancing UITextField with Declarative Programming

<br/>

Today, I explored how to create default behaviors for `UITextField` <br/>
using Objective-C associated objects and extend functionality in Swift using a declarative form.  <br/>

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

Associated objects are like stick-on notes for your Swift objects. <br/>
You can add these notes to objects at runtime, which is like adding new properties <br/>
without altering the original class definition. <br/>

<br/>

**How Stick-on Notes Work:** <br/>
`objc_setAssociatedObject` is like writing something on your stick-on note and attaching it to an object.<br/>
`objc_getAssociatedObject` is like reading what's written on the note attached to an object.<br/>
The key is the unique identifier for your note, ensuring you can find the right one among many.<br/>

<br/> 

**Adding Stored Properties:** <br/>
Associated objects allow you to effectively add stored properties to an instance of a class.<br/> 
This means you can associate state or storage with an instance that persists across the object's lifetime.<br/> 

<br/> 

**Avoid Subclassing:** <br/> 
Often, subclassing just to add a single property can be overkill and can lead to a bloated class hierarchy.<br/> 
Associated objects provide a lightweight alternative to subclassing for storing additional information.<br/>

<br/> 

**Modular Code** <br/>
They help keep code modular.<br/>
You can separate concerns by attaching functionality or data to objects <br/>
without changing their original implementation. <br/>

#

<br/> 

## CharacterLimitHandler:

These classes handle input character limiting. It uses RxSwift to react to text changes:

```swift
var characterLimitHandler: CharacterLimitHandler? {
    get {
        return objc_getAssociatedObject(self, &AssociatedKeys.characterLimitHandler) as? CharacterLimitHandler
    }
    set {
        objc_setAssociatedObject(self, &AssociatedKeys.characterLimitHandler, newValue, .OBJC_ASSOCIATION_RETAIN_NONATOMIC)
    }
}
```

#

<br/>

## Declarative UITextField Extension:

I extended `UITextField` to support a declarative style, enabling easy configuration of text fields:

```swift
extension UITextField {

    func setupForCharacterLimit(limit: Int) -> Self {
        let handler = CharacterLimitHandler(limit: limit)
        self.characterLimitHandler = handler
        
        self.rx.text
            .compactMap { $0 }
            .flatMapLatest { text -> Observable<String> in
                let limitedText = String(text.prefix(limit))
                return .just(limitedText)
            }
            .observe(on: MainScheduler.instance)
            .subscribe(onNext: { [weak self] limitedText in
                if self?.text != limitedText {
                    self?.text = limitedText
                }
            })
            .disposed(by: handler.disposeBag)
        
        return self
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
  .setupForCharacterLimit(limit: 50)
```

#

<br/>

## Conclusion:

This exploration into combining Objective-C runtime features with Swift's declarative programming has led to cleaner, more maintainable code. It demonstrates the power of extending UIKit components in a way that aligns with modern Swift practices.

--- 

+++
title = "Swift Fudamentals 2 - Closures"
date = 2026-06-15
draft = true
author = "Kein Li"
+++


Explore closures and topics such as capture lists, escaping closures, and non escaping closures


***Capture lists***: Closures can define capture lists to 'freeze' the value at the time the closure is defined - what this mean is the 'count' the closure sees is the first value when the closure is accessed for the first time (if using lazy) 
or when its created for the first time


```swift

var count = 10

var printer = { [count] in // capturel ist
  print(count)
}
printer() // prints 10
count+=1
printer() // prints 10 again
--- 

// No capture list; references same value
var count2 = 10

var printer2 = { 
  print(count)
}
printer2()
count2+=1
printer2()
````
***Escaping closures that capture self must be explicit*** - closures that are escaping, meaning they will run at a later time and live outside the scope of 
the creator, are considered escaping. Examples of escaping closures are ones
that are passed to a function, as a parameter, or one that is assigned as a 
property. 

Escaping closures must either use `self` explicitly or include `self` in the capture list 

```swift
class Foo {
  var count: Int 
  
  lazy var someIncrementClosure = {
    self.count+=1
  }

  lazy var someIncrementClosure = { [self]
    count+=1
  }

  init(count: Int = 20) {
    self.count = count
  }
}

var f = Foo()
f.someIncrementClosure()
```

***Closures are reference types*** - this means they are impacted by ARC and can lead to retain cycles. Below we have a retain cycle: closure captures self, 
while self owns the closure. 

- Note:
- The retain cycle is only created when the closure is accessed for the first time, since this is a lazy property


```swift
class Foo {
  var count: Int 
  
  lazy var someIncrementClosure = {  // retain cycle
    self.count+=1
  }

  init(count: Int = 20) {
    self.count = count
  }
}

var f = Foo()
f.someIncrementClosure()
```

To prevent retain cycles, two things we can do is:
- use `[weak self]` 
- Explicitly use a capture list (less common)

```swift
---

class Foo {
  var count: Int 
  
  lazy var someIncrementClosure = {  [count]
    count+=1
  }

  lazy var someIncrementClosure = {  [weak self]
    self?.count+=1
  }

  init(count: Int = 20) {
    self.count = count
  }
}
```

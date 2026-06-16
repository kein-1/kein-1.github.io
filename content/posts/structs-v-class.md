+++
title = "Swift Fudamentals 1 - Structs v Classes"
date = 2026-06-15
draft = false
author = "Kein Li"
+++


Explore differences between structs and classes and when to use each. Topics covered
will include mutability, assignment, CoW. 

```swift
class Foo {
  var count: Int 
  var bar: Bar?

  init(count:Int) {
    self.count = count 
  }

  deinit {
    print("i'm gone")
  }

  func printSelf() {
    print("my count: \(count)")
  }

  func printBarCount() {
    guard let bar else { return }
    print("bar count: \(bar.count)")
  }

  func mutateBar() {
    self.bar?.count+=1
  }
}

struct Bar {
  var count: Int 
  var foo: Foo? 

  func printFooCount() {
    guard let foo else { return }
    print("foo's count: \(foo.count)")
  }

  func mutateFooCount() {
    foo?.count+=1
  }
}
```

***We'll start with basics:***

****Diff #1: Assignment**** — struct & class declared with `let` cannot be reassigned: 

```swift                    
    let f = Foo(count:10)
    let b = Bar(count: 20, foo: f)                                                                                 

    f = Foo(count: 20)                                                                                                 
    b = Bar(count: 120, foo: Foo(count: 10))                                                                         
```    

****Diff #2: Mutability**** — struct cannot be mutated if we use `let`; but if we 
use `var` we can mutate its properties.This also explains why we can mutate 
native Arrays (since they're structs) if we declared them as `vars`

Classes do not matter since using `let` vs `var` is just about the pointer assignment


```swift

let f = Foo(count:10)
let b = Bar(count: 20, foo: f)

f.count = 20 // good 
b.count = 30 // error

---

var b = Bar(count: 20, foo: f)

b.count = 30 // good

```
****Diff #3: Value vs Reference Semantics**** — structs are declared on the stack, while 
classes are declared on the heap. But what happens if we have a class within
a struct, and a struct within a class? 

Case #1: Struct within a Class

```swift

var f2 = f
f2.mutateBar()

f2.printBarCount()
f.printBarCount()

```
Since we are just assigning a new pointer, the underlying class is the same 
reference. This means `f` and `f2` both point to the same object; so mutating
its struct field will cause it to reflect in both print statements. 

Also, if structs are declared in a class as a property, it is allocated on the 
`heap`, not the stack


Case #2: Class within a Struct
```swift 
var f = Foo(count: 10)
var b1 = Bar(count:20, foo: f)
var b2 = Bar(count:30, foo:f)

b1.mutateFooCount()

b1.printFooCount() // prints 11
b2.printFooCount() // prints 11
```

Structs are copied, but the underlying data we're copying here is a pointer to
a class. This means both structs point to the same class, and mutating one struct's 
class value will cause the other struct to reflect the changes. We can also
confirm that the underyling class is the same by printing the memory addresses:

```swift
print(Unmanaged.passUnretained(b1.foo!).toOpaque())
print(Unmanaged.passUnretained(b2.foo!).toOpaque())
```

****Diff #4: ARC**** — structs do not contribute to ARC (Automatic Reference Counting), 
which is Swift's way of memory management. ARC will be covered in another post


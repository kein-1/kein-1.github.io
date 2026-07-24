+++
title = "SwiftUI view updates in @Observable vs @StateObject/@ObservedObject"
date = 2026-07-21
draft = false
author = "Kein Li"
+++

Sort of a continuation from the previous post [@StateObject vs @ObservedObject](/posts/stateobject-vs-observedobject/).

I'm used to using the modern @Observable macro over @StateObject/ObservedObject but when I came across Speecify's iOS Debugging challenge, which had @StateObject/ObservedObject, it made me revisit how the @StateObject/ObservedObject differs from @Observable, and when SwiftUI decides to redraw the body. 

I added print statements in the `init` of each child view along with the SwiftUI printChanges() in the body of each child view to see what actually runs. 


```swift
class CounterViewModel: ObservableObject {
    @Published var counter = 0
    @Published var label = "string name"
    
    func incrementCounter() {
        counter += 1
    }
}

struct ParentView: View {
    
    @State private var localCounter = 0
    @StateObject private var viewModel: CounterViewModel = CounterViewModel()
    
    var body: some View {
        let _ = Self._printChanges()
        VStack {
            
            Text("I am Parent View")
            Button("Increment local state") {
                localCounter += 1
            }
            
            Button("Increment view model counter") {
                viewModel.incrementCounter()
            }
            
            Button("Change view model label") {
                viewModel.label += "!"
            }
            
            Text("Local counter: \(localCounter)")
            
            Divider()
            
            CounterDisplayView(viewModel: viewModel)
            
            Divider()
            
            LabelDisplayView(label: viewModel.label)
        }
    }
}

struct CounterDisplayView: View {
    
    @ObservedObject var viewModel: CounterViewModel
    init(viewModel: CounterViewModel) {
        print("CounterDisplayView init ran")
        _viewModel = ObservedObject(wrappedValue: viewModel)
    }
    
    var body: some View {
        let _ = print("CounterDisplayView body re-rendered")
        let _ = Self._printChanges()
        VStack {
            Text("I am Counter Display View")
            Text("Counter value: \(viewModel.counter)")
            Button("Increment from child") {
                viewModel.incrementCounter()
            }
        }
        
    }
}


struct LabelDisplayView: View {
    
    let label: String
    
    init(label: String) {
        print("LabelDisplayView init ran")
        self.label = label
    }
    
    var body: some View {
        let _ = print("LabelDisplayView body re-rendered")
        let _ = Self._printChanges()
        VStack {
            Text("I am Label Display View")
           Text(label)
        }
    }
}
```

Looking at `@StateObject/ObservedObject`: if we increment the localCounter in the parent, we notice that the parent will re-evaluate its body property, meaning the child structs will be recreated (as seen in the init) but their body properties won't change since the dependencies did not update. 

Now if we update the view model's label, which `LabelDisplayView` depends on, we see that `LabelDisplayView` re-renders but `CounterDisplayView` *also* re-renders, despite it not depending on the label. 

```
ParentView: _viewModel changed.
CounterDisplayView init ran
LabelDisplayView init ran
CounterDisplayView body re-rendered
CounterDisplayView: _viewModel changed.
LabelDisplayView body re-rendered
LabelDisplayView: @self changed.
```

Contrast this with @Observable where we do the same thing in updating the label:


```swift
import SwiftUI

@Observable
class CounterViewModel {
    var counter = 0
    var label = "string name"
    
    func incrementCounter() {
        counter += 1
    }
}

struct ParentView: View {
    
    @State private var localCounter = 0
    @State private var viewModel: CounterViewModel = CounterViewModel()
    
    var body: some View {
        VStack {
            let _ = Self._printChanges()
            Text("I am Parent View")
            Button("Increment local state") {
                localCounter += 1
            }
            
            Button("Increment view model counter") {
                viewModel.incrementCounter()
            }
            
            Button("Change view model label") {
                viewModel.label += "!"
            }
            
            Text("Local counter: \(localCounter)")
            
            Divider()
            
            CounterDisplayView(viewModel: viewModel)
            
            Divider()
            
            LabelDisplayView(label: viewModel.label)
        }
    }
}

struct CounterDisplayView: View {
    
    let viewModel: CounterViewModel
    init(viewModel: CounterViewModel) {
        print("CounterDisplayView init ran")
        self.viewModel = viewModel
    }
    
    var body: some View {
        let _ = print("CounterDisplayView body re-rendered")
        let _ = Self._printChanges()
        VStack {
            Text("I am Counter Display View")
            Text("Counter value: \(viewModel.counter)")
            Button("Increment from child") {
                viewModel.incrementCounter()
            }
        }
    }
}

struct LabelDisplayView: View {
    
    let label: String
    
    init(label: String) {
        print("LabelDisplayView init ran")
        self.label = label
    }
    
    var body: some View {
        let _ = print("LabelDisplayView body re-rendered")
        let _ = Self._printChanges()
        VStack {
            Text("I am Label Display View")
            Text(label)
        }
    }
}

```

```ParentView: \CounterViewModel.<computed 0x000000034006f8b0 (String)> changed.
CounterDisplayView init ran
LabelDisplayView init ran
LabelDisplayView body re-rendered
LabelDisplayView: @self changed.
```

CounterDisplayView's body did not re-render. This is what we expect: it doesn't depend on the label, so why should it re-render? It seems like The new`@Observable` macro is more granular in its updates and only tells SwiftUI to diff and update the body property of the dependencies. 

One thing to note is: CounterDisplayView's init *did* run when we update the label. BUT if we were to instead pass the view model to LabelDisplayView and then have it read the label from the view model, we only see that LabelDisplayView's body re-rendered, and it wasn't even a new init. 



```swift

struct LabelDisplayView: View {
    
    let viewModel: CounterViewModel
    init(viewModel: CounterViewModel) {
        print("LabelDisplayView init ran")
        self.viewModel = viewModel
    }
    
    var body: some View {
        let _ = print("LabelDisplayView body re-rendered")
        let _ = Self._printChanges()
        VStack {
            Text("I am Label Display View")
            Text(viewModel.label)
        }
    }
---

LabelDisplayView body re-rendered
LabelDisplayView: \CounterViewModel.<computed 0x00000003400b6f1c (String)> changed.
```

This is because we pass the view model itself to the child views. The parent's body property does not re-run, thus we don't see the init run in the child. But if we were to pass the label only, the parent notices the view model changed and re-evaluates its label field since a child depends on it. Thus it will cause the body property of the parent to re-run, which in turn causes the both children's init to run again.

---

Another interesting thing is when we update the counter. In `@StateObject/ObservedObject`:

```
ParentView: _viewModel changed.
CounterDisplayView init ran
LabelDisplayView init ran
CounterDisplayView body re-rendered
CounterDisplayView: _viewModel changed.
```

In contrast, with `@Observable`:

```
CounterDisplayView body re-rendered
CounterDisplayView: \CounterViewModel.<computed 0x000000034006fcdc (Int)> changed.
```

Notice how the `LabelDisplayView` in `@StateObject/ObservedObject` has to be re-initialized and even though only the counter was changed. In @Observable, only the dependent views re-render and are re-initialized. The child view itself isn't even re-initialized. 
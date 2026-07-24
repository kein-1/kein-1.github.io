+++
title = "SwiftUI differences in @StateObject vs @ObservedObject"
date = 2026-07-20
draft = false
author = "Kein Li"
+++

I'm used to using the modern @Observable macro over @StateObject/ObservedObject but when I came across Speecify's iOS Debugging challenge, which had @StateObject/ObservedObject, I was curious when to use each, how the @StateObject/ObservedObject differs from each other.


Starting with @StateObject/@ObservedObject: notice what happens if we initialize the view model
inline and pass it to the child, where the child stores it via @ObservedObject.
No issues when we increment the counter in the child and we see the update take effect.

However, if we increment the local state in the parent, SwiftUI detects that there
is a change in a dependency and will re-render the body here (more on that later). 
Thus, the view model is again created and we lose our previous counter value updates.


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
    var body: some View {
        VStack {
            Text("I am Parent View")
            Button("Increment local state") {
                localCounter += 1
            }
            Text("Local counter: \(localCounter)")
            Divider()
            CounterDisplayView(viewModel: CounterViewModel())
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
```

There are a few ways to fix this: 
1. Use @StateObject instead of @ObservedObject in the parent, and just pass it to child while child maintains @ObservedObject: 

```swift
@StateObject private var viewModel: CounterViewModel = CounterViewModel()
```

2. Use @StateObject instead of @ObservedObject child. Parent maintains the inline creation:

```swift
 @StateObject var viewModel: CounterViewModel
    init(vm: CounterViewModel) {
        print("CounterDisplayView init ran")
        _viewModel = StateObject(wrappedValue: vm)
    }
```


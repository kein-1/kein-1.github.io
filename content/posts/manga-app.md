+++
title = "Building out a mini iOS Manga App"
date = 2026-02-22
draft = false
weight = 0
author = "Kein Li"
category = "ios"
+++

### Building Out a Mini iOS Manga App

Finally getting back into building iOS apps after a ~2 year hiatus. It's been awhile since I built something outside of work. I started with watching some CS193P lectures
and followed along for a few lectures before I got bored and decided the best
way to learn is to build anything ASAP.

Personally I think using AI tools is awful when learning something new and
having it write the code for you is awful. I use it strictly for learning purposes like "Explain Swift Concurrency" or "Is my code idiomatic?" or hints on
how to approach a problem

#### Notes

- **Navigation and TabViews**
  - NavigationStack and TabView did not initially work well together.
  - Each TabView required its own NavigationStack for navigation titles to behave properly.
  - Applying a universal background color did not work globally; it only applied correctly within each TabItem view.

- **Use of Enums**
  - Enums were used to manage both navigation and loading states.
  - Navigation was wrapped in a `Route` enum and switched inside `.navigationDestination`.
  - Loading state enum contained: `Idle`, `Loading`, `Success`, and `Error`, where Loaded and Error contained the data and error message respectively so I can switch on the state and either display the data or the error message.

- **Error Handling**
  - Network calls threw specific `NetworkError` enums.
  - Covered decoding, URL, and networking errors.
  - Errors were mapped to a user-friendly error message struct stored inside the loading state.

- **Async/Await**
  - Used async/await functions along with the `.task` modifier for background HTTP calls.
  - Implemented an HTTP client layer injected into view models.
  - Followed Dependency Injection and separation of concerns principles.

- **`.task` Modifier with `id`**
  - The `id` property re-runs the task whenever the id changes.
  - Used this to auto-refetch when query/filter properties changed.
  - Watched a struct for changes and re-ran `loadManga()`.
  - Allowed reuse of the same function for initial load and future refetches (e.g., filter changes).

- **View Models**
  - Used ObservableObjects to manage state for each view.
  - Each view model had its own HTTP client instance.
  - Followed Dependency Injection and separation of concerns principles.

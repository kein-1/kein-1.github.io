---
title: "My Apps"
---

A few things I've built.

<style>
  .app { display: flex; gap: 2rem; align-items: flex-start; margin: 3rem 0; }
  .app-info { flex: 1; min-width: 0; }
  .app-title { display: flex; align-items: center; gap: 0.75rem; margin-bottom: 0.5rem; }
  .app-title img { width: 56px; height: 56px; border-radius: 12px; margin: 0; }
  .app-title h3 { margin: 0 !important; }
  .app-shots {
    flex: 0 0 320px;
    display: flex;
    gap: 0.75rem;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    padding-bottom: 0.5rem;
  }
  .app-shots img {
    width: 300px;
    height: auto;
    border-radius: 18px;
    scroll-snap-align: start;
    flex-shrink: 0;
    margin: 0;
    box-shadow: 0 4px 20px rgba(0,0,0,0.08);
  }
  @media (max-width: 640px) {
    .app { flex-direction: column; }
    .app-shots { flex: 1 1 auto; width: 100%; }
  }
</style>

<div class="app">
  <div class="app-info">

<div class="app-title">
  <img src="/images/daymark/icon.png" alt="">
  <h3>DayMark</h3>
</div>

A daily journal — capture what you did each day in 50 words or less.

**Stack:** Swift, SwiftUI, SwiftData

[App Store](https://apps.apple.com/us/app/daymark-daily-journal/id6760376611)

  </div>
  <div class="app-shots">
    <img src="/images/daymark/daymark-01.png" alt="DayMark 1">
    <img src="/images/daymark/daymark-02.png" alt="DayMark 2">
    <img src="/images/daymark/daymark-03.png" alt="DayMark 3">
    <img src="/images/daymark/daymark-04.png" alt="DayMark 4">
    <img src="/images/daymark/daymark-05.png" alt="DayMark 5">
    <img src="/images/daymark/daymark-06.png" alt="DayMark 6">
  </div>
</div>

<div class="app">
  <div class="app-info">

<div class="app-title">
  <img src="/images/mochi/icon.png" alt="">
  <h3>Mochi</h3>
</div>

An all-in-one pet app to track everything that matters for your pet. Actively evolving as new functionality gets added.

**Stack:** Swift, SwiftUI, SwiftData, MapKit

[App Store](https://apps.apple.com/us/app/mochi-pet-health-tracker/id6771205280)

  </div>
  <div class="app-shots">
    <img src="/images/mochi/mochi-01.png" alt="Mochi 1">
    <img src="/images/mochi/mochi-02.png" alt="Mochi 2">
    <img src="/images/mochi/mochi-03.png" alt="Mochi 3">
    <img src="/images/mochi/mochi-04.png" alt="Mochi 4">
    <img src="/images/mochi/mochi-05.png" alt="Mochi 5">
  </div>
</div>

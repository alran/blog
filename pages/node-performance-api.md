# Node Performance Timing API
### 1/18/2019

The [Performance Timing API](https://nodejs.org/api/perf_hooks.html) is a great way to check the performance of a 
method. It allows you to wrap your function in a function that measures the runtime of the wrapped function.

I used this API to check the speed of a method I was writing to see if changes I made made a substantial difference.

To use, first require `'perf_hooks'` in the file in which you are writing the method. Below, I pull out `performance`
to more easily access timing measurement functionality. I also pull out `PerformanceObserver` so that I can actually access
the measurements I take.

```javascript
const { performance, PerformanceObserver } = require('perf_hooks');
```

Next, wrap the method you are measuring with `performance.timerify`.

```javascript
const wrapped = performance.timerify(myFunction);
```

To observe the measurements taken related to the method, create a `PerformanceObserver`. You can access
instances of the `PerformanceEntry` class inside of this observer with `getEntries()`.

```javascript
const observer = new PerformanceObserver((list) => {
  console.log(list.getEntries()[0].duration);
  observer.disconnect();
});
```

Before actually calling your wrapped method, subscribe your instance of the `PerformanceObserver` to notifiications
of new `PerformanceEntry` instances. You can specify the specific entry types you are interested in as part of the 
options you pass to the `observe` method. For our purposes, we are watching for 'function', but in more advanced tracking,
you may end up using entry types like 'mark' or 'measure'.

```javascript
observer.observe({ entryTypes: ['function'] });
```

Last, call your wrapped function!

```javascript
wrapped();
```

When you run your file with node, the runtime will be printed to your terminal

```
→ node test.js

0.332929
```

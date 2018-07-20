---
layout: post
title: Interesting JS Patterns that I discovered
---

# Arrays #

## Destructuring Patterns ##

```
> result = [ [null, 1],["error", null],[null, 2],["error",4] ]
[ [ null, 1 ], [ 'error', null ], [ null, 2 ], [ 'error', 4 ] ]
> result.filter(([err,data]) => err === null )
[ [ null, 1 ], [ null, 2 ] ]
```

We can completely ignore the second value in the Array as we are interested only
on error and rewrite like this.
```
> result.filter(([err]) => err === null)
[ [ null, 1 ], [ null, 2 ] ]
```
Or as some languages do put a variable which signifies I dont care
```
> result.filter(([err, __]) => err === null)
[ [ null, 1 ], [ null, 2 ] ]
```

## For Looping ##

If i want to run a for loop X times and do something with it..

`[...Array(12).keys()].map(number => console.log(number))`

The above will run a loop.map 12 times from 0..11






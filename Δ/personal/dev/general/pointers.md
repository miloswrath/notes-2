---
created: 2026-05-27
updated: 2026-05-27
type: read
status: seed
aliases: []
topics:
  - cs-fundamentals
source_type: link
source: https://en.wikipedia.org/wiki/Pointer_(computer_programming)
author:
related: []
ideas: []
projects: []
tags:
  - delta/read
  - cs-fundamentals
---

# pointers

## Summary
---

Pointers are references to other values stored in memory.

## Key Points
 ---
 > Pointers are most often used to let another function change the original value, not usually to change where the caller’s pointer points.

- A pointer stores the memory address of another value.
- `&x` means "address of x".
- `*p` means "value at pointer p".

## Why This Matters
---
Pointers let programs share, change, and pass values without copying them.

## Connections
- Related knowledge: [[memory]]
- Possible ideas: 
- Relevant projects: 

## Notes

***General Concept***
- Memory is like numbered boxes.
- A normal variable stores a value.
- A pointer stores the box number where a value lives.

```c
int x = 5;      // normal value
int *p = &x;    // p points to x

printf("%d", *p); // prints 5

*p = 10;        // change x through p
printf("%d", x);  // prints 10
```

```go
x := 5       // normal value
p := &x      // p points to x

fmt.Println(*p) // prints 5

*p = 10      // change x through p
fmt.Println(x)  // prints 10
```


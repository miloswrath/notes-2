---
created: 2026-05-27
updated: 2026-05-27
type: read
status: seed
aliases: []
topics:
  - golang
  - personal
source_type: link
source: https://gobyexample.com/
author:
related: []
ideas: []
projects: []
tags:
  - delta/read
  - golang
  - dev
---

# Golang learning 1

## Summary
One or two sentences capturing the core idea.

## Key Points
- 
- 
- 

## Why This Matters
How this knowledge changes understanding, decisions, or future thinking.

## Connections
- Related knowledge: 
- Possible ideas: 
- Relevant projects: 

## Notes

### Pointers
[Go Docs Page](https://gobyexample.com/pointers) · [[Δ/personal/dev/general/pointers|Relevant notes]]
- Pointers allow for a value to be passed into a function without actually copying the value. 

***Print Forms***

| Symbol / form                       | Where it is used | Meaning with pointers                                                                                | Example                                                           |
| ----------------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `&x`                                | Go expression    | **Address-of** operator. Returns a pointer to `x`'s memory address.                                  | `p := &x` means `p` points to `x`.                                |
| `*p`                                | Go expression    | **Dereference** operator. Reads or changes the value stored at the address `p` points to.            | `fmt.Println(*p)` prints the value of `x`; `*p = 10` changes `x`. |
| `var p *int`                        | Go type syntax   | Declares `p` as a pointer to an `int`. Here `*` is part of the type, not a dereference.              | `var p *int; p = &x`                                              |
| `fmt.Print(p)` / `fmt.Println(p)`   | Print function   | Prints the pointer value itself, usually as a memory address like `0xc000...`.                       | `fmt.Println(p)` prints where `x` lives.                          |
| `fmt.Print(*p)` / `fmt.Println(*p)` | Print function   | Prints the value stored at the pointer's address.                                                    | If `x := 5`, then `fmt.Println(*p)` prints `5`.                   |
| `fmt.Printf("%p", p)`               | Format verb      | Prints a pointer address explicitly. `%p` is the standard pointer-address format.                    | `fmt.Printf("%p", p)`                                             |
| `fmt.Printf("%v", p)`               | Format verb      | Prints the default representation of the pointer, usually the address.                               | `fmt.Printf("%v", p)`                                             |
| `fmt.Printf("%v", *p)`              | Format verb      | Prints the default representation of the value being pointed to.                                     | `fmt.Printf("%v", *p)`                                            |
| `fmt.Printf("%T", p)`               | Format verb      | Prints the type of the pointer.                                                                      | For `p := &x`, this may print `*int`.                             |
| `nil`                               | Pointer value    | Means the pointer points to nothing. Dereferencing a `nil` pointer with `*p` causes a runtime panic. | `var p *int; fmt.Println(p)` prints `<nil>`.                      |

> Note: `fmt.Print` and `fmt.Println` do **not** use format symbols like `%p` or `%v`; use `fmt.Printf` when you want format verbs.
> src: `pi`

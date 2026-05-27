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

***Print Forms and Format Verbs***

| Symbol / form                       | Where it is used | Meaning / pointer usage                                                                               | Example                                                           |
| ----------------------------------- | ---------------- | ----------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `&x`                                | Go expression    | **Address-of** operator. Returns a pointer to `x`'s memory address.                                   | `p := &x` means `p` points to `x`.                                |
| `*p`                                | Go expression    | **Dereference** operator. Reads or changes the value stored at the address `p` points to.             | `fmt.Println(*p)` prints the value of `x`; `*p = 10` changes `x`. |
| `var p *int`                        | Go type syntax   | Declares `p` as a pointer to an `int`. Here `*` is part of the type, not a dereference.               | `var p *int; p = &x`                                              |
| `fmt.Print(p)` / `fmt.Println(p)`   | Print function   | Prints the pointer value itself, usually as a memory address like `0xc000...`.                        | `fmt.Println(p)` prints where `x` lives.                          |
| `fmt.Print(*p)` / `fmt.Println(*p)` | Print function   | Prints the value stored at the pointer's address.                                                     | If `x := 5`, then `fmt.Println(*p)` prints `5`.                   |
| `fmt.Printf("%p", p)`               | Format verb      | Prints a pointer address explicitly. `%p` is the standard pointer-address format.                     | `fmt.Printf("%p", p)`                                            |
| `fmt.Printf("%v", p)`               | Format verb      | Prints the default representation of a value. For a pointer, this usually prints the address.         | `fmt.Printf("%v", p)`                                            |
| `fmt.Printf("%v", *p)`              | Format verb      | Prints the default representation of the value being pointed to.                                      | `fmt.Printf("%v", *p)`                                           |
| `fmt.Printf("%+v", value)`          | Format verb      | Prints a value with extra detail. For structs, includes field names.                                  | `fmt.Printf("%+v", person)`                                      |
| `fmt.Printf("%#v", value)`          | Format verb      | Prints a Go-syntax representation of the value, useful for debugging.                                 | `fmt.Printf("%#v", value)`                                       |
| `fmt.Printf("%T", p)`               | Format verb      | Prints the type of a value. For a pointer, this may print something like `*int`.                      | `fmt.Printf("%T", p)`                                            |
| `fmt.Printf("%s", s)`               | Format verb      | Prints a string or byte slice as plain text.                                                          | `fmt.Printf("%s", "hello")` prints `hello`.                    |
| `fmt.Printf("%q", s)`               | Format verb      | Prints a quoted string, or a quoted character for runes/bytes.                                        | `fmt.Printf("%q", "hi")` prints `"hi"`.                      |
| `fmt.Printf("%d", n)`               | Format verb      | Prints an integer in base-10 decimal form.                                                            | `fmt.Printf("%d", 42)` prints `42`.                              |
| `fmt.Printf("%b", n)`               | Format verb      | Prints an integer in base-2 binary form.                                                              | `fmt.Printf("%b", 5)` prints `101`.                              |
| `fmt.Printf("%o", n)`               | Format verb      | Prints an integer in base-8 octal form.                                                               | `fmt.Printf("%o", 8)` prints `10`.                               |
| `fmt.Printf("%x", n)` / `%X`        | Format verb      | Prints an integer, byte slice, or string in hexadecimal. `%x` uses lowercase; `%X` uses uppercase.   | `fmt.Printf("%x", 255)` prints `ff`.                             |
| `fmt.Printf("%c", r)`               | Format verb      | Prints an integer/rune as the Unicode character it represents.                                        | `fmt.Printf("%c", 65)` prints `A`.                               |
| `fmt.Printf("%U", r)`               | Format verb      | Prints a Unicode code point format.                                                                   | `fmt.Printf("%U", 'A')` prints `U+0041`.                         |
| `fmt.Printf("%t", b)`               | Format verb      | Prints a boolean as `true` or `false`.                                                                | `fmt.Printf("%t", true)` prints `true`.                          |
| `fmt.Printf("%f", f)`               | Format verb      | Prints a floating-point number in decimal form.                                                       | `fmt.Printf("%f", 3.14)` prints `3.140000`.                      |
| `fmt.Printf("%.2f", f)`             | Format verb      | Prints a floating-point number with 2 digits after the decimal.                                       | `fmt.Printf("%.2f", 3.14159)` prints `3.14`.                     |
| `fmt.Printf("%e", f)` / `%E`        | Format verb      | Prints a floating-point number in scientific notation.                                                | `fmt.Printf("%e", 1000.0)` prints something like `1.000000e+03`. |
| `fmt.Printf("%g", f)` / `%G`        | Format verb      | Prints a floating-point number using a compact form: `%e`/`%E` or `%f`, whichever is shorter.        | `fmt.Printf("%g", 1000.0)` prints `1000`.                        |
| `fmt.Printf("%%")`                  | Format verb      | Prints a literal percent sign.                                                                        | `fmt.Printf("%%")` prints `%`.                                   |
| `nil`                               | Pointer value    | Means the pointer points to nothing. Dereferencing a `nil` pointer with `*p` causes a runtime panic. | `var p *int; fmt.Println(p)` prints `<nil>`.                      |

> Note: `fmt.Print` and `fmt.Println` do **not** use format verbs like `%p`, `%d`, or `%c`; use `fmt.Printf` when you want formatted output.
> src: `pi`

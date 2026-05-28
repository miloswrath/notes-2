---
created: 2026-05-28
updated: 2026-05-28
type: read
status: seed
aliases:
  - go.mod
  - Go modules
topics:
  - golang
  - go-modules
  - dependency-management
source_type: link
source:
author:
related:
  - "[[Δ/personal/dev/go/Golang learning 1|Golang learning 1]]"
ideas: []
projects: []
tags:
  - delta/read
---

# go-mod

## Summary
`go.mod` is the manifest for a Go module: it names the module, declares the Go version, and records dependency requirements. Treat it as the project’s dependency contract, while `go.sum` verifies the downloaded module contents.

## Key Points
- Core directives: `module`, `go`, and `require`.
- Prefer `go get` and `go mod tidy` over manual dependency edits.
- Commit both `go.mod` and `go.sum`.

## Notes

### What `go.mod` defines

A Go module is a versioned collection of Go packages, usually one repository. Its `go.mod` file tells Go:

- the module import path
- the Go version/toolchain behavior to target
- the dependency modules and versions required to build
- optional overrides or exclusions

```go
module github.com/you/myproject

go 1.22

require (
    github.com/google/uuid v1.6.0
    golang.org/x/text v0.14.0
)
```

### Core directives

#### `module`

```go
module github.com/you/myproject
```

The module path is the import prefix other code uses, for example:

```go
import "github.com/you/myproject/somepackage"
```

For local or private code, the name can technically be anything, but it is usually best to use the eventual repository path.

#### `go`

```go
go 1.22
```

Declares the Go version the module targets. This affects language features, module behavior, and dependency pruning. It does not strictly mean the code can only run on that exact Go version, but it should stay aligned with developer and CI expectations.

#### `require`

```go
require github.com/google/uuid v1.6.0
```

Lists dependency modules and versions. Go usually manages this through commands rather than hand edits.

### `go.sum`

Go creates `go.sum` next to `go.mod`. It records cryptographic hashes of dependency versions so builds are reproducible and dependencies cannot silently change.

Commit both files:

```text
go.mod
go.sum
```

### Common commands

Initialize a module:

```bash
go mod init github.com/you/myproject
```

Add or upgrade a dependency:

```bash
go get github.com/google/uuid
go get github.com/google/uuid@v1.6.0
go get github.com/google/uuid@latest
```

Clean up dependencies:

```bash
go mod tidy
```

`go mod tidy` adds missing requirements, removes unused ones, and updates `go.sum`. Run it before committing.

Inspect dependencies:

```bash
go mod graph
go mod why github.com/some/dependency
```

### Direct vs indirect dependencies

A direct dependency is imported by your code. An indirect dependency is needed by one of your dependencies or was previously used directly.

```go
require (
    github.com/gin-gonic/gin v1.10.0
    github.com/bytedance/sonic v1.11.6 // indirect
)
```

Usually, let Go manage indirect dependencies.

### Overrides and exclusions

`replace` overrides a dependency, often for local development or forks:

```go
replace github.com/you/sharedlib => ../sharedlib
replace github.com/original/lib => github.com/yourfork/lib v1.2.3
```

Use `replace` carefully. It affects your module’s build, but downstream users do not automatically inherit it.

`exclude` blocks a specific bad version and is rarely needed:

```go
exclude github.com/some/lib v1.2.0
```

### Recommended workflow

For a new project:

```bash
go mod init github.com/you/project
go mod tidy
go test ./...
```

Before committing:

```bash
go mod tidy
go test ./...
git add go.mod go.sum
```

When adding or updating a dependency:

```bash
go get example.com/pkg@latest
go mod tidy
go test ./...
```

### Rule of thumb

Rarely edit `go.mod` manually except to change the module path, update the Go version, add/remove `replace`, or review dependency versions. Most dependency changes should come from `go get` and `go mod tidy`.

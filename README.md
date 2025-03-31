# Go-Easy - The Go Compiler with Unused Variable Warnings

This is a modified version of the Go compiler that treats unused variables as warnings instead of errors. It allows code with unused variables to compile and run successfully.

## Features

- Unused variables are reported as warnings instead of errors
- Code with unused variables still compiles and runs successfully
- The compiler version has "-easy" suffix to clearly identify it as a custom build

## Automatic Builds

This repository includes a GitHub Actions workflow that automatically:

1. Monitors the official Go repository for changes
2. Builds Go for multiple platforms when changes are detected
3. Creates GitHub releases with the binary packages

## Repository Structure

For the GitHub Actions workflow to work correctly, you need to have the modified Go source files in the correct structure. The repository should contain:

```
├── .github
│   └── workflows
│       └── build-and-release.yml
├── README.md
└── src
    └── cmd
        ├── compile
        │   └── internal
        │       ├── base
        │       │   └── print.go
        │       └── types2
        │           ├── api.go
        │           └── errors.go
        └── dist
            └── build.go
```

### Required Modified Files

1. `src/cmd/compile/internal/base/print.go` - Modified to not count unused variable errors
2. `src/cmd/compile/internal/types2/errors.go` - Modified to show "warning:" prefix for unused variable errors
3. `src/cmd/compile/internal/types2/api.go` - Modified to only return errors for non-unused-variable issues
4. `src/cmd/dist/build.go` - Modified to append "-easy" to version string

## Installation

You can either:

1. Download pre-built binaries from the [Releases](../../releases) page
2. Clone this repository and build it yourself

## Building Manually

If you want to build Go-Easy manually:

1. Clone the official Go repository
2. Copy the modified files from this repository to the Go repository
3. Run `./src/make.bash` (or `.\src\make.bat` on Windows)

## Testing

```go
package main

import "fmt"

func main() {
    var a = "hello"
    fmt.Println(a)
    
    var unused = true
    // This variable is unused but will compile with just a warning
}
```

With Go-Easy, this will compile and run with a warning:

```
test.go:9:5: warning: declared and not used: unused
hello
```

## License

Go-Easy is based on Go and is subject to the same [license](https://golang.org/LICENSE) as the original Go source code.

# Go-Easy

**The Go programming language compiler - that goes easy on you**

This is a modified version of the Go compiler that treats unused variables as warnings instead of errors. It allows code with unused variables to compile and run successfully, making it more forgiving during development and prototyping.

## Differences from the Original Go Compiler

Go-Easy modifies the standard Go compiler in several key ways:

| Feature | Original Go | Go-Easy |
|---------|-------------|---------|
| Unused variables | Errors that prevent compilation | Warnings that allow compilation to continue |
| Version string | Standard Go version (e.g., "go1.25") | Appended with "-easy" (e.g., "go1.25-easy") |
| Compilation process | Stops on unused variable errors | Continues despite unused variables |
| Development workflow | Requires fixing all unused variables | Allows focusing on functionality first |

These changes make Go-Easy particularly useful for:
- Rapid prototyping and experimentation
- Learning Go without getting stuck on variable usage
- Incremental development where variables may temporarily be unused
- Legacy code maintenance where removing unused variables might be risky

## Features

- Unused variables are reported as warnings instead of errors
- Code with unused variables still compiles and runs successfully
- The compiler version has "-easy" suffix to clearly identify it as a custom build

## Automated Workflows

This repository includes several GitHub Actions workflows that work together to keep Go-Easy updated with the latest Go version:

### Auto-Merge Workflow

The `auto-merge-golang.yml` workflow:
- Runs daily to check for updates to the official Go repository
- Creates a PR with the latest Go code + Go-Easy modifications
- Automatically merges the PR to keep the master branch up-to-date

### Platform-Specific Build Workflows

After auto-merge completes, three separate build workflows run:
- `build-and-release-macos.yml` - Builds Go-Easy for macOS (arm64, amd64)
- `build-and-release-windows.yml` - Builds Go-Easy for Windows (386, amd64)
- `build-and-release-linux.yml` - Builds Go-Easy for Linux (386, amd64, arm, arm64)

These workflows:
- Automatically run after the auto-merge workflow succeeds
- Build Go-Easy for each platform and architecture
- Create GitHub releases with the binary packages
- Can be manually triggered with custom options

### Manual Workflow Trigger

You can manually trigger any workflow from the GitHub Actions tab:

1. Navigate to the Actions tab in your repository
2. Select the desired workflow
3. Click "Run workflow"
4. Configure options:
   - Force build/update: Proceeds even if no changes are detected
   - Custom version suffix: Set a custom suffix (default is "-easy")
   - Skip release: Build but don't create a GitHub release

## Repository Structure

For the GitHub Actions workflows to work correctly, you need to have the modified Go source files in the correct structure. The repository should contain:

```
├── .github
│   └── workflows
│       ├── auto-merge-golang.yml
│       ├── build-and-release-macos.yml
│       ├── build-and-release-windows.yml
│       └── build-and-release-linux.yml
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

## How It Works

The automation system follows this process:
1. The auto-merge workflow checks daily for updates to the Go repository
2. When updates are found, it clones the latest Go code and applies our modifications
3. A PR is created and automatically merged to keep the master branch up-to-date
4. The platform-specific build workflows are triggered after the merge
5. Each build workflow compiles Go-Easy for its target platform(s)
6. A GitHub release is created with all the built binaries

This ensures that Go-Easy always stays up-to-date with the latest Go version while maintaining our modifications.

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
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build and Test Commands

```bash
# Run all tests
go test ./...

# Run a specific test
go test -run TestName ./...

# Run tests with verbose output
go test -v ./...

# Build/check compilation
go build ./...

# Download dependencies
go mod download

# Tidy dependencies
go mod tidy
```

## Architecture Overview

This is a Go logging library (`github.com/Bparsons0904/goLogger`) that wraps the standard library's `slog` package with a fluent interface.

### Core Design

- **Logger interface** (`logger.go:11-27`): Defines the public API with methods for different log levels and chaining
- **SlogLogger struct**: Implements the Logger interface, wrapping `*slog.Logger`
- **Output format**: JSON by default (to stderr), text when `LOG_FORMAT=text` env var is set
- **Test mode**: Automatically discards output when running under `go test`

### Key Patterns

**Fluent chaining** - Methods like `With()`, `File()`, and `Function()` return new Logger instances with additional context:
```go
logger.File("handler.go").Function("Create").Info("starting")
```

**Error methods with returns** - Several error methods both log and return the error:
- `Error()` / `ErrMsg()` - logs and returns `fmt.Errorf`
- `Err()` - logs and returns the original error
- `Er()` / `ErMsg()` - logs only, no return

**Timer utility** - Returns a closure for timing operations:
```go
done := logger.Timer("database query")
defer done()
```

### Environment Variables

- `LOG_FORMAT=text` - Use text handler instead of JSON

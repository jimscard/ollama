# Repository Guidelines

## Project Structure

Ollama is a Go-based project with native code via CGO. Key directories:

- **`cmd/`** - CLI entrypoints (e.g., `main.go` → `cmd/ollama/`)
- **`server/`** - HTTP API server implementation
- **`api/`** - API request/response types
- **`llm/`** - Core LLM inference engine
- **`ml/`** - Machine learning backends (Metal, CUDA, Vulkan)
- **`convert/`** - Model format conversion utilities
- **`integration/`** - Integration test suite
- **`docs/`** - Documentation source
- **`scripts/`** - CI and utility scripts

## Build & Test Commands

| Command | Description |
|---------|-------------|
| `go run . serve` | Run Ollama server (Linux/macOS) |
| `go build` / `go run .` | Build CLI binary |
| `go test ./...` | Run all Go tests |
| `go test ./integration/...` | Run integration tests |
| `cmake -B build && cmake --build build` | Build native code (macOS/Linux) |
| `go generate ./...` | Generate code from templates |

## Coding Style

- **Language**: Go 1.26+, C++17
- **Formatting**: `gofmt` and `gofumpt` enforced
- **Linting**: `golangci-lint` with strict rules (no `errcheck`, limited `staticcheck`)
- **Package naming**: Short, lowercase, no underscores
- **Test files**: `*_test.go` in same package as production code

## Commit & PR Guidelines

**Commit messages**: `<package>: <short description>` (e.g., `mlx: add memory trace logging`)

**PR requirements**:
- Include tests for new/modified behavior
- Update documentation for user-facing changes
- Reference related issues (e.g., `Closes #123`)
- Keep changes focused and atomic

## Testing

- **Unit tests**: `go test ./<package>` in respective packages
- **Integration tests**: Located in `integration/`, cover end-to-end scenarios
- **Coverage**: Strive for behavior-focused tests over implementation details

## Security

- Report security issues via [SECURITY.md](./SECURITY.md)
- Do not disclose vulnerabilities publicly
- PRs touching auth/config require security review

## Getting Help

Join [Discord](https://discord.gg/ollama) for real-time support.

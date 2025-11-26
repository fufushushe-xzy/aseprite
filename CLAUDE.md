# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Aseprite is a pixel art animation program written in C++17. It uses a custom UI framework (LAF) and the Skia graphics library for rendering. The codebase is organized in a layered architecture with clear separation between document model, rendering, UI, and application logic.

## Building

### Automatic Building

For quick setup, use the automated build scripts:
- **Windows**: `build.cmd`
- **macOS/Linux**: `./build.sh`

Options:
- `./build.sh --auto` - Build without prompts (release mode)
- `./build.sh --reset` - Reset configuration

### Manual Building

```bash
# Clone with submodules
git clone --recursive https://github.com/aseprite/aseprite.git

# Update existing clone
git submodule update --init --recursive

# Configure (example for Linux)
mkdir build
cd build
export CC=clang
export CXX=clang++
cmake \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DCMAKE_CXX_FLAGS:STRING=-stdlib=libstdc++ \
  -DCMAKE_EXE_LINKER_FLAGS:STRING=-stdlib=libstdc++ \
  -DLAF_BACKEND=skia \
  -DSKIA_DIR=$HOME/deps/skia \
  -DSKIA_LIBRARY_DIR=$HOME/deps/skia/out/Release-x64 \
  -DSKIA_LIBRARY=$HOME/deps/skia/out/Release-x64/libskia.a \
  -G Ninja \
  ..

# Build
ninja aseprite
```

**Windows**: Must use Visual Studio 2022 command prompt. MinGW is not supported.

**Skia dependency**: You must download pre-built Skia from https://github.com/aseprite/skia/releases or build the `aseprite-m124` branch yourself.

### Build Options

Key CMake options (set with `-DOPTION=ON/OFF`):
- `ENABLE_TESTS=ON` - Build unit tests
- `ENABLE_BENCHMARKS=ON` - Build benchmarks
- `ENABLE_DEVMODE=ON` - Developer mode with extra debugging features
- `CMAKE_BUILD_TYPE=Debug|Release|RelWithDebInfo` - Build type

## Testing

```bash
# Enable tests when configuring
cmake -DENABLE_TESTS=ON ..

# Build tests
ninja

# Run all tests
ctest

# Run specific test executable
./bin/app-test
```

Tests are located throughout the codebase with `*_tests.cpp` naming.

## Code Style

### Pre-commit Hooks

**Required**: Install pre-commit hooks before making changes:

```bash
pip install pre-commit
pre-commit install
```

Run manually:
```bash
pre-commit run                                    # Format staged files
pre-commit run --hook-stage manual clang-tidy    # Run clang-tidy (check suggestions carefully)
```

### Formatting Standards

- **Formatter**: clang-format 19 (configured in `.clang-format`)
- **Linter**: clang-tidy 19 (configured in `.clang-tidy`)
- **Column limit**: 100 characters
- **Standard**: C++17 (targeting macOS 10.14 compatibility)
- **Const notation**: West const (`const int x` not `int const x`)

### Naming Conventions

```cpp
namespace app {                    // lowercase namespaces

class ClassName {                  // CapitalCase classes
public:
  void memberFunction();           // camelCase methods
  int value() const { return m_value; }  // Inline getters OK

private:
  int m_memberVar;                 // m_ prefix for member variables
};

void function_name();              // snake_case for functions
```

### Important Rules

- Use `auto` for complex types, iterators, and obvious cases
- Use `nullptr` instead of `NULL`
- Prefer range-based for loops
- Use `std::unique_ptr`, `std::shared_ptr` sparingly (prefer value semantics)
- Opening braces on same line for functions
- No braces for single-line if/for unless returning early
- Platform detection: `#if LAF_WINDOWS`, `#elif LAF_MACOS`, `#elif LAF_LINUX`

## Architecture

### Layered Structure

The codebase is organized in dependency layers (from bottom to top):

**Level 0** - Independent libraries:
- `clip` - Clipboard operations
- `fixmath` - Fixed-point math
- `flic` - FLI/FLC file format
- `laf/base` - Core utilities (threading, utf8, filesystem)
- `laf/gfx` - Graphics primitives (point, rect, color)
- `observable` - Signal/slot system
- `scripting` - JavaScript/Lua engine
- `undo` - Undo/redo system

**Level 1**:
- `cfg` - Configuration file handling (.ini)
- `gen` - Code generator from XML
- `net` - HTTP networking
- `laf/os` - OS abstraction (windowing, input)

**Level 2**:
- `doc` - Document model (sprites, layers, frames, cels)
- `ui` - Portable UI framework (widgets, windows)
- `updater` - Update checking

**Level 3**:
- `dio` - Document I/O (load/save sprites)
- `filters` - Image effects
- `render` - Document rendering
- `view` - Timeline/range views

**Level 4**:
- `app` - Main application logic
- `desktop` - Desktop integration

**Level 5**:
- `main` - Entry point

### Key Modules

#### `src/app/cmd/`
Command pattern for undo/redo operations. Each command modifies the document atomically.

**Important**: Commands must NOT hold `ImageRef` or smart pointers to doc objects. This causes conflicts in the undo tree. Use object IDs and resolve them when executing.

#### `src/app/commands/`
High-level user commands (menu items, shortcuts). These create and execute `cmd` objects.

#### `src/doc/`
Core document model:
- `Sprite` - Top-level container
- `Layer` - Layer hierarchy
- `Frame` - Timeline frames
- `Cel` - Layer content at specific frame
- `Image` - Pixel data

#### `src/app/script/`
Lua scripting API. Each `*_class.cpp` file exposes a document type to Lua.

#### `src/app/ui/`
Application UI:
- `editor/` - Canvas editor and drawing states
- `skin/` - Theme system
- Widget implementations

## Development Workflow

### Contributor License Agreement

You must sign the [CLA](https://github.com/igarastudio/cla) before contributing code.

### Commit Guidelines

- One feature/fix per commit
- Rebase to `main` branch (or `beta` for beta features)
- Subject line: imperative mood, 72 char limit
- Platform-specific: prefix with `[win]`, `[osx]`, `[x11]`
- CLI-related: prefix with `[cli]`
- Lua-related: prefix with `[lua]`

Example:
```
[win] Fix clipboard handling for transparency

Additional details about the change...
```

### Debugging Features

When built with `ENABLE_DEVMODE=ON`:
- `F5` (Windows) - Show memory usage
- `F1` - Switch renderers (new/old/shader)
- `Ctrl+F1` - Test UI scaling
- `Ctrl+Alt+Shift+Q` - Crash test (for testing crash handler)
- `Ctrl+Alt+Shift+R` - Recover document from backup

Debug macros:
- `TRACEARGS(...)` - Print debug output (debug builds)
- `PRINTARGS(...)` - Print output (all builds)

### Working with Extensions

Extensions are in `data/extensions/`. Each has a `package.json` manifest. Extensions can include:
- Palettes (.gpl files)
- Themes (theme.xml)
- Scripts (.lua files)

## Common Pitfalls

1. **Submodules**: Always clone with `--recursive` or run `git submodule update --init --recursive`
2. **MinGW**: Not supported on Windows. Must use Visual Studio.
3. **Skia**: Required dependency. Download from releases or build yourself.
4. **Smart pointers in cmd**: Don't use `ImageRef` or similar in command objects - causes undo tree issues.
5. **C++ version**: C++17 only. Some C++20 features unavailable due to macOS 10.14 target.

## Resources

- Build instructions: `INSTALL.md`
- Code style: `docs/CODING_STYLE.md`
- Contributing: `CONTRIBUTING.md`
- Source architecture: `src/README.md`
- File format spec: `docs/ase-file-specs.md`
- Scripting API: https://www.aseprite.org/docs/scripting/
- Community: https://community.aseprite.org/

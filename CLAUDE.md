# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build Commands

```bash
# Configure build with vcpkg dependencies
cmake -S . -B build -DCMAKE_TOOLCHAIN_FILE=/home/sott0n/vcpkg/scripts/buildsystems/vcpkg.cmake

# Build the project
cmake --build build

# Run tests (after building)
cd build && ctest

# Run the debugger tool
./build/tools/sdb <program_path>
# Or attach to existing process
./build/tools/sdb -p <pid>
```

## Architecture Overview

**sdb** is a simple debugger implementation in C++ that provides basic process control and debugging capabilities. The project follows a library + tool architecture:

### Core Components

- **libsdb** (src/): Core debugging library providing process management
  - `process.hpp/cpp`: Process abstraction for launching/attaching to programs and controlling execution
  - `error.hpp`: Custom exception handling with errno support

- **sdb tool** (tools/): Interactive debugger CLI using readline for command input
  - Supports launching programs or attaching to existing processes via PID
  - Uses ptrace system calls for low-level process control
  - Command-line interface with history support

### Key Design Patterns

- **RAII Process Management**: The `process` class manages process lifecycle with automatic cleanup
- **Static Factory Methods**: `process::launch()` and `process::attach()` for process creation
- **State Tracking**: Process states (stopped, running, exited, terminated) with reason codes
- **Exception-based Error Handling**: Custom `sdb::error` class for consistent error reporting

### Dependencies

- **vcpkg** for package management
- **readline**: Command-line interface with history
- **Catch2**: Unit testing framework
- **C++17**: Modern C++ features, filesystem support

The project uses CMake with separate targets for the library, main tool, and tests, following standard CMake conventions for installation and packaging.
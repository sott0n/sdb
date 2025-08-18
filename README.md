# sdb

**sdb** is a simple debugger implementation in C++ that provides basic process control and debugging capabilities using ptrace system calls.
This is based on [TartanL/sdb](https://github.com/TartanLlama/sdb) and a book ["Building a Debugger"](https://www.amazon.co.jp/Building-Debugger-Sy-Brand/dp/171850408X).

## Features

- Launch programs under debugger control
- Attach to existing processes by PID
- Process state management (stopped, running, exited, terminated)
- Interactive command-line interface with readline support
- Command history and tab completion

## How to build

```bash
cmake -S . -B build -DCMAKE_TOOLCHAIN_FILE=<path-to-vcpkg>/vcpkg/scripts/buildsystems/vcpkg.cmake
cmake --build build
```

## Usage

### Launch a program under the debugger

```bash
./build/tools/sdb /path/to/program
```

### Attach to an existing process

```bash
./build/tools/sdb -p <pid>
```

### Available commands

Once in the debugger prompt (`sdb> `):

- `continue` (or `c`) - Resume program execution
- Press Enter - Repeat the last command

## Example

```bash
# Launch a program
$ ./build/tools/sdb /bin/sleep 10
Process 12345 stopped with signal TRAP
sdb> continue
Process 12345 exited with status 0

# Attach to existing process
$ ./build/tools/sdb -p 12345
Process 12345 stopped with signal STOP
sdb> continue
```

## Troubleshooting

### Permission Issues

If you get "Operation not permitted" when attaching to a process, this is due to ptrace security restrictions:

```bash
# Check current ptrace restriction level
cat /proc/sys/kernel/yama/ptrace_scope

# Temporarily allow ptrace attach (requires sudo)
sudo sysctl kernel.yama.ptrace_scope=0

# Or run debugger with sudo
sudo ./build/tools/sdb -p <pid>

# Verify you own the target process
ps -p <pid> -o pid,ppid,user,comm
```

**ptrace_scope values:**
- `0` = no restrictions (allows attaching to any process you own)
- `1` = restricted to child processes only (default on many systems)  
- `2` = admin-only attach
- `3` = no attach allowed

## Testing

```bash
cd build && ctest
```
# Signals-For-C

A Linux C program that monitors a log file in real time using POSIX signals and process forking. The parent process forks a child to watch `logfile.log` — when the child detects `INFO`, `WARNING`, or `ERROR` entries, it signals the parent via `SIGUSR1` and a pipe, which then spawns numbered Task processes and prints appropriate output.


## Features

- **Parent/child architecture** — parent sets up signal handlers and forks a monitor child
- **Real-time log detection** — uses `inotify` on Linux for immediate response to new log lines; falls back to polling on other platforms
- **FIFO event delivery** — pipe ensures events are processed in strict order even if signals arrive in bursts
- **Log rotation support** — detects inode changes and reopens the file transparently
- **Three log levels handled:**
  | Prefix | Output |
  |--------|--------|
  | `INFO` | `Task N started (PID: ...)` |
  | `WARNING` | `Warning detected in log file!` then `Task N started (PID: ...)` |
  | `ERROR` | `Exception detected in log file!` then `Task N started (PID: ...)` |

## Requirements

- GCC
- Linux (uses `inotify`; polling fallback available for other POSIX systems)

## Build

```bash
gcc -std=c11 -Wall -Wextra -O2 -o monitor monitor.c
```

## Usage

Place `logfile.log` in the current directory, then run:

```bash
./monitor
```

To test dynamic/real-time detection, append lines while the program runs:

```bash
echo "$(date '+%Y-%m-%d %H:%M:%S') INFO  Service started"   >> logfile.log
echo "$(date '+%Y-%m-%d %H:%M:%S') WARNING High memory use" >> logfile.log
echo "$(date '+%Y-%m-%d %H:%M:%S') ERROR  DB connection timeout" >> logfile.log
```

Stop with `Ctrl+C`.

## Example Output

```
Parent process (PID: 3728) monitoring log file.

Task 1 started (PID: 3729)

Exception detected in log file!

Task 2 started (PID: 3730)

Exception detected in log file!

Task 3 started (PID: 3731)

Exception detected in log file!

Task 4 started (PID: 3732)

Task 5 started (PID: 3733)
```

## Reason
```
It is a small learning experience for me on how I get to use terminal on Linux and how signals worked in C.
```

## Author

Edward Moon

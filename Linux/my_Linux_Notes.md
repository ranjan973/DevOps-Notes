# Linux, Unix and C Notes

## History of C, Unix and Linux

C was invented by Dennis Ritchie at Bell Labs in the early 1970s to build the Unix operating system. Before C, operating systems were written in assembly language, which was tied to a specific CPU architecture.

C provided a higher-level language that could still access hardware efficiently. Programs could be written once in C and then compiled into machine code for different processors, making software and operating systems much more portable.

Unix's successful rewrite in C proved that large operating systems could be moved across different hardware platforms with minimal changes.

The first compiler was written in assembly language and could compile C code into machine code for a specific CPU architecture. Over time, compilers became self-hosting and were able to compile newer versions of themselves.

Modern compilers such as GCC can compile multiple programming languages and generate machine code for multiple CPU architectures, making software portable across different hardware platforms.

GNU/Linux doesn't derive from UNIX but is considered a Unix like OS. Linux follows the Unix philosophy and is considered a Unix-like operating system. Developed in 1991 by Linus Torvalds.

Linux is the kernel name not OS.

The OS name is:

GNU/Linux → Kernel + GNU provided utilities form the OS

GNU is a different project that comes up with the utilities.

---

## System Calls

Applications invoke system calls whenever they need privileged services such as:

- File access
- Process creation
- Networking
- Memory management

The system call causes the CPU to switch from user mode to kernel mode, where the kernel executes the requested operation and returns the result back to the application.

### open() vs openat()

`open()` is not the actual system call.

`open()` is a library function provided by glibc that eventually invokes the kernel's open-related system call on your behalf.

The kernel sees `openat()`, not your C function call.

`openat()` is a real kernel system call.

---

## Source Code, Bytecode and Machine Code

Bytecode is an intermediate, platform-independent instruction set generated from source code.

It is not machine code and cannot be executed directly by the CPU.

In interpreted languages such as Python, the interpreter converts the source code to bytecode and executes the bytecode.

The interpreter itself is a compiled machine-code program running on the CPU.

In languages such as Java, a JIT compiler may further convert bytecode into native machine code during execution for improved performance.

The interpreter does not generate a separate machine-code file.

Instead, it reads each bytecode instruction and executes the corresponding machine-code routine that is already part of the interpreter.

Example:

```python
if opcode == LOAD_CONST:
    execute machine instructions to load value
```

Here `LOAD_CONST` is bytecode.

So the interpreter is not continuously translating the entire program into machine code.

In contrast, a JIT Java compiler converts bytecode into native machine code during runtime for faster execution.

### Quick Summary

Machine Code = CPU understands

Bytecode = Interpreter/JVM understands

Source Code = Humans understand

---

## Bash Execution Model

Bash is an interpreted shell.

It does not typically compile shell scripts into machine code.

Instead, it reads commands, parses them, and either executes built-in shell functions or starts external programs using system calls such as `fork()` and `execve()`.

The external programs themselves are already compiled machine-code executables that the kernel loads and runs.

When we run:

```bash
ls -l /tmp
```

Flow:

```text
bash
  ↓
fork()
  ↓
child process
  ↓
execve("/bin/ls")
```

The kernel then loads the `ls` executable into memory.

CPU executes the `ls`.

The `ls` program was already compiled earlier by GCC.

The shell is not compiling anything.

It simply asks the kernel to run an already-compiled executable.

With bash scripting:

Bash does not compile the script into machine code first.

It interprets the script line by line.

This is similar to Python except it doesn't generate the bytecode.

---

## Linux File Extensions

In Linux, the file extension doesn't matter.

There is a magic header at the top of each file which determines the file type.

---

## UNIX Today

Unix OS was discontinued in 1980s but several unix based OS like:

- HPUX
- AIX
- Solaris

are present.

UNIX is a trademark of Open Group which certifies OS if they are Unix based.

macOS is one of the certified Unix OS.

---

# Useful Commands

## User Information

```bash
who
```

Shows who is logged in to the system.

```bash
w
```

Shows who has logged in to the system and also shows what command it is running.

Shows a bit more info than `who`.

---

## Directory Navigation

```bash
cd -
```

Takes back to the previous working directory.

---

## ls Command

```bash
ls -1
```

Lists just the file/folder name in vertical line without any other details.

```bash
ls -lS
```

Sort based on size (Biggest on Top).

```bash
ls -i file.txt
```

Shows inode number of file.txt.

---

## whereis

```bash
whereis libc
```

It can show the location of a library.

Example:

```text
libc:
/usr/lib/x86_64-linux-gnu/libc.a
/usr/lib/x86_64-linux-gnu/libc.so
/usr/share/man/man7/libc.7.gz
```

---

## rm

```bash
rm -i
```

Prompt before every removal.

---

## File Comparison Commands

### diff

```bash
diff file1 file2
```

Compares files line by line.

Can also recursively diff multiple files in a folder.

### cmp

```bash
cmp file1 file2
```

Compares 2 files byte by byte.

### comm

```bash
comm file1 file2
```

Compares 2 sorted files line by line.

---

# Package Management

## Debian / Ubuntu

```bash
dpkg -L coreutils
```

Search all files installed by coreutils.

This contains the well known commands like:

- ls
- cp
- mv
- rm

```bash
dpkg -S /bin/ls
```

Search from which package `ls` is coming from.

---

## RHEL / CentOS

```bash
rpm -qf /bin/ls
```

Search from which package `/bin/ls` is coming from in RedHat.

Output:

```text
coreutils-8.32-40.el9.x86_64
```

```bash
rpm -ql coreutils
```

Search the commands or binaries in the coreutils package.

### Quick Reminder

```text
-l = list files in a package
-f = find package owning a file
```

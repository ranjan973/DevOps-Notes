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
# Std Input/Output/Error, Redirection and Pipe

STDIN -> 0

STDOUT -> 1

STDERR -> 2

```bash
> file
```

-> Redirect output to a file

```bash
>&1
```

-> Redirect to a file descriptor

```bash
2>&1
```

-> STDERR follows STDOUT

```bash
1>&2
```

-> STDOUT follows STDERR

Think of `&` as:

> "The number after me is a file descriptor, not a filename."

### Discard everything

```bash
command > /dev/null 2>&1
```

STDOUT goes to `/dev/null` and STDERR goes to where STDOUT is going, which is `/dev/null`.

Modern syntax:

```bash
command &> /dev/null
```

### Pipe

```bash
|
```

Is used to pass the output of one command to the input of a different command.

The STDOUT of the first command becomes the STDIN of another command after the pipe.

---

## Permissions and Ownership

```bash
chmod
```

-> Change the permissions of a file or folder.

```bash
chown
```

-> Change the owner and group ownership of a file or folder.

```bash
chgrp
```

-> Specifically used to change the group ownership of a file or folder.

---

# Hard Link & Soft Link

```bash
ln -s $PWD/file1.txt /tmp/file1.txt
```

-> Create a soft link.

If the original file is deleted, this link will not work.

It's like a shortcut of the file which points to the location of the file.

```bash
ln file1.txt /tmp/file1.txt
```

-> Creates a hard link and points to the inode of the file directly.

The hard link exists even if the original file is deleted.

It's a pointer to the original file so if original file is modified, the hard link file is also modified.

A hard link is not a copy of the data; it is another name for the same file.

### Why use a hard link instead of a copy?

A hard link creates another filename for the same inode, so no duplicate data is stored.

It saves disk space, allows multiple names for the same file, and ensures the file remains available until all hard links are removed.

A copy creates a completely separate inode and duplicate data.

It's an effective way of taking backups since the modification in original file automatically gets in the hard link file.

A copy of the file won't have that.

---

# find Command

```bash
find . -iname "*.yaml"
```

-> Find all the `.yaml` files dynamically within the current directory.

By default, `find` searches recursively.

`-i` is for case-insensitive.

```bash
find . -maxdepth 2 -name "*.yaml"
```

```bash
find . -type d
```

-> Will only show directories.

Types:

```text
f = file
d = directory
l = link
p = named pipe
s = socket
```

```bash
find . -perm 777
```

-> Finds files with 777 permissions.

```bash
find . -type f -empty
```

-> Finds empty files.

Can find empty directories with `-type d`.

```bash
find . -type f -user aranjan
```

-> Find files owned by user `aranjan`.

### Finding files by size

```bash
find /var -type f -size +100M
```

-> Finds files bigger than 100 MB.

```text
+100M = greater than 100 MB
-100M = less than 100 MB
100M  = exactly 100 MB
```

### Finding files by time

```bash
find . -type f -mtime +7
```

-> Find files modified more than 7 days ago.

```text
-mtime -7 = modified within last 7 days
-mtime +7 = modified more than 7 days ago
-mtime 7  = modified around 7 days ago
```

```bash
find /etc -type f -mmin -60
```

-> Find config files modified in the last 1 hour.

```bash
find . -type f -atime +30
```

-> Find files not accessed for more than 30 days.

### Delete old log files

```bash
find /var/log -type f -name "*.log" -mtime +30 -delete
```

-> Delete old log files older than 30 days.

### Execute a command on matched files

```bash
find /var/log -type f -name "*.log" -exec ls -lh {} \;
```

-> Find `.log` files and show details.

```text
{}  = each matched file
\;  = end of exec command
```

---

# xargs

`xargs` reads input from standard input and converts it into command-line arguments for another command.

It is commonly used with commands like `find`, `grep`, and `locate` to process large lists of files efficiently.

Compared to `find -exec`, `xargs` is often faster because it can pass many arguments to a command in a single execution.

Example:

```bash
find . -name "*.log" | xargs rm
```

---

# Compression and Archiving

```bash
gzip file.txt
```

Compress.

```bash
gzip -k file.txt
```

Compress and keep original file.

```bash
gzip -9 file.txt
```

Maximum compression.

```bash
gunzip file.txt.gz
```

Decompress.

```bash
gzip -d file.txt.gz
```

Same as `gunzip`.

### Read compressed files

```bash
zcat file.gz
```

Display contents.

```bash
zless file.gz
```

View contents.

```bash
zgrep ERROR file.gz
```

Search contents.

### tar

```bash
tar -t -f files.tar
```

View files without extracting.

```bash
tar -czvf backup.tar.gz dir/
```

Create compressed archive.

```bash
tar -xzvf backup.tar.gz
```

Extract compressed archive.

### How gzip works

`gzip` uses the DEFLATE algorithm (LZ77 + Huffman coding) to find repeated patterns and encode common data using fewer bits, creating a `.gz` file.

`gunzip` reverses the process and restores the original file exactly.

---

# Shell Scripting

A critical use case for `env` is in script headers (shebangs).

Instead of hardcoding a binary path like:

```bash
#!/bin/python3
```

developers use:

```bash
#!/usr/bin/env python3
```

This tells the system to look up the execution path of `python3` dynamically using the user's current `$PATH` variable, making scripts highly portable across different Linux distributions and macOS.

```text
.sh
```

extension is just for human convenience.

The script will execute even with `.txt` or no extension.

```bash
!vi
```

This is a history expansion feature in shells like bash.

It means:

> Execute the most recent command from your history that starts with vi.

---

# Variables and Data Types

There are no variables or data types in bash.

To assign a value to a variable use `=` and reference it with `$`.

Example:

```bash
echo $rating
```

### Important Note

Do not use spaces around the `=` operator when assigning variables in shell scripts.

Wrong:

```bash
var = 25
```

Correct:

```bash
var=25
```

Bash interprets:

```bash
var = 25
```

as:

```text
command   : var
argument1 : =
argument2 : 25
```

Example:

```bash
session="Linux Basics"
attendance=20
```

```text
"double quotes"  -> variables expand, safer
no quotes        -> variables expand, but less safe
'single quotes'  -> variables do not expand
```

Best Practice:

```bash
echo "$session attendance is $attendance"
```

---

# Arrays

Arrays can hold several values under one variable name.

### Create Array

```bash
arr=("one" "two" three)
```

Double quotes are NOT mandatory if there are no spaces in an element.

### This will print only the first element

```bash
echo "$arr"
```

Equal to:

```bash
echo "${arr[0]}"
```

### Access element

```bash
echo "${arr[0]}"
```

### Print all elements

```bash
echo "${arr[@]}"
```

### Print array length

```bash
echo "${#arr[@]}"
```

### Print indexes

```bash
echo "${!arr[@]}"
```

### Add element

```bash
arr+=("four")
```

### Update element

```bash
arr[1]="TWO"
```

### Delete element

```bash
unset arr[1]
```

### Delete full array

```bash
unset arr
```

### Loop through values

```bash
for item in "${arr[@]}"
do
    echo "$item"
done
```

### Loop through indexes

```bash
for i in "${!arr[@]}"
do
    echo "$i = ${arr[$i]}"
done
```

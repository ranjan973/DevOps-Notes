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
# Getting Input

## 1) Passing arguments at the time of invoking the script and accessing them using `$1`, `$2` etc.

Example:

```bash
./myscript.sh abc 123

echo $1
echo $2
echo $3
```

This will print:

```text
abc
123
space
```

### Notes

```text
$0 contains the script name.
$1, $2, $3, ... contain positional arguments.
$# returns the total number of arguments passed.
$@ returns all arguments as separate words.
$* returns all arguments as a single string.
```

## 2) Taking input using `read` command and storing it in a variable

Example:

```bash
echo "What is your name?"
read name
echo "Hello $name"
```

---

# ASCII

ASCII = A standard mapping of characters to numbers.

Problem solved:

Different computers needed a common way to represent text.

Example:

```text
A = 65
a = 97
0 = 48
```

ASCII uses 7 bits → 128 characters and is the foundation of modern text encoding systems like Unicode.

---

# Arithmetic and String Comparison Operators

Bash uses different operators for numbers and strings.

Integer comparisons commonly use:

```text
-eq
-ne
-gt
-ge
-lt
-le
```

String comparisons use operators like:

```text
=
!=
<
>
-z
-n
```

```text
-z = String is empty/null
-n = String is not empty
```

In Bash, `[[ condition ]]` is safer and more powerful than `[ condition ]`.

In Bash, `(( ))` is preferred for arithmetic conditions because it looks more like normal programming syntax.

Inside `(( ))`, you can use symbols like:

```text
>
<
==
!=
>=
<=
```

Do not use `>` and `<` for numeric comparison inside `[ ]`.

This is wrong for numeric comparison because `>` is treated as a string comparison or redirection operator, depending on the context.

## Best Practice

For Bash scripting, use `(( ))` for numbers:

```bash
if (( count > 10 )); then
    echo "Count is greater than 10"
fi
```

Use `[[ ]]` for strings:

```bash
if [[ "$env" == "prod" ]]; then
    echo "Production environment"
fi
```

Inside `(( ))`, Bash already treats variable names as arithmetic variables, so `$` is optional and usually avoided.

Example:

```bash
(( a > b ))
```

is preferred over:

```bash
(( $a > $b ))
```

But inside `[[ ]]`, use `$var` to access the variable value for string comparison:

```bash
[[ "$name" == "Anand" ]]
```

---

# Integer Checking Condition

```bash
if ! [[ "$num1" =~ ^-?[0-9]+$ && "$num2" =~ ^-?[0-9]+$ ]]; then
```

Meaning:

If `num1` and `num2` are not both valid integers, then show error.

```text
=~      Matches regular expression
^       Start of input
-?      Optional minus sign
[0-9]+  One or more digits
$       End of input
```

---

# What is Regular Expression?

Regular Expressions (Regex) are one of the most powerful tools we use in Linux, Bash, grep, sed, awk, Python, and many DevOps tools.

Think of regex as:

> A language for describing text patterns.

Instead of looking for an exact string, regex lets you search for patterns.

## Quick Regex Cheat Sheet

```text
^       Start of line
$       End of line
.       Any character
*       0 or more
+       1 or more
?       0 or 1
[]      Character class
[^]     NOT
{n}     Exactly n times
{n,m}   Between n and m times
|       OR
()      Grouping
```

## Most Common DevOps Regex

```text
^[0-9]+$              # Positive integer
^-?[0-9]+$            # Integer (can optionally contain negative values as well)
^[a-zA-Z]+$           # Letters only
^[a-zA-Z0-9]+$        # Alphanumeric
^[a-zA-Z0-9._-]+$     # Username/file name
^[0-9]{10}$           # 10-digit number
```

---

# Case in Shell Scripting

In shell scripting, `case` is a conditional control structure used to compare one value against multiple patterns.

The same logic using `if-elif-else` would become very messy.

Example:

```bash
case "$choice" in
    start|Start)
        echo "Starting service"
        ;;
    stop|Stop)
        echo "Stopping service"
        ;;
    restart|Restart)
        echo "Restarting service"
        ;;
    *)
        echo "Invalid option"
        ;;
esac
```

## IMP Note: Case Is Pattern Matching, Not Just Exact Matching

```text
*       Matches anything
?       Matches any single character
[abc]   Matches one character: a, b, or c
[0-9]   Matches one digit
[a-z]   Matches lowercase letter range
*.txt   Matches anything ending with .txt
file?   Matches file1, fileA, etc.
```

Example:

```bash
#!/usr/bin/env bash

echo "Enter a single digit:"
read num

case "$num" in
    [0-9])
        echo "You entered a single digit"
        ;;
    *)
        echo "Not a single digit"
        ;;
esac
```

## Key Points

```text
case checks one value against multiple patterns.
It is cleaner than long if-elif-else for menus/options.
Patterns are checked from top to bottom.
First matching branch runs.
;; ends a branch.
*) is the default branch.
| is used for OR patterns.
case uses shell glob patterns, not regex.
Always quote the variable: case "$var" in
Use esac to close the block.
```

---

# Conversion Inputs to Upper or Lower Case in Bash

Example:

```bash
name="aNaNd"

echo "${name,,}"   # anand
echo "${name^^}"   # ANAND
echo "${name^}"    # ANaNd
echo "${name,}"    # aNaNd
```

---

# Integer Expansion & Command Substitution

```bash
today=$(date)
```

OR

```bash
today=`date`
```

OR

```bash
files=$(ls)
```

→ Command substitution

```bash
sum=$((5 + 3))
```

→ Arithmetic expansion

Without arithmetic expansion:

```bash
echo "$a" + "$b"
```

Output:

```text
10 + 20
```

(just text)

With arithmetic expansion:

```bash
a=10
b=20

echo $(( a + b ))
```

Output:

```text
30
```

We can also write:

```bash
echo $(($a + $b))
```

Note:

Bash arithmetic expansion works with integers only.

---

# While and Until Loop

```text
while = Continue WHILE condition is true
until = Continue UNTIL condition becomes true
```

A while loop runs as long as the condition is TRUE.

Syntax:

```bash
while condition
do
    commands
done
```

An until loop runs as long as the condition is FALSE.

Syntax:

```bash
until condition
do
    commands
done
```

## Use while when the condition naturally describes continuation

Examples:

```bash
while (( count <= 10 ))
while read line
while [[ -n "$input" ]]
```

## Use until when the condition naturally describes the stopping point

Examples:

```bash
until ping -c1 server >/dev/null
until [[ -f file.txt ]]
until (( retries == 5 ))
```

---

# exit, break and continue in Shell Scripting

```text
break    -> Terminates the loop
continue -> Skips the current iteration and moves to the next one
exit     -> Exit script
```

---

# Process Management

```bash
pstree -a
```

Shows the running processes in a tree format along with the arguments.

A process is an OS/kernel entity identified by a PID.

A job is a shell construct used to manage one or more processes.

Jobs can run in the foreground, background, or be stopped.

Background processes are typically managed as jobs by the shell, but not every process on the system is a shell job.

```text
&      -> Run a command in the background
jobs   -> Lists active jobs and status in the current shell
bg     -> Put a job in the background
fg     -> Bring a background job to the foreground
wait   -> Wait for background processes to finish
```

## Difference Between Job and Process

```bash
jobs      # show jobs
bg %1     # continue job in background
fg %1     # bring job to foreground
kill %1   # kill a job
```

```text
Ctrl + Z -> Stops a job
```

If you want to start it again:

```bash
bg <job_number>
```

A job is simply a shell-managed group of one or more processes.

When you start commands from a shell (Bash), the shell assigns them job numbers.

One job can have multiple processes.

Shell assigns the job a Job ID such as:

```text
%1
%2
```

A job exists only within the current shell session.

A process is a running instance of a program managed by the operating system.

It exists regardless of a shell.

---

# Text Processing Commands

```text
grep -> Searches for patterns in text files
sed  -> Processes and transforms text streams
awk  -> Processes and analyzes text files
sort -> Sort lines of text files
cut  -> Removes sections from each line of files
tr   -> Translates or deletes characters
```
# grep - Global Regular Expression Print

Searches text and prints lines that match a pattern.

Examples:

```bash
grep error app.log
```

Prints all lines containing the word `error` in `app.log`.

```bash
grep error *.log
```

Searches all log files.

```bash
grep -n error app.log
```

Displays line numbers as well.

```bash
grep -c error app.log
```

Counts the number of lines which match `error`.

**Note:** This is NOT the number of occurrences of the word.

```bash
grep -v error app.log
```

Show lines NOT containing `error`.

```bash
grep -w root file.txt
```

Matches only the exact word.

```bash
grep -l error *.log
```

Shows file names only.

```bash
grep -L error *.log
```

Lists files that do not contain `error`.

```bash
grep -A3 ERROR app.log
```

Show match + next 3 lines.

```bash
grep -B3 ERROR app.log
```

Shows 3 lines before the match.

```bash
grep -C3 ERROR app.log
```

Context around the match.

Shows 3 lines before and 3 lines after.

```bash
grep -r error
```

Searches for `error` recursively including files under subdirectories.

```bash
grep -q error app.log
```

Suppresses the output and allows checking the status of the previous command using:

```bash
echo $?
```

```text
0        = Success (Pattern found)
Non-zero = Failure (Pattern not found)
```

This feature can help us write automation scripts to check whether a certain pattern or word exists in a file and take actions based on the command's exit status.

---

## Regular Expressions (Important)

```bash
grep '^root' /etc/passwd
```

Find lines starting with `root`.

```bash
grep 'bash$' /etc/passwd
```

Find lines ending with `bash`.

Will print all lines that end with `bash`.

```bash
grep '^root$' file.txt
```

Exact line match.

```bash
grep 'a.c' file.txt
```

Find any character between `a` and `c`.

Examples:

```text
abc
axc
a1c
```

---

## Extended Regex -> grep -E or egrep

```bash
grep -E 'error|warning'
```

Matches either `error` or `warning`.

### BRE vs ERE

In Basic RegEx (BRE), characters like `+`, `?`, `|`, and `()` are treated as ordinary characters, not regex operators.

To give them a special regex meaning, you must escape them with `\`.

Example:

You want:

```text
One or more digits
```

BRE:

```text
^[0-9]\+$
```

Here `\+` means:

```text
Treat + as the regex operator "one or more"
```

Without the backslash:

```text
^[0-9]+$
```

BRE interprets `+` as a literal plus sign, not a quantifier.

Extended RegEx (ERE) says `+ ? | ()` are already special, so no backslash is needed.

Much easier to read.

### Remember

```bash
[[ string =~ regex ]]
```

→ ERE

```bash
grep pattern
```

→ BRE (default)

```bash
grep -E pattern
```

→ ERE

```text
grep  -> Basic Regular Expressions
egrep -> Extended Regular Expressions
```

---

# sed - Stream Editor

```text
grep -> Find lines
sed  -> Modify lines
awk  -> Process fields/columns
```

Examples:

```bash
sed 's/old/new/g' file
```

Replace all occurrences of `old` with `new`.

```bash
sed -i 's/old/new/g' file
```

Modify file in place.

```bash
sed -n '/pattern/p' file
```

Print only matching lines.

```bash
sed '/pattern/d' file
```

Delete matching lines.

```bash
sed '/^$/d' file
```

Delete blank lines.

```bash
sed 's/^/#/' file
```

Add `#` at the beginning of every line.

```bash
sed 's/^#//' file
```

Remove leading `#`.

```bash
sed -n '5p' file
```

Print only line 5.

```bash
sed -n '$p' file
```

Print the last line.

```bash
sed -E 's/[0-9]+/NUMBER/g'
```

Replace one or more digits with `NUMBER`.

```bash
sed '/start/,/end/d' file
```

Delete everything from `start` to `end`.

---

# awk

## Basic Syntax

```bash
awk 'pattern { action }' file
```

Example:

```bash
awk -F: '$7 == "/bin/bash" {print $1}' /etc/passwd
```

---

## How This Command Works

- **`awk`** → Invokes the awk text-processing utility.
- **`-F:`** → Sets the field separator (delimiter) to a colon (`:`), which is how fields are split in the `/etc/passwd` file.
- **`$7 == "/bin/bash"`** → Checks if the 7th field (the login shell column) exactly matches `/bin/bash`.
- **`{print $1}`** → Prints only the 100% matching 1st field (the username).
- **`/etc/passwd`** → Specifies the file path to read from.

---

## Common Pattern Matching Examples

### 1. Match Pattern Across an Entire Line

```bash
awk '/error/' logfile.txt
```

Matches records containing the word `error`.

### 2. Match Pattern in a Specific Column

```bash
awk '$1 ~ /apple/' data.txt
```

Matches rows where column 1 contains `apple`.

### 3. Negative Pattern Match

```bash
awk '$2 !~ /yellow/' data.txt
```

Matches rows where column 2 does NOT contain `yellow`.

### 4. Match Exact Strings (Logical Comparisons)

```bash
awk '$1 == "admin"' users.txt
```

Matches rows where column 1 is exactly `admin`.

### 5. Regex Match in Specific Column

```bash
awk -F: '$7 ~ /(nologin|false|sync)$/{print $0}' /etc/passwd
```

If column 7 ends with:

```text
nologin
false
sync
```

then print the entire record.

```text
$0 = Entire record (entire line)
```

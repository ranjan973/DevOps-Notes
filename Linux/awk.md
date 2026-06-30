# AWK Quick Reference

## What is awk?

`awk` is a text-processing and reporting tool that works **record by record (line by line)** and automatically splits each line into **fields (columns)**.

Use awk when you need to:

- Extract columns
- Filter records
- Perform calculations
- Generate reports
- Process CSV, log, and configuration files

```text
grep -> Find lines
sed  -> Modify lines
awk  -> Work with fields and data
```

---

# How awk Works

For every input line:

```text
Read line
↓
Split into fields using FS
↓
Check pattern
↓
Execute action if pattern matches
↓
Move to next line
```

Example:

```bash
awk '$2 > 100 {print $1}' file
```

```text
Pattern : $2 > 100
Action  : print $1
```

---

# Syntax

```bash
awk 'pattern { action }' file
```

Examples:

Print all lines:

```bash
awk '{print}' file
```

Print matching records:

```bash
awk '$3 > 100' file
```

Print specific fields:

```bash
awk '$3 > 100 {print $1}' file
```

---

# BEGIN and END

## BEGIN

Runs once before reading input.

```bash
awk 'BEGIN {print "Report Started"}'
```

Common uses:

- Set FS/OFS
- Print headers
- Initialize variables

---

## END

Runs once after all input has been processed.

```bash
awk 'END {print NR}' file
```

Common uses:

- Print totals
- Print averages
- Print summaries

---

## Example

```bash
awk '
BEGIN {print "Users"}
$3 > 1000 {count++}
END {print "Count =", count}
' file
```

---

# Most Important Built-in Variables

## $0

Entire record (line)

```bash
awk '{print $0}'
```

---

## $1, $2, $3 ... $NF

Fields (columns)

```bash
awk '{print $1,$3}'
```

---

## NF

Number of fields in current record.

```bash
awk '{print NF}'
```

---

## NR

Current record (line) number.

```bash
awk '{print NR,$0}'
```

---

## FNR

Line number within the current file.

Useful when processing multiple files.

---

## FS

Input Field Separator.

Default:

```text
Whitespace (space/tab)
```

Examples:

```bash
awk -F: '{print $1}' /etc/passwd
```

```bash
awk 'BEGIN {FS=":"} {print $1}' /etc/passwd
```

---

## OFS

Output Field Separator.

```bash
awk 'BEGIN {OFS=":"} {print $1,$2,$3}'
```

Output:

```text
field1:field2:field3
```

---

## RS

Input Record Separator.

Default:

```text
Newline
```

---

## ORS

Output Record Separator.

Default:

```text
Newline
```

---

# Pattern Matching

## Match Operator (~)

```bash
$7 ~ /bash$/
```

Field 7 ends with `bash`.

---

## Not Match Operator (!~)

```bash
$7 !~ /bash$/
```

Field 7 does not end with `bash`.

---

## Regex OR

```bash
$7 ~ /(nologin|false|sync)$/
```

Equivalent to:

```bash
$7 ~ /nologin$/ || $7 ~ /false$/ || $7 ~ /sync$/
```

---

# Logical Operators

AND:

```bash
&&
```

OR:

```bash
||
```

NOT:

```bash
!
```

Examples:

```bash
$3 > 1000 && $7 ~ /bash$/
```

```bash
$3 == 0 || $7 ~ /bash$/
```

---

# Most Common Operations

## Print a Field

```bash
awk '{print $1}'
```

---

## Print Last Field

```bash
awk '{print $NF}'
```

---

## Print Line Number

```bash
awk '{print NR,$0}'
```

---

## Count Records

```bash
awk 'END {print NR}' file
```

Equivalent to:

```bash
wc -l file
```

---

## Count Matching Records

```bash
awk '/error/ {count++} END {print count}' logfile
```

Example:

```bash
awk -F: '$7 ~ /(nologin|false|sync)$/ {count++} END {print count}' /etc/passwd
```

---

## Sum a Column

```bash
awk '{sum += $2} END {print sum}'
```

---

## Average a Column

```bash
awk '{sum += $2} END {print sum/NR}'
```

---

# Common Functions

## length()

Length of string.

```bash
awk '{print length($1)}'
```

---

## toupper()

Convert to uppercase.

```bash
awk '{print toupper($1)}'
```

---

## tolower()

Convert to lowercase.

```bash
awk '{print tolower($1)}'
```

---

## substr()

Extract substring.

```bash
awk '{print substr($1,1,3)}'
```

---

## split()

Split a string into an array.

```bash
split($0, arr, ",")
```

---

## sub()

Replace first occurrence.

```bash
sub("old","new")
```

---

## gsub()

Replace all occurrences.

```bash
gsub("old","new")
```

---

## index()

Return position of substring.

```bash
index($0,"Linux")
```

---

## printf()

Formatted output.

```bash
printf "%-15s %5d\n",$1,$2
```

---

# Associative Arrays (Very Important)

Count occurrences:

```bash
awk '
{
    count[$1]++
}
END {
    for(i in count)
        print i, count[i]
}'
```

Input:

```text
apple
apple
banana
apple
```

Output:

```text
apple 3
banana 1
```

---

# Useful Loops

## for Loop

```bash
awk '
{
    for(i=1;i<=NF;i++)
        print $i
}'
```

---

## while Loop

```bash
awk '
BEGIN {
    i=1
    while(i<=5) {
        print i
        i++
    }
}'
```

---

# Real System Administration Examples

## Print Usernames

```bash
awk -F: '{print $1}' /etc/passwd
```

---

## Print Usernames and Shells

```bash
awk -F: '{print $1,$7}' /etc/passwd
```

---

## Users Using Bash

```bash
awk -F: '$7 ~ /bash$/'
```

---

## Count Service Accounts

```bash
awk -F: '$7 ~ /(nologin|false|sync)$/ {count++} END {print count}' /etc/passwd
```

---

## Memory Usage

```bash
free -m | awk '/Mem:/ {print $3}'
```

---

## Disk Usage

```bash
df -h | awk 'NR>1 {print $1,$5}'
```

---

## Remove Duplicate Lines

```bash
awk '!seen[$0]++'
```

Input:

```text
A
A
B
B
```

Output:

```text
A
B
```

---

# AWK Mental Model

```text
BEGIN  -> Setup

Pattern -> Decide which records to process

Action  -> What to do with matching records

END     -> Print summary/report
```

---

# Essential Built-in Variables

```text
$0      Entire record (line)
$1..$NF Fields (columns)
NF      Number of fields
NR      Current line number
FNR     Line number within current file
FS      Input field separator
OFS     Output field separator
RS      Input record separator
ORS     Output record separator
```

---

# Essential Operators

```text
~       Regex match
!~      Regex not match

==      Equal
!=      Not equal
> <     Greater/Less than
>= <=   Greater/Less than or equal

&&      AND
||      OR
!       NOT
```

---

# 80% AWK Commands Used in Real Life

```bash
awk '{print $1}'

awk '{print $NF}'

awk '{print NR,$0}'

awk -F',' '{print $1,$3}'

awk '/error/'

awk '$2 > 100'

awk '{sum += $1} END {print sum}'

awk '{sum += $1} END {print sum/NR}'

awk '!seen[$0]++'

awk -F: '{print $1}' /etc/passwd

awk -F: '$7 ~ /bash$/'

awk -F: '$7 ~ /(nologin|false|sync)$/ {count++} END {print count}'
```

---

# Interview Summary

```text
grep -> Which lines?
sed  -> Modify lines.
awk  -> Work with fields, calculations, filtering, and reporting.

BEGIN  -> Setup
Pattern -> Select records
Action  -> Process records
END     -> Print summary

Master:
- FS/OFS
- NR/NF
- $0, $1...$NF
- ~ and !~
- Conditions
- Sum/Count/Average
- Associative Arrays

These cover the majority of real-world Linux Admin,
DevOps, SRE, and shell scripting use cases.
```

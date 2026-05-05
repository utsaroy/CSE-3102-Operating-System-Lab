# Operating Systems Laboratory — Lab 2

## Shell Programming Tutorial (POSIX `sh` with useful `bash` extras)

> **Student Handout + Tutorial (expanded)**  
> This lab introduces **shell scripting**: writing executable command files that automate tasks, take input, make decisions, and run loops.

---

## Why shell scripting matters for OS lab

In Operating Systems labs and real system work, shell scripts are used to:

- automate repetitive commands
- set up experiments and compile/run programs
- manage files, permissions, and logs
- process text output (filters like `grep`, `sort`, `wc`)
- write admin-style tools (menus, checks, backups)

A shell script is often the “glue” between OS tools.

---

## Learning outcomes

By the end of this lab, you should be able to:

- explain what a shell is and how scripts run
- create executable scripts with a **shebang** (`#!/bin/sh`)
- write comments and variables (including quoted strings)
- capture command output into variables (command substitution)
- read input from keyboard (`read`) and from command-line arguments (`$1`, `$2`, …)
- use `if/then/else`, `elif`, and `case/esac` for branching
- write loops with `for` and `while`
- follow best practices: quoting, `printf` vs `echo`, argument checking

---

## Table of contents

1. [Shell basics and running scripts](#1-shell-basics-and-running-scripts)
2. [Demo 1 — Comments, variables, and `echo`](#2-demo-1--comments-variables-and-echo)
3. [Demo 2 — Capturing command output into variables](#3-demo-2--capturing-command-output-into-variables)
4. [Demo 3 — Reading input from the keyboard (`read`)](#4-demo-3--reading-input-from-the-keyboard-read)
5. [Demo 4 — Command-line arguments](#5-demo-4--command-line-arguments)
6. [Demo 5 — Decisions with `if ... then`](#6-demo-5--decisions-with-if--then)
7. [Demo 6 — Decisions with `if ... then ... else`](#7-demo-6--decisions-with-if--then--else)
8. [Demo 7 — Nested `if` menu (and `elif` improvement)](#8-demo-7--nested-if-menu-and-elif-improvement)
9. [Demo 8 — `case ... esac` menu](#9-demo-8--case--esac-menu)
10. [Demo 9 — `for` loop with a fixed list](#10-demo-9--for-loop-with-a-fixed-list)
11. [Demo 10 — `for` loop over files (and safer alternatives)](#11-demo-10--for-loop-over-files-and-safer-alternatives)
12. [Demo 11 — `for` loop from a text file list (and safer `while read`)](#12-demo-11--for-loop-from-a-text-file-list-and-safer-while-read)
13. [Demo 12 — `for` loop over command-line arguments (`$*` vs `"$@"`)](#13-demo-12--for-loop-over-command-line-arguments--vs--)
14. [While loop example (POSIX and Bash forms)](#14-while-loop-example-posix-and-bash-forms)
15. [Extra essential notes and commands](#15-extra-essential-notes-and-commands)
16. [Practice exercise](#16-practice-exercise)
17. [Reference solutions / expected outputs](#17-reference-solutions--expected-outputs)

---

# 1. Shell basics and running scripts

## 1.1 What is a shell?

A **shell** is a program that:

- reads commands from the keyboard (or from a file)
- runs those commands
- prints output

Common shells:

- `sh` (POSIX shell; often linked to `dash` on Ubuntu)
- `bash` (Bourne Again Shell; common default)
- `zsh`, `ksh`

In this lab, the demos are written for `sh` (`#!/bin/sh`) unless stated otherwise.

---

## 1.2 The shebang (`#!`)

The first line of a script is usually the **shebang**:

```sh
#!/bin/sh
```

It tells the OS which interpreter should execute the file.

- Use `#!/bin/sh` for maximum portability.
- Use `#!/bin/bash` if you rely on Bash-only features (like `(( ... ))` arithmetic conditions, arrays, etc.).

---

## 1.3 Comments

Anything after `#` is a comment (except the shebang line):

```sh
# This is a comment
```

Comments are ignored by the shell and are used to explain code.

---

## 1.4 Creating and running scripts

1) Create a file (example):

```bash
touch shelldemo1
nano shelldemo1
```

2) Make it executable:

```bash
chmod +x shelldemo1
```

3) Run it:

```bash
./shelldemo1
```

### Why `./`?

`./` means “run the program in the current directory”. Many systems do **not** include the current directory in `$PATH` by default for safety.

### Useful checks

- Check permissions:
  ```bash
  ls -l shelldemo1
  ```
- Run without executable permission:
  ```bash
  sh shelldemo1
  ```

---

# 2. Demo 1 — Comments, variables, and `echo`

## Topic

- comments
- user-defined variables
- printing with `echo`

## Script: `shelldemo1`

```sh
#!/bin/sh
#Filename: shelldemo1 Author: RS

#Define variables
name=John
car="Ford Escort"
age=21

#Display the contents of the variables
echo "My name is $name"
echo "I am $age years old and \c"
echo "I drive a $car."
```

## Explanation

- Variables are assigned with **no spaces** around `=`.
- Use quotes when a value contains spaces: `car="Ford Escort"`.
- `$name` expands to the stored value.
- `\c` is a legacy way to suppress newline in some `echo` implementations.

## Output

```text
My name is John
I am 21 years old and I drive a Ford Escort.
```

## Extra (recommended): use `printf` for predictable formatting

Different shells treat `echo` escape sequences differently. `printf` is more consistent:

```sh
printf "My name is %s\n" "$name"
printf "I am %s years old and " "$age"
printf "I drive a %s.\n" "$car"
```

---

# 3. Demo 2 — Capturing command output into variables

## Topic

Store output of a UNIX command into a variable.

## Script: `shelldemo2`

```sh
#!/bin/sh
#Filename: shelldemo2 Author: RS

#Define variables
todaysdate=`date`
myworkingdirectory=`pwd`

#Display the contents of the variables
echo "The date is $todaysdate"
echo "My current working directory is $myworkingdirectory"
```

## Explanation

- Backticks run a command and substitute its output.
- `date` prints the current date/time.
- `pwd` prints the current working directory.

## Output (example)

```text
The date is Fri Aug 17 14:30:58 BST 2001
My current working directory is /home/martin/shelldemos
```

## Extra (recommended): modern command substitution

Use `$(...)` instead of backticks:

```sh
todaysdate=$(date)
myworkingdirectory=$(pwd)
```

---

# 4. Demo 3 — Reading input from the keyboard (`read`)

## Topic

Get user input interactively.

## Script: `shelldemo3`

```sh
#!/bin/sh
#Filename: shelldemo3 Author: RS

echo "Please enter your first name: \c"
read firstname
echo "Please enter your surname: \c"
read surname
echo "Please enter your date of birth: (dd/mm/yyyy): \c"
read dateofbirth

echo "Welcome $firstname $surname, your date of birth is on record as $dateofbirth."
```

## Explanation

- `read firstname` waits for input and stores it.
- Input comes from the keyboard (standard input).

## Output (example — user input shown after prompts)

```text
Please enter your first name: Joe
Please enter your surname: Bloggs
Please enter your date of birth: (dd/mm/yyyy): 01/04/1980
Welcome Joe Bloggs, your date of birth is on record as 01/04/1980.
```

## Extra improvements

Prefer `printf` for prompts:

```sh
printf "Please enter your first name: "
read firstname
```

---

# 5. Demo 4 — Command-line arguments

## Topic

Input supplied when launching the script.

## Script: `shelldemo4`

```sh
#!/bin/sh
#Filename: shelldemo4 Author: RS

echo "This program has obtained its input from the command line"
echo "Welcome $1 $2, your date of birth is on record as $3."
```

## How to run

```bash
./shelldemo4 Joe Bloggs 01/04/1980
```

## Output

```text
This program has obtained its input from the command line
Welcome Joe Bloggs, your date of birth is on record as 01/04/1980.
```

## Explanation: special variables

- `$0` script name
- `$1..$9` arguments 1..9
- `$#` number of arguments
- `$*` all arguments as a single word list
- `"$@"` all arguments as separate quoted items (**recommended**)

## Extra: argument count validation (recommended)

```sh
#!/bin/sh
if [ "$#" -ne 3 ]; then
  echo "Usage: $0 <FirstName> <Surname> <DOB(dd/mm/yyyy)>"
  exit 1
fi

echo "Welcome $1 $2, your date of birth is on record as $3."
```

---

# 6. Demo 5 — Decisions with `if ... then`

## Topic

Conditional execution using a test.

## Script: `shelldemo5`

```sh
#!/bin/sh
#Filename: shelldemo5 Author: RS

clear
echo "Would you like to see a joke (y/n)? \c"
read reply

if [ "$reply" = "y" ]
then
  echo "Question: How many surrealists does it take to change a light bulb?"
  echo "Answer: Fish."
fi

echo "\n\nHave a nice day."
```

## Explanation

- Tests use `[ ... ]` with **spaces**.
- String compare uses `=`.
- Quotes around `$reply` prevent errors when the user presses Enter without typing.

## Output examples

If user enters `y`:

```text
Would you like to see a joke (y/n)? y
Question: How many surrealists does it take to change a light bulb?
Answer: Fish.

Have a nice day.
```

If user enters `n`:

```text
Would you like to see a joke (y/n)? n

Have a nice day.
```

---

# 7. Demo 6 — Decisions with `if ... then ... else`

## Topic

Two-way branching.

## Script: `shelldemo6`

```sh
#!/bin/sh
#Filename: shelldemo6 Author: RS

clear
echo "Would you like to see a joke (y/n)? \c"
read reply

if [ "$reply" = "y" ]
then
  echo "Question: How many surrealists does it take to change a light bulb?"
  echo "Answer: Fish."
else
  echo "Not in the mood for jokes? Never mind perhaps another day."
fi

echo "\n\nHave a nice day."
```

## Output example (`n`)

```text
Would you like to see a joke (y/n)? n
Not in the mood for jokes? Never mind perhaps another day.

Have a nice day.
```

---

# 8. Demo 7 — Nested `if` menu (and `elif` improvement)

## Topic

Multiple choices with numeric tests.

## Script: `shelldemo7`

```sh
#!/bin/sh
#Filename: shelldemo7 Author: RS

echo "UNIX COMMAND SELECTOR"
echo "1. Show date"
echo "2. Show hostname"
echo "3. Show this month's calendar"
echo "Please make your selection (1,2,3) \c"
read menunumber

if [ "$menunumber" -eq 1 ]
then
  date
else
  if [ "$menunumber" -eq 2 ]
  then
    hostname
  else
    if [ "$menunumber" -eq 3 ]
    then
      cal
    else
      echo "INVALID CHOICE! \07\07"
    fi
  fi
fi

echo "\nThank you for using the Unix command selector."
```

## Explanation

- `-eq` tests numeric equality.
- `\07\07` attempts to beep twice (terminal dependent).

## Output (example for selection `1`)

```text
UNIX COMMAND SELECTOR
1. Show date
2. Show hostname
3. Show this month's calendar
Please make your selection (1,2,3) 1
Tues Oct 23 17:32:45 BST 2001

Thank you for using the Unix command selector.
```

## Extra (recommended): rewrite using `elif` (cleaner)

```sh
if [ "$menunumber" -eq 1 ]; then
  date
elif [ "$menunumber" -eq 2 ]; then
  hostname
elif [ "$menunumber" -eq 3 ]; then
  cal
else
  echo "INVALID CHOICE!"
fi
```

---

# 9. Demo 8 — `case ... esac` menu

## Topic

Cleaner multi-way branching.

## Script: `shelldemo8`

```sh
#!/bin/sh

echo "UNIX COMMAND SELECTOR"
echo "1. Show date"
echo "2. Show hostname"
echo "3. Show this month's calendar"
echo "Please make your selection (1,2,3) \c"
read menunumber

case $menunumber in
  1) date ;;
  2) hostname ;;
  3) cal ;;
  *) echo "INVALID CHOICE! \07\07" ;;
esac

echo "\nThank you for using the Unix command selector."
```

## Explanation

- `case` matches the value against patterns.
- `*)` is the default (anything else).
- Each branch ends with `;;`.

## Output (example: `1`)

```text
UNIX COMMAND SELECTOR
1. Show date
2. Show hostname
3. Show this month's calendar
Please make your selection (1,2,3) 1
Tues Oct 23 17:32:45 BST 2001

Thank you for using the Unix command selector.
```

---

# 10. Demo 9 — `for` loop with a fixed list

## Topic

Looping through a list of items.

## Script: `shelldemo9`

```sh
#!/bin/sh

echo "Demonstration of looping using the for loop and a list of car names"
for car in ford vauxhall rover toyota mazda subaru
do
  echo "$car"
done

echo "\nEnd of demonstration program."
```

## Output

```text
Demonstration of looping using the for loop and a list of car names
ford
vauxhall
rover
toyota
mazda
subaru

End of demonstration program.
```

---

# 11. Demo 10 — `for` loop over files (and safer alternatives)

## Topic

Looping through filenames from `ls`.

## Script: `shelldemo10` (as given)

```sh
#!/bin/sh

echo "Demonstration of looping using the for loop and a list of filenames generated by the ls command"
for myfile in `ls`
do
  cat $myfile
done
```

## Output

The screen will show the contents of all files in the current directory.

## Important note (extra, necessary information)

`for f in \`ls\`` is **unsafe** if filenames contain spaces/newlines.

### Safer alternative (recommended)

```sh
#!/bin/sh

echo "Safer file loop using a glob"
for myfile in ./*
do
  [ -f "$myfile" ] || continue
  echo "=== $myfile ==="
  cat "$myfile"
done
```

### Output

You will see a header for each file then its contents.

---

# 12. Demo 11 — `for` loop from a text file list (and safer `while read`)

## Topic

Looping through filenames stored in a text file `myfilelist`.

## Script: `shelldemo11` (as given)

```sh
#!/bin/sh

echo "Demonstration of looping using the for loop and a list of filenames held in a text file named myfilelist"
for myfile in `cat myfilelist`
do
  cat $myfile
done
```

## Output

The output will be the contents of all files whose names are listed in `myfilelist`.

## Extra (recommended): safe version using `while read`

```sh
#!/bin/sh

echo "Safe reading of myfilelist (handles spaces)"
while IFS= read -r myfile
do
  [ -f "$myfile" ] || { echo "Missing: $myfile"; continue; }
  cat "$myfile"
done < myfilelist
```

---

# 13. Demo 12 — `for` loop over command-line arguments (`$*` vs `"$@"`)

## Topic

Process all filenames passed on the command line.

## Script: `shelldemo12` (as given)

```sh
#!/bin/sh

echo "Demonstration of looping using the for loop and a list of arguments supplied at the command line"
echo "Program invoked by the command: ./shelldemo12 filename1 filename2 filename3"

for myfile in $*
do
  cat $myfile
done
```

## Output

The output will be the contents of all files listed when executing the script.

## Extra (recommended): use `"$@"` for correctness

```sh
#!/bin/sh

for myfile in "$@"
do
  cat "$myfile"
done
```

Why? Because `"$@"` preserves each argument exactly (important if names contain spaces).

---

# 14. While loop example (POSIX and Bash forms)

## Bash-only version (as provided)

```bash
#!/bin/bash
n=1
while (( n <= 5 ))
do
  echo "Welcome $n times."
  n=$(( n+1 ))
done
```

### Output

```text
Welcome 1 times.
Welcome 2 times.
Welcome 3 times.
Welcome 4 times.
Welcome 5 times.
```

## POSIX `sh` version (portable)

```sh
#!/bin/sh
n=1
while [ "$n" -le 5 ]
do
  echo "Welcome $n times."
  n=$((n+1))
done
```

---

# 15. Extra essential notes and commands

These are not explicitly in the original demos, but they are required for writing reliable scripts.

## 15.1 Quoting rules (critical)

Always quote variable expansions unless you *want* word splitting:

- Good:
  ```sh
  echo "$name"
  rm -- "$file"
  ```
- Risky:
  ```sh
  rm $file
  ```

## 15.2 String tests vs numeric tests

- String equality:
  ```sh
  [ "$a" = "$b" ]
  ```
- Numeric equality:
  ```sh
  [ "$a" -eq "$b" ]
  ```

## 15.3 File tests

Common file test operators:

- `-f file` regular file
- `-d dir` directory
- `-e path` exists
- `-r/-w/-x` readable/writable/executable

Example:

```sh
if [ -f "data.txt" ]; then
  echo "data.txt exists"
else
  echo "data.txt missing"
fi
```

## 15.4 Exit status and `&&` / `||`

Commands return an exit code:

- `0` = success
- non-zero = failure

Examples:

```sh
mkdir testdir && echo "Created testdir"
rm missingfile || echo "Failed to remove missingfile"
```

## 15.5 Debugging scripts

Trace execution:

```bash
sh -x script.sh
```

In Bash:

```bash
bash -x script.sh
```

## 15.6 Useful related commands (commonly used with scripts)

- `date`, `pwd`, `hostname`, `cal`
- `grep`, `find`, `sort`, `wc`
- `chmod`, `ls -l`
- `ps`, `top`, `kill`

---

# 16. Practice exercise

Do the following in a terminal to practice Lab 2 concepts.

## Part A — Create and run scripts

1. Create a folder:
   ```bash
   mkdir -p lab2_shell
   cd lab2_shell
   ```
2. Create script files:
   ```bash
   touch shelldemo1 shelldemo2 shelldemo3
   ```
3. Copy the code from this tutorial into each script.
4. Make them executable:
   ```bash
   chmod +x shelldemo1 shelldemo2 shelldemo3
   ```
5. Execute them:
   ```bash
   ./shelldemo1
   ./shelldemo2
   ./shelldemo3
   ```

## Part B — Arguments and loops

1. Create a small text file:
   ```bash
   echo "hello" > a.txt
   echo "world" > b.txt
   ```
2. Run `shelldemo12` on the files:
   ```bash
   ./shelldemo12 a.txt b.txt
   ```

## Part C — Menu programs

Run `shelldemo8` and test each option (`1`, `2`, `3`, and invalid input).

---

# 17. Reference solutions / expected outputs

Your outputs may differ depending on:

- current date/time
- hostname
- terminal settings for `clear` and beep (`\07`)

## Demo 2 output variability

Example:

```text
The date is Tue May 05 10:15:00 UTC 2026
My current working directory is /home/student/lab2_shell
```

## Demo 7/8 variability

- `date` prints the current date/time
- `hostname` prints the machine name
- `cal` prints the calendar for the current month

---

## Checklist before you finish

- [ ] I can create and run scripts (`chmod +x`, `./script`)
- [ ] I can define variables correctly (no spaces around `=`)
- [ ] I can read keyboard input (`read`)
- [ ] I can use command-line arguments (`$1`, `$2`, `$#`, `"$@"`)
- [ ] I can write `if/else`, `elif`, and `case`
- [ ] I can write `for` loops and `while` loops
- [ ] I understand basic quoting and safe scripting rules

````markdown
# Lab 5 — `grep` and `make` (Makefile) Tutorial

**Course/Lab Context:** Operating Systems Lab  \
**Topic:** Text searching with `grep` and build automation with `make`  \
**Goal:** Learn the most useful `grep` options and how to compile a small C project using a Makefile.

---

## 0) Prerequisites (Beginner Checklist)

Before starting, make sure you have:

- A Linux environment (Ubuntu, WSL, VM, or Linux machine)
- Basic terminal usage: `cd`, `ls`, `pwd`
- Installed tools:
  - `grep` (usually preinstalled)
  - `gcc` and `make`

Check versions:

```bash
grep --version | head -n 1
make --version | head -n 1
gcc --version | head -n 1
```

**Expected output (example):**

```text
grep (GNU grep) ...
GNU Make ...
gcc (Ubuntu ...) ...
```

> Version numbers may differ, that’s totally fine.

---

# Part 1 — `grep` Command

## 1.1 What is `grep`?

`grep` stands for **G**lobally search for a **R**egular **E**xpression and **P**rint.

In simple terms:

- `grep` searches for a **pattern** (text or regex) in a file
- It prints the **lines that match**

### General syntax

```bash
grep [options] pattern filename
```

- **pattern**: what you want to find (e.g., `David`, `^A`, `5$`)
- **filename**: file you want to search (e.g., `datafile`)
- **options**: how you want the search to behave (`-i`, `-n`, `-v`, etc.)

---

## 1.2 Create a sample data file

### Create `datafile` using `vi`

```bash
vi datafile
```

Insert these lines:

```text
Abel Adams 80 90
Brian Brown 50 40
Cath Cookson 30 55
Dave Davidson 40 60
David Smith 60 40
Eric Erikson 60 80
```

### Alternative (easier) method: create using `cat`

```bash
cat > datafile << 'EOF'
Abel Adams 80 90
Brian Brown 50 40
Cath Cookson 30 55
Dave Davidson 40 60
David Smith 60 40
Eric Erikson 60 80
EOF
```

Verify contents:

```bash
cat datafile
```

**Expected output:**

```text
Abel Adams 80 90
Brian Brown 50 40
Cath Cookson 30 55
Dave Davidson 40 60
David Smith 60 40
Eric Erikson 60 80
```

---

## 1.3 Example 1 — Basic search

Search for the word **Davidson** in `datafile`.

```bash
grep Davidson datafile
```

**Expected output:**

```text
Dave Davidson 40 60
```

**Explanation:** `grep` prints the line(s) containing the word `Davidson`.

---

## 1.4 Example 2 — Beginning of line (`^`)

`^` means the pattern must appear at the **start** of the line.

Find lines starting with **A**:

```bash
grep '^A' datafile
```

**Expected output:**

```text
Abel Adams 80 90
```

---

## 1.5 Example 3 — End of line (`$`)

`$` means the pattern must appear at the **end** of the line.

Find lines ending with **5**:

```bash
grep '5$' datafile
```

**Expected output:**

```text
Cath Cookson 30 55
```

---

## 1.6 Example 4 — Case-insensitive search (`-i`)

`-i` ignores uppercase/lowercase differences.

Search for `brian` (matches `Brian`):

```bash
grep -i brian datafile
```

**Expected output:**

```text
Brian Brown 50 40
```

---

## 1.7 Example 5 — Show line numbers (`-n`)

`-n` prints the line number before the matching line.

```bash
grep -n Adams datafile
```

**Expected output:**

```text
1:Abel Adams 80 90
```

---

## 1.8 Example 6 — Invert match (`-v`)

`-v` prints lines that **do not** match the pattern.

Print all lines except the line containing `Eric`:

```bash
grep -v Eric datafile
```

**Expected output:**

```text
Abel Adams 80 90
Brian Brown 50 40
Cath Cookson 30 55
Dave Davidson 40 60
David Smith 60 40
```

---

## 1.9 Example 7 — Count matching lines (`-c`)

`-c` counts how many **lines** match the pattern.

`Dav` appears in:

- `Dave Davidson ...`
- `David Smith ...`

So count is **2**:

```bash
grep -c Dav datafile
```

**Expected output:**

```text
2
```

> Tip: `-c` counts matching **lines**, not the total number of times the word appears.

---

## 1.10 Example 8 — Multiple patterns (`-E` and `|`)

- `-E` enables **extended regular expressions**
- `|` means **OR**

Find lines containing **David OR Brian**:

```bash
grep -E 'David|Brian' datafile
```

**Expected output:**

```text
Brian Brown 50 40
Dave Davidson 40 60
David Smith 60 40
```

---

## 1.11 Example 9 — Recursive search (`-r`)

`-r` searches inside files in the current directory **and all subdirectories**.

Suppose `main` appears in `main.c` and `./backup/main_old.c`.

```bash
grep -r main .
```

**Expected output (example):**

```text
./main.c:int main(){
./backup/main_old.c:int main(){
```

> The exact file paths depend on what exists in your folders.

---

## 1.12 Example 10 — Show filenames only (`-l`)

`-l` prints only the filenames where the pattern is found.

Suppose `main` appears in `main.c` and `test.c`:

```bash
grep -l main *.c
```

**Expected output (example):**

```text
main.c
test.c
```

---

## 1.13 Example 11 — `grep` with pipe (`|`)

A **pipe** sends the output of one command into another command.

### 1.13.1 Show directories only

In `ls -l` output:

- lines starting with `d` are directories

```bash
ls -l | grep '^d'
```

**Sample output:**

```text
drwxr-xr-x 2 ubuntu ubuntu 4096 Apr 29 09:10 backup
```

### 1.13.2 Show regular files only

In `ls -l` output:

- lines starting with `-` are regular files

```bash
ls -l | grep '^-'
```

**Sample output:**

```text
-rw-r--r-- 1 ubuntu ubuntu  120 Apr 29 09:05 datafile
-rw-r--r-- 1 ubuntu ubuntu  180 Apr 29 09:06 main.c
```

---

## 1.14 Example 12 — Find empty lines (`^$`)

`^$` means an **empty line** (start immediately followed by end).

Sample `file.txt`:

```text
line one


line three
```

To see the blank line clearly, use `-n`:

```bash
grep -n '^$' file.txt
```

**Expected output:**

```text
2:
```

---

## 1.15 Example 13 — Remove empty lines (`-v '^$'`)

`-v` reverses the match, so it prints all **non-empty** lines:

```bash
grep -v '^$' file.txt
```

**Expected output:**

```text
line one
line three
```

---

## 1.16 Example 14 — Search only `.c` files (`--include`)

When searching recursively, `--include=*.c` limits grep to only C source files.

Suppose `main` appears in `main.c` only:

```bash
grep -r --include=*.c main .
```

**Expected output (example):**

```text
./main.c:int main(){
```

### Explanation of each part

- `grep` = search
- `-r` = search inside folders also
- `--include=*.c` = search only files ending with `.c`
- `main` = word/pattern to search
- `.` = current folder

---

## 1.17 Example 15 — Context lines (`-C 1`)

`-C 1` prints:

- 1 line before the match
- the matching line
- 1 line after the match

With line numbers (`-n`):

```bash
grep -n -C 1 David datafile
```

**Expected output:**

```text
3-Cath Cookson 30 55
4:Dave Davidson 40 60
5-David Smith 60 40
--
4-Dave Davidson 40 60
5:David Smith 60 40
6-Eric Erikson 60 80
```

**Explanation:** There are multiple matches, so grep prints multiple “blocks” separated by `--`.

---

## 1.18 Quick meaning of important `grep` symbols and options

| Symbol/Option | Meaning | Example |
|---|---|---|
| `^` | Beginning of line | `grep '^A' datafile` |
| `$` | End of line | `grep '5$' datafile` |
| `-i` | Ignore case | `grep -i brian datafile` |
| `-n` | Show line number | `grep -n Adams datafile` |
| `-v` | Invert match | `grep -v Eric datafile` |
| `-c` | Count matching lines | `grep -c Dav datafile` |
| `-E` | Extended regular expression | `grep -E 'David|Brian' datafile` |
| `-r` | Recursive search | `grep -r main .` |
| `-l` | Show filenames only | `grep -l main *.c` |
| `-C 1` | Show one context line before and after | `grep -n -C 1 David datafile` |

---

## 1.19 Extra useful related commands (still on-topic)

### Show only the matching part (`-o`)

```bash
echo "David Smith" | grep -o "David"
```

**Expected output:**

```text
David
```

### Highlight matches (`--color`)

```bash
grep --color=auto David datafile
```

**Expected output:**

It prints matching lines, with `David` highlighted in color (in most terminals).

---

## 1.20 Common mistakes & tips (grep)

### Common mistakes

- **Forgetting quotes** for patterns like `'^A'`, `'5$'`, `'^$'`.
  - Without quotes, your shell may interpret special characters.
- Confusing **regex meaning**:
  - `.` in regex means “any character”, not a dot.
- Using recursive search from the wrong folder:
  - `grep -r main .` searches from your current directory.

### Tips

- Use `-n` often during labs—it makes outputs easier to explain.
- For large folders, add `--include=*.c` to avoid searching everything.

---

# Part 2 — `make` Command (Makefile)

## 2.1 What is `make`?

`make` is a **build automation tool**.

It reads instructions from a file (usually named **Makefile**) and automatically:

- Checks which files changed
- Runs only the required commands (compile/link)
- Builds only what’s necessary (saves time)

In simple terms:

> A Makefile is a set of rules that tells Linux **how to build your program** from source files.

---

## 2.2 Example project

We have two C files:

### `main.c`

```c
#include<stdio.h>
void hello();
int main(){
    hello();
    return 0;
}
```

### `hello.c`

```c
#include<stdio.h>
void hello(){
    printf("Hello OS Lab\n");
}
```

---

## 2.3 What happens without `make`? (Manual build)

If you compile manually, you typically run:

```bash
gcc -c main.c
gcc -c hello.c
gcc main.o hello.o -o program
```

Run the executable:

```bash
./program
```

**Expected output:**

```text
Hello OS Lab
```

---

## 2.4 Simple Makefile

Create a file named `Makefile`:

```make
program: main.o hello.o
	gcc main.o hello.o -o program


main.o: main.c
	gcc -c main.c


hello.o: hello.c
	gcc -c hello.c
```

> **Important:** The indentation before `gcc ...` must be a **TAB**, not spaces.

---

## 2.5 Run `make`

In the folder containing the Makefile:

```bash
make
```

What you should see (example):

```text
gcc -c main.c
gcc -c hello.c
gcc main.o hello.o -o program
```

Now run your program:

```bash
./program
```

**Expected output:**

```text
Hello OS Lab
```

---

## 2.6 Clean rule

A common Makefile rule is `clean`, to delete generated files (`.o` and the final program).

Add this to your Makefile:

```make
clean:
	rm -f *.o program
```

Run it:

```bash
make clean
```

**Expected result:**

- Object files (`*.o`) removed
- Executable `program` removed

---

## 2.7 Variables in Makefile

Variables reduce repetition.

Common variables:

```make
CC=gcc
CFLAGS=-Wall
```

- `CC` = which compiler to use
- `CFLAGS` = compiler flags
  - `-Wall` enables many helpful warnings (recommended)

---

## 2.8 Automatic variables

Make provides special variables that change depending on the rule:

| Variable | Meaning |
|---|---|
| `$@` | target |
| `$<` | first dependency |
| `$^` | all dependencies |

---

## 2.9 Pattern rule

Instead of writing separate rules for each `.o`, you can use a pattern rule:

```make
%.o: %.c
	gcc -c $<
```

**Explanation:**

- `%.o` matches any object file like `main.o`, `hello.o`
- `%.c` matches the corresponding source file `main.c`, `hello.c`
- `$<` is the first dependency (the `.c` file)

---

## 2.10 Full Makefile (recommended)

This is a complete and clean Makefile:

```make
CC=gcc
CFLAGS=-Wall
OBJ=main.o hello.o


program: $(OBJ)
	$(CC) $(OBJ) -o program


%.o: %.c
	$(CC) $(CFLAGS) -c $<


run: program
	./program


clean:
	rm -f *.o program
```

### How to use

Build:

```bash
make
```

Run using a Makefile rule:

```bash
make run
```

**Expected output:**

```text
Hello OS Lab
```

Clean:

```bash
make clean
```

---

## 2.11 Common mistakes & tips (make)

### Common mistakes

- **Using spaces instead of TAB** before commands:
  - Error example: `Makefile:*** missing separator. Stop.`
- Forgetting to name the file `Makefile`:
  - If you name it differently, you must run: `make -f filename`
- Not having `gcc`/`make` installed:
  - Install on Ubuntu: `sudo apt update && sudo apt install build-essential`

### Tips

- See what `make` would run (dry run):

```bash
make -n
```

- Force rebuild even if nothing changed:

```bash
make -B
```

---

## Lab Questions (Practice)

Try these after finishing the tutorial:

1. Use `grep` to find all lines that start with `D` in `datafile`.
2. Use `grep -c` to count how many lines contain `60`.
3. Modify `hello.c` to print `Hello from Makefile!` and run `make` again.
   - Notice that `make` recompiles only what changed.

---

### Summary

You learned:

- How to search files using `grep` (basic, regex, recursive, context)
- How `make` builds only changed files using Makefile rules
- How to write a clean Makefile using variables and pattern rules

````

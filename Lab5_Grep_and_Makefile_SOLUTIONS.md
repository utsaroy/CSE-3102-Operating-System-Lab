````markdown
# Lab 5 — Solutions (grep + make)

**Lab:** 5  \
**Topic:** `grep` and `make`

> Notes:
> - Outputs may vary depending on your directory contents.
> - For tasks involving `ls -l`, formatting can differ across systems.
> - If a task is not perfectly solvable using only `grep` for *all possible inputs*, the limitation is explained.

---

## Part A — Advanced `grep` Solutions

### Task A1 — Student filter using only `grep`

#### A1.1 First name starts with `Da`

```bash
grep '^Da' datafile
```

**Expected output**
```text
Dave Davidson 40 60
David Smith 60 40
```

**Explanation:** `^` anchors the match to the beginning of the line.

---

#### A1.2 Last name ends with `son`

Simple (works for this file format):

```bash
grep 'son ' datafile
```

**Expected output**
```text
Cath Cookson 30 55
Dave Davidson 40 60
Eric Erikson 60 80
```

Stricter regex (second word ends with `son`):

```bash
grep -E '^[A-Za-z]+ [A-Za-z]*son ' datafile
```

**Explanation:** In `datafile`, last name is followed by a space then marks.

---

#### A1.3 Second mark is exactly 80

```bash
grep ' 80$' datafile
```

**Expected output**
```text
Eric Erikson 60 80
```

**Explanation:** `$` anchors the match at end-of-line.

---

#### A1.4 Either mark is < 50 (grep-only attempt)

**Reality check:** Numeric comparisons are not what grep is designed for. This solution assumes marks are **0–99** and space-separated.

A workable (but complex) grep-only approach:

```bash
grep -E ' ([0-9]|[0-4][0-9]) [0-9]{2}$| [0-9]{2} ([0-9]|[0-4][0-9])$| ([0-9]|[0-4][0-9]) ([0-9]|[0-4][0-9])$' datafile
```

**Expected output (for provided datafile)**
```text
Brian Brown 50 40
Cath Cookson 30 55
Dave Davidson 40 60
David Smith 60 40
```

**Best/practical solution (recommended in real work) using awk:**

```bash
awk '$3 < 50 || $4 < 50' datafile
```

---

### Task A2 — Exact match vs substring

#### A2.1 Show why `Dav` is not exact

```bash
grep Dav datafile
```

**Output**
```text
Dave Davidson 40 60
David Smith 60 40
```

**Explanation:** `Dav` occurs as a substring in both `Dave` and `David`.

---

#### A2.2 Match `David` but not `Dave` or `Davidson`

```bash
grep -w David datafile
```

**Expected output**
```text
David Smith 60 40
```

**Explanation:** `-w` matches whole words only.

Alternative:

```bash
grep -E '\bDavid\b' datafile
```

---

### Task A3 — Context extraction (`-n -C`)

```bash
grep -n -C 2 David datafile
```

**Expected output (based on given datafile)**
```text
2-Brian Brown 50 40
3-Cath Cookson 30 55
4:Dave Davidson 40 60
5:David Smith 60 40
6-Eric Erikson 60 80
```

**Explanation:** When matches are close, grep merges overlapping context into one block.

---

### Task A4 — Recursive + include + filenames-only + exclude-dir

#### A4.1 Search `int main()` only in `.c`

```bash
grep -r --include='*.c' 'int main()' .
```

**Example output**
```text
./src/main.c:int main(){
```

---

#### A4.2 Filenames only

```bash
grep -rl --include='*.c' 'int main()' .
```

**Example output**
```text
./src/main.c
```

---

#### A4.3 Exclude backup directory

```bash
grep -r --exclude-dir='backup' main .
```

**Explanation:** `--exclude-dir` prevents searching inside that folder.

---

### Task A5 — Empty vs whitespace-only

#### A5.1 Truly empty lines

```bash
grep -n '^$' file.txt
```

**Explanation:** matches lines with length 0.

---

#### A5.2 Empty OR only spaces/tabs

```bash
grep -n -E '^[[:space:]]*$' file.txt
```

**Explanation:** `[[:space:]]*` matches any number of whitespace characters.

---

#### A5.3 Show non-empty lines with numbers

Truly non-empty (does not remove space-only):

```bash
grep -n -v '^$' file.txt
```

Non-blank (removes whitespace-only too):

```bash
grep -n -v -E '^[[:space:]]*$' file.txt
```

---

### Task A6 — Pipes with `ls -l`

#### A6.1 Directories with permissions starting `drwx`

```bash
ls -l | grep '^drwx'
```

---

#### A6.2 Regular files then only `.c`

```bash
ls -l | grep '^-' | grep '\.c$'
```

---

#### A6.3 Files larger than 1KB (approx via digit count)

```bash
ls -l | grep '^-' | grep -E ' [0-9]{4,} '
```

**Explanation:** A 4+ digit size is 1000 bytes or more. This is not perfect on all systems but acceptable for many lab environments.

---

## Part B — `make` / Makefile Solutions

### Task B1 — Prove rebuilding behavior

1) Build:

```bash
make
```

2) Run again (no changes):

```bash
make
```

**Expected:**
```text
make: 'program' is up to date.
```

3) Change `hello.c`, then:

```bash
make
```

**Expected behavior:** only `hello.o` recompiles, then linking runs.

**Explanation:** make uses file timestamps to rebuild only what changed.

---

### Task B2 — Header dependency

Create **hello.h**:

```c
void hello();
```

Update C files to include it:

- `#include "hello.h"`

Update Makefile dependencies:

```make
main.o: main.c hello.h
	gcc -c main.c

hello.o: hello.c hello.h
	gcc -c hello.c
```

**Proof:** Modify `hello.h` then run `make`. Both objects should rebuild.

---

### Task B3 — Debug/release build modes

```make
CC=gcc
OBJ=main.o hello.o
TARGET=program

.PHONY: debug release clean

debug: CFLAGS=-Wall -g -O0
debug: $(TARGET)

release: CFLAGS=-Wall -O2
release: $(TARGET)

$(TARGET): $(OBJ)
	$(CC) $(OBJ) -o $(TARGET)

%.o: %.c
	$(CC) $(CFLAGS) -c $<

clean:
	rm -f *.o $(TARGET)
```

**Explanation:** debug/release set different `CFLAGS` but reuse the same rules.

---

### Task B4 — `.PHONY` + safe clean

Add:

```make
.PHONY: clean run debug release check
```

Safe clean:

```make
clean:
	rm -f *.o program
```

---

### Task B5 — Run rules with arguments

Update API to accept name:

**hello.h**
```c
void hello(const char *name);
```

**hello.c**
```c
#include <stdio.h>
#include "hello.h"

void hello(const char *name){
    if(name) printf("Hello %s from Makefile!\n", name);
    else printf("Hello from Makefile!\n");
}
```

**main.c**
```c
#include <stdio.h>
#include "hello.h"

int main(int argc, char *argv[]){
    const char *name = (argc > 1) ? argv[1] : NULL;
    hello(name);
    return 0;
}
```

Makefile targets:

```make
run: program
	./program

runname: program
	./program Utsaroy
```

---

### Task B6 — Warnings challenge

Example of intentionally bad code:

```c
int unused = 5;
printf(name);
```

Build with warnings:

```bash
make CFLAGS='-Wall'
```

Then fix by:

- removing unused variables
- using safe printf format strings

Example fix:

```c
printf("Hello %s\n", name);
```

---

## Part C — Combined Solutions

### Task C1 — Analyze build logs

Create log:

```bash
make clean
make > build.log 2>&1
```

1) Count gcc lines:

```bash
grep -c gcc build.log
```

2) Show warnings:

```bash
grep 'warning:' build.log
```

3) Context around warnings:

```bash
grep -C 1 'warning:' build.log
```

4) Filenames containing warnings in all `.log` files:

```bash
grep -l 'warning:' *.log
```

---

### Task C2 — Fail build if TODO exists

Add target:

```make
check:
	@grep -r --include='*.c' -n 'TODO' . && (echo 'ERROR: TODO found. Remove TODOs.'; exit 1) || (echo 'OK: No TODO found.')
```

Make build depend on it:

```make
program: check $(OBJ)
	$(CC) $(OBJ) -o program
```

**Explanation:**
- If `grep` finds TODO, it exits 0, triggers the error branch, and make fails.
- If it finds nothing, grep exits 1, and the `OK` branch runs.

---

**End of solutions.**
````

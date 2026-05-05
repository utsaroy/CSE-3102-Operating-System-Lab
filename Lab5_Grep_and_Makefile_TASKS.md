````markdown
# Lab 5 — Difficult Tasks (grep + make)

**Course:** CSE-3102 Operating Systems Lab  \
**Lab:** 5  \
**Topic:** `grep` and `make` (Makefile)

> Instructions: Answer using terminal commands and outputs. Unless a task explicitly allows it, try to solve using only the requested tool (`grep` or `make`).

---

## Part A — Advanced `grep` Tasks

### Task A1 — Build a “student filter” using only `grep`
Using the provided `datafile`, write **exact `grep` commands** (one command per subtask) to do the following:

1. Print only the lines where the **first name starts with `Da`**.
2. Print only the lines where the **last name ends with `son`**.
3. Print the lines where the **second mark** (the last number) is exactly **80**.
4. Print lines where **either mark** is **less than 50**.

**Deliverable:** commands + output.

---

### Task A2 — Exact word match vs substring match

1. Show why `grep Dav datafile` is not an “exact name search”.
2. Write a command that matches **David** but does **not** match **Dave** and does **not** match **Davidson**.

**Restriction:** Use `grep` regex only.

---

### Task A3 — Context extraction challenge (`-n -C`)
Search for `David` such that:

- show **2 lines before and 2 lines after** each match,
- line numbers must appear.

**Deliverable:** command + output.

---

### Task A4 — Recursive + include + file-name-only combo

Create this directory structure:

```text
lab5/
  src/
    main.c
    util.c
  backup/
    main_old.c
  notes.txt
```

Put the word `main` in multiple files, but ensure:

- `notes.txt` also contains `main`
- `backup/main_old.c` contains `main`
- only `src/main.c` contains `int main()`

Now write commands to:

1. Recursively search for `int main()` but **only inside `.c` files**.
2. Print **only filenames** where `int main()` exists.
3. Search for `main` recursively but **exclude** the `backup/` directory.

---

### Task A5 — Empty lines vs whitespace-only lines

Create a file `file.txt` with:

- multiple blank lines,
- some lines containing only spaces,
- and some normal text lines.

Write `grep` commands that:

1. Print **truly empty lines only** (no spaces).
2. Print lines that are **empty OR contain only spaces/tabs**.
3. Print all **non-empty** lines with line numbers.

---

### Task A6 — Pipe mastery (realistic filtering)

In a directory with at least:

- 2 directories
- 3 files
- at least one `.c` file

Write pipeline commands (`|`) to:

1. Show only directories whose permissions begin with `drwx`.
2. Show only regular files, then filter only `.c` files.
3. Show only files larger than 1KB using `ls -l | grep ...`.

---

## Part B — Difficult `make` / Makefile Tasks

### Task B1 — Prove correct rebuilding behavior

Using the `main.c` and `hello.c` project:

1. Build using your Makefile.
2. Run `make` again and confirm that **nothing rebuilds**.
3. Modify only `hello.c`, then run `make` and confirm that:
   - only `hello.o` recompiles
   - linking runs again
   - `main.o` does not recompile

**Deliverable:** terminal output evidence.

---

### Task B2 — Header dependency (essential)

Create `hello.h` and update `main.c` and `hello.c` to include it.

Modify the Makefile so that if `hello.h` changes, the correct `.o` files rebuild.

**Deliverable:** updated Makefile + proof that changing `hello.h` triggers rebuild.

---

### Task B3 — Debug/release build modes

Extend your Makefile to support:

- `make debug` builds with: `-g -O0 -Wall`
- `make release` builds with: `-O2 -Wall`
- both must create the same executable name: `program`

**Restriction:** Don’t duplicate full compile commands—use variables and reuse rules.

---

### Task B4 — Makefile quality: `.PHONY` + safe clean

1. Add `.PHONY` for targets that are not real files: `clean`, `run`, `debug`, `release`, `check`.
2. Make `clean` safe:
   - It must not error if files don’t exist.
   - It must not delete unrelated files.

---

### Task B5 — `run` rules with arguments

Modify code so that `hello()` prints the first command-line argument if provided.

Add Makefile rules:

- `make run` runs: `./program`
- `make runname` runs: `./program Utsaroy`

---

### Task B6 — Warnings challenge (`-Wall`)

Intentionally add code that causes **at least 3 warnings**, then:

1. Show the warnings produced by `make`.
2. Fix the code so the build is warning-free.

---

## Part C — Combined Challenge (grep + make)

### Task C1 — Build log analyzer using grep

Run:

```bash
make clean
make > build.log 2>&1
```

Now use `grep` to:

1. Count how many times `gcc` appears in the log.
2. Show only lines that contain warnings (`warning:`).
3. Show 1 line of context around each warning.
4. Print only filenames that contain warnings by searching inside `*.log`.

---

### Task C2 — Enforced rule: build must fail if TODO exists

Add a Makefile target `check` that fails if any `.c` file contains the word `TODO`.

- If TODO exists: print an error message and stop.
- If TODO does not exist: print “OK” and continue.

Then make your main build depend on `check`.

---

**End of tasks.**
````

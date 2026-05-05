# Vim Lab Manual (Lab 4)

*A worked-example tutorial for a single lab session (90–120 minutes)*  
*Audience: absolute beginners using a command-line text editor for the first time*

Prepared for Overleaf / LaTeX compilation (source) → converted to Markdown for the lab repository.  
Date: 2026-05-05

---

## Table of Contents

1. [Why Vim still matters in a systems lab](#1-why-vim-still-matters-in-a-systems-lab)
2. [One-session roadmap](#2-one-session-roadmap)
3. [The evolution of text editors](#3-the-evolution-of-text-editors)
   - 3.1 [From line editors to screen editors](#31-from-line-editors-to-screen-editors)
   - 3.2 [Why `vi` was important](#32-why-vi-was-important)
   - 3.3 [From `vi` to Vim](#33-from-vi-to-vim)
   - 3.4 [A compact timeline](#34-a-compact-timeline)
4. [Traditional `vi` versus modern Vim](#4-traditional-vi-versus-modern-vim)
   - 4.1 [Features typically associated with Vim and not classic `vi`](#41-features-typically-associated-with-vim-and-not-classic-vi)
5. [Your first contact with Vim](#5-your-first-contact-with-vim)
   - 5.1 [Start Vim](#51-start-vim)
   - 5.2 [Understand the modes first](#52-understand-the-modes-first)
   - 5.3 [The absolute minimum commands](#53-the-absolute-minimum-commands)
6. [Worked example 1: create, edit, save, and quit](#6-worked-example-1-create-edit-save-and-quit)
7. [Worked example 2: move around without fear](#7-worked-example-2-move-around-without-fear)
8. [Operators and motions: the grammar of Vim](#8-operators-and-motions-the-grammar-of-vim)
9. [Worked example 3: fix a badly written paragraph](#9-worked-example-3-fix-a-badly-written-paragraph)
10. [Searching and replacing](#10-searching-and-replacing)
11. [Visual mode and block editing](#11-visual-mode-and-block-editing)
12. [Registers: useful to learn on day one](#12-registers-useful-to-learn-on-day-one)
13. [Marks and macros: a short first exposure](#13-marks-and-macros-a-short-first-exposure)
14. [Buffers, windows, and split screens](#14-buffers-windows-and-split-screens)
15. [A compact note on buffers and tabs](#15-a-compact-note-on-buffers-and-tabs)
16. [A small `.vimrc` for beginners](#16-a-small-vimrc-for-beginners)
17. [Built-in help: becoming more independent in Vim](#17-built-in-help-becoming-more-independent-in-vim)
18. [Common beginner mistakes](#18-common-beginner-mistakes)
19. [Suggested practice sequence for a single session](#19-suggested-practice-sequence-for-a-single-session)
20. [A compact cheat sheet](#20-a-compact-cheat-sheet)
21. [Conclusion](#21-conclusion)

---

## 1) Why Vim still matters in a systems lab

In an operating systems, systems programming, or Linux laboratory, you regularly edit files such as:

- Shell scripts (`.sh`)
- C source files (`.c`, `.h`)
- Configuration files
- Makefiles
- Test inputs
- Short notes

Very often, you do this **directly in a terminal**, sometimes over **SSH**. In those environments, an editor that:

- starts quickly,
- works without a desktop GUI,
- exists on most Unix-like systems,

is extremely valuable.

Vim still matters because it is:

- **terminal-native** (works anywhere you have a shell)
- **fast** to start and run
- **widely available**
- designed for efficient keyboard editing

### Beginner mindset

Vim can feel uncomfortable at first because it is **modal**. The good news is that the same design that feels strange on day one is exactly why experienced users can edit quickly.

**A good first-day goal is modest:**

- open a file
- type text
- save and quit
- move around confidently
- do simple edits
- search
- copy/paste using registers
- use split windows

That is already enough for real lab work.

---

## 2) One-session roadmap

This tutorial is designed for a single lab period of roughly **90 to 120 minutes**.

| Time block | Focus |
|---|---|
| First 10 minutes | What Vim is, why modes exist, and how to quit safely |
| Next 20 minutes | Open a file, insert text, save, quit, reopen, move around |
| Next 20 minutes | Practice operators and motions using a small text file |
| Next 15 minutes | Search & replace, visual mode, and block editing |
| Next 15 minutes | Registers, copying, pasting, black-hole register |
| Next 10 minutes | Split windows, buffers, a two-file workflow |
| Last 10 minutes | Marks, macros, help, and self-study |

### Commands to become comfortable with early

Focus first on:

`i`, `Esc`, `:w`, `:q`, `h j k l`, `w`, `b`, `0`, `$`, `dd`, `yy`, `p`, `u`, `/`, `:s`, `:split`

---

## 3) The evolution of text editors

Understanding *why* Vim works the way it does makes it less intimidating.

### 3.1 From line editors to screen editors

Early Unix systems were built around slow terminals and constrained hardware. In that environment:

- **Line editors** such as `ed` were natural.
- Users edited **one line at a time** using terse commands.

As screen terminals became more common:

- the editor **`ex`** extended the command-driven model,
- and **`vi`** became the visual, screen-oriented interface associated with `ex`.

**Key shift:** users could see a full screen of text and move around interactively. Editing became faster and more intuitive, but kept command precision.

### 3.2 Why `vi` was important

`vi` was influential because it turned editing into a compact language.

- In **Normal mode**, keys perform actions.
- In **Insert mode**, keys produce text.

This separation allowed the same keyboard keys to serve different purposes without constant modifier chords.

Examples of `vi`-style editing language:

- `dw` → delete word
- `d$` → delete to end of line
- `ci"` → change inside quotation marks

### 3.3 From `vi` to Vim

Vim stands for **Vi IMproved**.

It began as a better `vi` and gradually became a richer environment:

- multi-level undo
- split windows
- extensive help
- scripting
- syntax highlighting
- sophisticated registers
- visual editing

**Essential point:** Vim keeps the classic `vi` editing model but makes it more powerful for modern work.

### 3.4 A compact timeline

| Era | Development |
|---|---|
| Early Unix | `ed` established line-oriented editing |
| 1970s | `ex` expanded command-driven editing |
| Late 1970s | `vi` popularized full-screen visual editing |
| 1991 onward | Bram Moolenaar released Vim and extended the model |
| Modern era | Vim remains common on Linux/remote systems; descendants continue modal editing |

---

## 4) Traditional `vi` versus modern Vim

Many students say “vi” when they really mean Vim. On Linux, typing `vi` often launches Vim or a Vim-compatible implementation.

### Important lab habit: verify features

In real lab environments, never assume the binary called `vi` supports every Vim feature.

Inside the editor:

```vim
:version
```

### 4.1 Features typically associated with Vim and not classic `vi`

| Feature | Why it matters in practice |
|---|---|
| Multi-level undo/redo | You can back out of many mistakes, not just the last one |
| Split windows/vertical splits | Two files or views can stay visible at the same time |
| Better built-in help | `:help` is a full reference manual |
| Visual/block mode | Selection-based editing becomes much easier |
| Richer registers | You can control where deleted/copied text goes |
| Syntax highlighting + filetype | Code becomes easier to read |
| Tabs, buffers, sessions, plugins | Larger workflows become manageable |

---

## 5) Your first contact with Vim

### 5.1 Start Vim

For this lab, begin with:

```bash
vim
```

### Expected output

Vim opens in your terminal. You will start in **Normal mode**.

### 5.2 Understand the modes first

| Mode | Purpose | How to enter |
|---|---|---|
| Normal mode | Navigation and commands | `Esc` |
| Insert mode | Type text | `i`, `a`, `o`, `O` |
| Visual mode | Select text | `v`, `V`, `Ctrl-v` |
| Command-line mode | Run Ex commands | `:` |
| Replace mode | Overwrite characters | `R` |

**Survival tip:** when confused, press `Esc`.

### 5.3 The absolute minimum commands

| Command | Meaning |
|---|---|
| `i` | start inserting text before the cursor |
| `Esc` | return to Normal mode |
| `:w` | save the file |
| `:q` | quit |
| `:wq` | save and quit |
| `:q!` | quit without saving |
| `u` | undo |
| `p` | paste after the cursor |

---

## 6) Worked example 1: create, edit, save, and quit

Do this exercise once before any theory-heavy discussion.

### Step 1: open a new file

At the shell:

```bash
vim lab_notes.txt
```

If the file does not exist, Vim opens an empty buffer. You are in **Normal mode**.

### Step 2: enter Insert mode and type text

Press:

```text
i
```

Type exactly these three lines:

```text
Operating Systems Lab
Today I am learning Vim.
This editor feels unusual at first.
```

### Step 3: return to Normal mode

Press:

```text
Esc
```

Now letters you type will be interpreted as commands.

### Step 4: save and quit

Option A:

```vim
:w
:q
```

Option B:

```vim
:wq
```

### Expected output

You return to your shell prompt. Verify:

```bash
cat lab_notes.txt
```

Expected:

```text
Operating Systems Lab
Today I am learning Vim.
This editor feels unusual at first.
```

### What you learned

Vim is not edited in the same mode in which commands are issued. You enter Insert mode to type text, then leave it to save, quit, move, copy, delete, and search.

---

## 7) Worked example 2: move around without fear

Movement is the foundation of Vim. Efficient editing comes from combining an operator with a motion.

### 7.1 Prepare a practice file

Start Vim:

```bash
vim
```

Press `i` and type:

```text
Vim is a modal editor.
Students often fear the first few minutes.
Practice makes the motions feel natural.
Remote Linux work becomes easier with skill.
```

Press `Esc`.

### 7.2 Basic motions to practice

In Normal mode, try:

| Keys | Effect |
|---|---|
| `h` | move left |
| `j` | move down |
| `k` | move up |
| `l` | move right |
| `w` | move to next word |
| `b` | move to previous word |
| `0` | move to beginning of line |
| `$` | move to end of line |
| `gg` | go to first line |
| `G` | go to last line |

### Expected output

Nothing changes in the text; only the cursor position changes.

### 7.3 A precise movement drill

Do the following in order:

1. `gg` to go to the first line.
2. `j` twice to reach the third line.
3. `0` to move to the start of that line.
4. `w` three times.
5. `$` to jump to the end of the line.
6. `b` twice to move backward by words.

**Tip:** On day one you may use `h j k l` a lot. Real speed appears when you use word/line/search motions.

---

## 8) Operators and motions: the grammar of Vim

Main idea:

> **operator + motion = action over a region of text**

### Typical operators

| Operator | Meaning |
|---|---|
| `d` | delete |
| `c` | change |
| `y` | yank (copy) |
| `>` | indent right |
| `<` | indent left |

### Typical motions

Examples: `w`, `b`, `0`, `$`, `gg`, `G` and text objects like `iw`.

### 8.1 Examples to memorize early

| Command | Meaning |
|---|---|
| `dw` | delete from cursor to next word boundary |
| `cw` | change the current word |
| `d$` | delete from cursor to end of line |
| `yy` | yank the current line |
| `dd` | delete the current line |
| `ciw` | change inner word |

---

## 9) Worked example 3: fix a badly written paragraph

Create the file from the shell:

```bash
cat > edit_practice.txt <<'END'
Vim is hard for every beginner.
But the hard part is mostly the first contact.
When students practice calmly, the editor becomes practical.
END
```

Open it:

```bash
vim edit_practice.txt
```

### 9.1 Task A: change one word with `ciw`

Move the cursor onto the word `hard` in line 1 and type:

```text
ciwuseful
```

Press `Esc`.

#### Expected output

Line 1 becomes:

```text
Vim is useful for every beginner.
```

### 9.2 Task B: delete to end of line with `d$`

Move to the word `mostly` in line 2 and type:

```text
d$
```

Now append a better ending:

```text
aabout learning the modes.
```

Press `Esc`.

#### Expected output

Line 2 becomes:

```text
But the hard part is about learning the modes.
```

### 9.3 Task C: duplicate a line

Move to line 3 and type:

```text
yyp
```

#### Expected output

Line 3 is copied and pasted below itself.

### 9.4 Task D: undo and redo

Undo:

```text
u
```

Redo:

```text
Ctrl-r
```

**Tip:** Vim’s undo is there so you can experiment without fear.

---

## 10) Searching and replacing

### 10.1 Search

In Normal mode:

| Command | Meaning |
|---|---|
| `/word` | search forward for `word` |
| `?word` | search backward for `word` |
| `n` | next match |
| `N` | previous match |

**Related useful command:** clear search highlight

```vim
:noh
```

### 10.2 Worked example 4: replace text across a file

Create a file:

```bash
cat > replace_practice.txt <<'END'
vi is old.
vi inspired vim.
vim keeps the vi editing idea.
END
```

Open it and run:

```vim
:%s/vi/Vim/g
```

#### Explanation

- `%` means the whole file
- `s` means substitute
- `/vi/Vim/` means replace `vi` by `Vim`
- `g` means do it for **all** matches on each line

#### Expected output

File becomes:

```text
Vim is old.
Vim inspired vim.
vim keeps the Vim editing idea.
```

Now try the safer confirming form:

```vim
:%s/Vim/VIM/gc
```

Expected behavior: Vim asks you to confirm each replacement.

---

## 11) Visual mode and block editing

Visual mode is often easier for beginners because the selected text is visible.

| Command | Meaning |
|---|---|
| `v` | character-wise visual mode |
| `V` | line-wise visual mode |
| `Ctrl-v` | block-wise visual mode |

### 11.1 Worked example 5: add comment markers to several lines

Create this file:

```bash
cat > block_practice.c <<'END'
int a = 10;
int b = 20;
int c = 30;
END
```

Open it and do the following:

1. Move the cursor to the first character of line 1.
2. Press `Ctrl-v`.
3. Press `j` twice to select the first column of all three lines.
4. Press `I// `.
5. Press `Esc`.

#### Expected output

```c
// int a = 10;
// int b = 20;
// int c = 30;
```

**Tip:** The insertion appears on all selected lines after you press `Esc`.

---

## 12) Registers: useful to learn on day one

### 12.1 What is a register?

A register is a named storage location where Vim can keep yanked, deleted, or recorded text.

General syntax uses the double quote character followed by a register name.

- `"ayy` yanks the current line into register `a`
- `"ap` pastes from register `a`

### 12.2 Important register categories

| Register | Purpose |
|---|---|
| `"` (unnamed) | default storage for most deletes and yanks |
| `0`–`9` | keep recent yanks and deletions |
| `a`–`z` | user-controlled named registers |
| `_` (black-hole) | throw text away without overwriting useful yanked text |
| `+` and `*` | system clipboard registers (when supported) |

### 12.3 Worked example 6: keep two pieces of text at once

Create this file:

```bash
cat > register_practice.txt <<'END'
alpha line
beta line
gamma line
delta line
END
```

Open it and do the following:

1. Move to line 1 and type:

   ```text
   "ayy
   ```

   This copies `alpha line` into register `a`.

2. Move to line 3 and type:

   ```text
   "byy
   ```

   This copies `gamma line` into register `b`.

3. Move to the last line and type:

   ```text
   "ap
   ```

4. Type:

   ```text
   "bp
   ```

#### Expected output

You will have pasted two different lines, each coming from the register you chose.

### 12.4 Worked example 7: why the black-hole register matters

Problem: you copy something, then you delete something else, and the delete overwrites what you copied.

Try:

1. Yank line 1: `yy`
2. Move to line 2 and delete: `dd`
3. Paste: `p`

You may paste the deleted line, not what you yanked.

Now repeat carefully:

1. Yank line 1: `yy`
2. Move to line 2 and delete into black-hole register:

   ```text
   "_dd
   ```

3. Paste: `p`

#### Expected output

Your yanked text is still available because the deletion went to the black-hole register.

---

## 13) Marks and macros: a short first exposure

### 13.1 Marks

Marks let you remember positions in a file.

| Command | Meaning |
|---|---|
| `ma` | set mark `a` at the current position |
| `'a` | jump to the **line** of mark `a` |
| `` `a `` | jump to the **exact cursor position** of mark `a` |

Practical use: set a mark before jumping far away to inspect another part of the file.

### 13.2 Macros

Macros record a sequence of edits and replay them.

| Command | Meaning |
|---|---|
| `qa` | start recording into register `a` |
| `q` | stop recording |
| `@a` | run macro in register `a` |
| `3@a` | run it three times |

### 13.3 Worked example 8: append semicolons with a macro

Create this file:

```bash
cat > macro_practice.txt <<'END'
int x = 10
int y = 20
int z = 30
END
```

Open it, move to the first line, and do this:

1. Start recording:

   ```text
   qa
   ```

2. Append semicolon at end of line:

   ```text
   A;
   ```

3. Press `Esc`
4. Move down:

   ```text
   j
   ```

5. Stop recording:

   ```text
   q
   ```

6. Apply the same edit to the next two lines:

   ```text
   2@a
   ```

#### Expected output

```c
int x = 10;
int y = 20;
int z = 30;
```

---

## 14) Buffers, windows, and split screens

### 14.1 Buffers versus windows

- A **buffer** is an open file in memory.
- A **window** is a view onto a buffer.

Multiple windows may show the same buffer or different buffers.

### 14.2 Split-screen commands to learn early

| Command | Meaning |
|---|---|
| `:split file` | open file in a horizontal split |
| `:vsplit file` | open file in a vertical split |
| `Ctrl-w w` | move to the next window |
| `Ctrl-w h` | move to left window |
| `Ctrl-w l` | move to right window |
| `Ctrl-w j` | move to lower window |
| `Ctrl-w k` | move to upper window |
| `:close` | close the current window |

### 14.3 Worked example 9: edit two files side by side

Create two files:

```bash
cat > main.c <<'END'
#include <stdio.h>
int main(void) {
printf("Hello\n");
return 0;
}
END

cat > notes.txt <<'END'
Tasks:
- change output message
- compile the file
END
```

Open the first one:

```bash
vim main.c
```

Inside Vim, type:

```vim
:vsplit notes.txt
```

Now practice:

1. `Ctrl-w l` to move to the right window.
2. Edit `notes.txt` and add a new bullet.
3. `Ctrl-w h` to return to `main.c`.
4. Change `Hello` to `Hello from Vim`.
5. Save both files with `:w` in each window.

#### Expected output

- `main.c` contains `printf("Hello from Vim\n");`
- `notes.txt` contains your added bullet

**Extra (optional, outside Vim):**

```bash
gcc main.c -o main
./main
```

Expected:

```text
Hello from Vim
```

---

## 15) A compact note on buffers and tabs

For a first session, this distinction is enough:

- A buffer is an open file.
- A window is one visible view of a buffer.
- A tab is a page-like collection of windows.

Useful commands:

```vim
:buffers
:bnext
:bprevious
:tabnew
:tabnext
```

---

## 16) A small `.vimrc` for beginners

A minimal beginner-friendly configuration can make the first experience less harsh.

Create/edit `~/.vimrc` and add:

```vim
set number
set relativenumber
set expandtab
set tabstop=4
set shiftwidth=4
set smartindent
set ignorecase
set smartcase
set incsearch
set hlsearch
set mouse=a
```

### Expected output

After reopening Vim:

- You see line numbers.
- Searches highlight matches.
- Indentation uses spaces.

### 16.1 What these options do

- `set number` shows line numbers.
- `set relativenumber` helps movement by counts.
- `expandtab`, `tabstop`, `shiftwidth` make indentation predictable.
- `ignorecase` and `smartcase` improve searching.
- `incsearch` and `hlsearch` make matches easier to see.

---

## 17) Built-in help: becoming more independent in Vim

Vim’s internal help system is one of its strongest features.

Try:

```vim
:help
:help i_CTRL-R
:help registers
:help visual-block
:help :split
:help usr_02.txt
```

### Tip

If you learn to look up help topics on your own, Vim stops feeling mysterious.

---

## 18) Common beginner mistakes

1. **Staying in Insert mode** and thinking Vim is broken.
   - Fix: press `Esc`.
2. **Trying to quit with the window manager** rather than `:q` or `:wq`.
   - Fix: use `:q`, `:wq`, or `:q!`.
3. **Using only arrows or only `h j k l`** and never learning word motions.
   - Fix: practice `w`, `b`, `0`, `$`, `/`.
4. **Forgetting deletes affect registers**.
   - Fix: use the black-hole register `"_dd` when needed.
5. **Confusing a window with a buffer**.
   - Fix: buffer=file-in-memory, window=view.
6. **Being afraid of undo**.
   - Fix: use `u` and redo with `Ctrl-r`.

---

## 19) Suggested practice sequence for a single session

1. Create a file, type three lines, save, quit, reopen.
2. Move by words and by line boundaries.
3. Use `ciw`, `dd`, `yy`, and `p`.
4. Search for a word and replace it globally with confirmation.
5. Add a prefix to multiple lines using visual block mode.
6. Copy one line into register `a`, another into register `b`, and paste both.
7. Use the black-hole register to delete without losing copied text.
8. Open a vertical split and edit two files side by side.
9. End by showing `:help registers` and `:help :vsplit`.

---

## 20) A compact cheat sheet

| Task | Useful commands |
|---|---|
| Enter text | `i`, `a`, `o`, `O` |
| Return to command mode | `Esc` |
| Save and quit | `:w`, `:q`, `:wq`, `:q!` |
| Move around | `h j k l`, `w`, `b`, `0`, `$`, `gg`, `G` |
| Delete and change | `dd`, `dw`, `d$`, `cw`, `ciw` |
| Copy and paste | `yy`, `p`, `P` |
| Undo and redo | `u`, `Ctrl-r` |
| Search | `/pattern`, `?pattern`, `n`, `N` |
| Replace | `:s/old/new/`, `:%s/old/new/gc` |
| Visual selection | `v`, `V`, `Ctrl-v` |
| Registers | `"ayy`, `"ap`, `"_dd` |
| Marks | `ma`, `'a`, `` `a `` |
| Macros | `qa`, `q`, `@a` |
| Split windows | `:split`, `:vsplit`, `Ctrl-w w` |
| Help | `:help topic` |

---

## 21) Conclusion

You now have enough Vim knowledge to do real systems-lab work:

- confidently use modes (`Esc` to recover)
- open/edit/save/quit files
- move efficiently
- combine operators and motions
- search and replace safely
- use visual block editing
- use registers (including black-hole)
- work with split windows

Keep practicing the sequence in Section 19 until it feels natural.

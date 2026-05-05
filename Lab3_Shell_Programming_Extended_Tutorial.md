````markdown
# Lab 03 — Shell Programming (Extended): Arrays, Functions, Strings, File Tests & Arithmetic (Beginner Tutorial)

The **shell** is the program that gives you a simple text-based interface to UNIX/Linux. You type commands, the shell runs them. Beyond that, the shell is also a **programming language**: you can write **shell scripts** (files of commands that run sequentially).

This lab tutorial includes:
- A **quick recap** of Lab 02 Part 1 topics (variables, input/output, branching, loops, etc.)
- The “extended” topics essential for OS labs:
  - Arrays (indexed + associative)
  - Functions (local variables, arguments, return values, recursion)
  - String manipulation
  - File and directory tests
  - Arithmetic + math (integer + floating point using `bc`)
  - A final “put it all together” script

> **NOTE (important lab habit):** For each demo, **type it into a file**, give execute permission, and run it:

```bash
chmod +x filename
./filename
```

---

## Before You Start: `sh` vs `bash` (Common Source of Confusion)

Some scripts below start with:
- `#!/bin/sh` (POSIX shell, may be `dash` on Ubuntu)
- `#!/bin/bash` (Bash shell with extra features)

**Arrays, `declare -A`, `(( ... ))`, `${var^^}` uppercase**, etc. require **bash**.
So for scripts using those features, always use:

```bash
#!/bin/bash
```

---

# Section A — Quick Recap of Previous Topics

If you’re confident with these, skip to **Section B**.

---

## Demo Program 1 — Variables, Comments, and `echo`

### What it teaches
- How to store values in variables
- How to print values using `echo`
- How `$name` expands the variable

### Script
```sh
#!/bin/sh
#Filename: shelldemo1
name=John
car="Ford Escort"
age=21
echo "My name is $name"
echo "I am $age years old and I drive a $car."
```

### Expected output
```
My name is John
I am 21 years old and I drive a Ford Escort.
```

### Common mistakes & tips
- **No spaces** around `=` in shell variables: `name=John` (correct), `name = John` (wrong).
- Use quotes for values with spaces: `car="Ford Escort"`.

---

## Demo Program 2 — Storing command output in variables

### What it teaches
- Save the output of a command like `date` or `pwd` into a variable

### Script
```sh
#!/bin/sh
#Filename: shelldemo2
todaysdate=`date`
myworkingdirectory=`pwd`
echo "The date is $todaysdate"
echo "My current working directory is $myworkingdirectory"
```

### Expected output (example)
```
The date is Sun Apr 12 14:30:58 BST 2026
My current working directory is /home/student/shelldemos
```

### Extra (recommended modern style)
Backticks work, but modern Bash style is clearer:

```sh
todaysdate=$(date)
myworkingdirectory=$(pwd)
```

### Common mistakes & tips
- Don’t put spaces inside backticks/`$()` accidentally.
- If command output contains spaces, **quote variables when printing** (as done above).

---

## Demo Program 3 — Reading user input with `read`

### What it teaches
- Prompting and reading input into variables

### Script
```sh
#!/bin/sh
#Filename: shelldemo3
echo "Please enter your first name: \c"
read firstname
echo "Please enter your surname: \c"
read surname
echo "Welcome $firstname $surname!"
```

### Expected output (example interaction)
```
Please enter your first name: John
Please enter your surname: Smith
Welcome John Smith!
```

### Tip (more portable prompting)
Some shells handle `\c` differently. In Bash, you can do:

```bash
read -p "Please enter your first name: " firstname
```

---

## Demo Program 4 — Command-line arguments (`$1`, `$2`, `$*`)

### What it teaches
- Using command-line arguments passed to the script

### Script
```sh
#!/bin/sh
#Filename: shelldemo4
#Run as: ./shelldemo4 Joe Bloggs 01/04/1980
echo "Welcome $1 $2, born on $3."
echo "Total arguments: $#"
echo "All arguments: $*"
```

### Expected output (example)
Command:

```bash
./shelldemo4 Joe Bloggs 01/04/1980
```

Output:

```
Welcome Joe Bloggs, born on 01/04/1980.
Total arguments: 3
All arguments: Joe Bloggs 01/04/1980
```

### Common mistakes & tips
- `$*` merges arguments; for safer looping over args, prefer `"$@"` in bash scripts.

---

## Demo Program 5 — `if / then / else` (branching)

### What it teaches
- Numeric comparison with `test` (`[ ... ]`)

### Script
```sh
#!/bin/sh
#Filename: shelldemo5
echo "Enter a number (1-10): \c"
read num
if [ "$num" -lt 5 ]
then
  echo "Number is less than 5"
else
  echo "Number is 5 or greater"
fi
```

### Expected output (example)
If user enters `3`:

```
Number is less than 5
```

### Common mistakes & tips
- `[ ... ]` **requires spaces**: `[ "$num" -lt 5 ]` not `["$num"-lt5]`.
- Quote variables inside tests to avoid errors on empty input.

---

## Demo Program 6 — `case ... esac` (multi-branch selection)

### What it teaches
- Multi-option branching using `case`

### Script
```sh
#!/bin/sh
#Filename: shelldemo6
echo "Enter 1 (date), 2 (hostname), 3 (calendar): \c"
read choice
case $choice in
  1) date;;
  2) hostname;;
  3) cal;;
  *) echo "Invalid choice";;
esac
```

### Expected output (example)
If user enters `2`, it prints your machine hostname (example):

```
my-laptop
```

### Tip
`case` is great for menu programs because it’s cleaner than multiple `if/elif`.

---

## Demo Program 7 — `for` loop

### What it teaches
- Looping through a list of words

### Script
```sh
#!/bin/sh
#Filename: shelldemo7
for lang in C Java Python Bash Go
do
  echo "Language: $lang"
done
```

### Expected output
```
Language: C
Language: Java
Language: Python
Language: Bash
Language: Go
```

---

## Demo Program 8 — `while` loop

### What it teaches
- Repeating while a condition remains true
- Arithmetic with `(( ... ))` (Bash)

### Script
```bash
#!/bin/bash
#Filename: shelldemo8
n=1
while (( n <= 5 ))
do
  echo "Count: $n"
  n=$(( n + 1 ))
done
```

### Expected output
```
Count: 1
Count: 2
Count: 3
Count: 4
Count: 5
```

### Common mistakes & tips
- `(( ... ))` is **bash arithmetic syntax**; use `#!/bin/bash`.

---

# Section B — Arrays

An **array** holds **multiple values** in one variable.

Bash supports:
- **Indexed arrays**: access by number (0,1,2,…)
- **Associative arrays**: access by key (name, id, dept, …)

---

## B.1 — Indexed Arrays

### Concept (simple explanation)
You store items in order, and each item has an index starting from **0**.

---

### Demo Program 9 — Declaring and accessing an indexed array

#### Script
```bash
#!/bin/bash
#Filename: shelldemo9
#Declare an array of programming languages
languages=("C" "C++" "Java" "Python" "Bash")

#Access individual elements using index
echo "First language: ${languages[0]}"
echo "Third language: ${languages[2]}"

#Print all elements
echo "All languages: ${languages[@]}"

#Print the number of elements
echo "Total languages: ${#languages[@]}"
```

#### Expected output
```
First language: C
Third language: Java
All languages: C C++ Java Python Bash
Total languages: 5
```

#### Key points
- `${languages[0]}` → first element
- `${languages[@]}` → all elements
- `${#languages[@]}` → number of elements (`#` means “length/count” here)

#### Common mistakes & tips
- Don’t forget braces: use `${languages[0]}` not `$languages[0]`.
- Always quote expansions when looping: `"${languages[@]}"` (see next demo).

---

### Demo Program 10 — Iterating over an indexed array with a `for` loop

#### Script
```bash
#!/bin/bash
#Filename: shelldemo10
fruits=("Apple" "Banana" "Cherry" "Date" "Elderberry")

echo "Fruit list:"
for fruit in "${fruits[@]}"
do
  echo " - $fruit"
done

#Iterate using numeric indices
echo ""
echo "Indexed access:"
for (( i=0; i<${#fruits[@]}; i++ ))
do
  echo " fruits[$i] = ${fruits[$i]}"
done
```

#### Expected output
```
Fruit list:
 - Apple
 - Banana
 - Cherry
 - Date
 - Elderberry

Indexed access:
 fruits[0] = Apple
 fruits[1] = Banana
 fruits[2] = Cherry
 fruits[3] = Date
 fruits[4] = Elderberry
```

#### Why two loop styles?
- `for fruit in ...` is simpler when you only need values.
- `for (( i=0; ... ))` is best when you also need the **index**.

#### Common mistakes & tips
- Use `"${fruits[@]}"` not `${fruits[@]}` to avoid breaking items containing spaces.

---

## B.2 — Associative Arrays

### Concept (simple explanation)
Like a dictionary / hash map:
- Instead of `array[0]`, you use keys like `array[name]`.

### Requirements
- Bash **4.0+**
- Must declare with:

```bash
declare -A arrayname
```

---

### Demo Program 11 — Declaring and using an associative array

#### Script
```bash
#!/bin/bash
#Filename: shelldemo11
#Must declare as associative array
declare -A student

#Assign key-value pairs
student[name]="Alice"
student[id]="2021-CS-042"
student[dept]="Computer Science"
student[cgpa]="3.82"

#Access values by key
echo "Name : ${student[name]}"
echo "Student ID : ${student[id]}"
echo "Department : ${student[dept]}"
echo "CGPA : ${student[cgpa]}"

#Print all keys
echo "Keys: ${!student[@]}"
```

#### Expected output
```
Name : Alice
Student ID : 2021-CS-042
Department : Computer Science
CGPA : 3.82
Keys: name id dept cgpa
```

#### Key points
- `${!student[@]}` → all keys (`!` means “keys of” for associative arrays)
- `${student[@]}` → all values

#### Common mistakes & tips
- Forgetting `declare -A` makes Bash treat it like an indexed array.
- Associative arrays are Bash-only; not available in `/bin/sh` on many systems.

---

# Section C — Functions

Functions let you group commands under a name so you can reuse them and keep scripts organized.

---

## C.1 — Defining and Calling Functions

### Demo Program 12 — Basic function definition and calling

#### Script
```bash
#!/bin/bash
#Filename: shelldemo12

#Function definition
greet() {
  echo "Hello! Welcome to Shell Programming."
  echo "Today is: $(date +%A\ %d\ %B\ %Y)"
}

show_separator() {
  echo "─────────────────────────────────────"
}

#Calling the functions
show_separator
greet
show_separator
```

#### Expected output (example)
```
─────────────────────────────────────
Hello! Welcome to Shell Programming.
Today is: Sunday 12 April 2026
─────────────────────────────────────
```

#### Tips
- Define functions **before** calling them.
- `$(date ...)` is command substitution.

---

## C.2 — Functions with Arguments

### Concept
Inside a function, `$1`, `$2`, etc. refer to the **function’s arguments**, not the script’s arguments.

---

### Demo Program 13 — Passing arguments to a function

#### Script
```bash
#!/bin/bash
#Filename: shelldemo13

add_numbers() {
  local a=$1 # first argument
  local b=$2 # second argument
  local sum=$(( a + b ))
  echo "Sum of $a and $b is: $sum"
}

print_box() {
  local msg=$1
  local line="=========================="
  echo "$line"
  echo " $msg"
  echo "$line"
}

add_numbers 15 27
add_numbers 100 250
print_box "Function Arguments Demo"
```

#### Expected output
```
Sum of 15 and 27 is: 42
Sum of 100 and 250 is: 350
==========================
 Function Arguments Demo
==========================
```

#### Important rule: use `local`
Without `local`, variables become **global** and can overwrite other values in your script.

---

## C.3 — Return Values from Functions

### Concept (simple explanation)
In shell:
- `return` is for **exit status** only (0–255)
- To “return” a real value, you usually **echo it** and capture with `$(...)`

---

### Demo Program 14 — Returning a value from a function

#### Script
```bash
#!/bin/bash
#Filename: shelldemo14

#Method 1: capture output with $(...)
multiply() {
  local result=$(( $1 * $2 ))
  echo $result # 'echo' is how we return a value
}

#Method 2: use return for exit status (0=success)
is_even() {
  if (( $1 % 2 == 0 )); then
    return 0 # true / success
  else
    return 1 # false / failure
  fi
}

#Using Method 1
product=$(multiply 7 8)
echo "7 x 8 = $product"

#Using Method 2
echo "\nTesting numbers 1 to 6:"
for n in 1 2 3 4 5 6
do
  if is_even $n; then
    echo " $n is even"
  else
    echo " $n is odd"
  fi
done
```

#### Expected output
```
7 x 8 = 56
Testing numbers 1 to 6:
 1 is odd
 2 is even
 3 is odd
 4 is even
 5 is odd
 6 is even
```

#### Common mistakes & tips
- `return 56` does **not** “return 56” like C; it sets an exit code (limited range).
- Prefer `echo` + capture for actual values: `val=$(func ...)`.

---

## C.4 — Recursive Functions

### Concept
A recursive function calls itself, usually with a smaller input each time, until a base case.

---

### Demo Program 15 — Factorial using recursion

#### Script
```bash
#!/bin/bash
#Filename: shelldemo15

factorial() {
  local n=$1
  if (( n <= 1 )); then
    echo 1
  else
    local sub=$(factorial $(( n - 1 )))
    echo $(( n * sub ))
  fi
}

echo "Factorials from 1 to 8:"
for i in 1 2 3 4 5 6 7 8
do
  result=$(factorial $i)
  echo " $i! = $result"
done
```

#### Expected output
```
Factorials from 1 to 8:
 1! = 1
 2! = 2
 3! = 6
 4! = 24
 5! = 120
 6! = 720
 7! = 5040
 8! = 40320
```

#### Tip
Recursion in shell is fine for small tasks, but avoid very large recursion (performance/limits).

---

# Section D — String Operations

Bash has powerful built-in string operators (no external tools needed for many tasks).

---

## Demo Program 16 — Common string operations

### What it teaches
- Length
- Substring extraction
- Upper/lowercase conversion
- Replace first vs replace all
- Extract filename from path
- Remove file extension

### Script
```bash
#!/bin/bash
#Filename: shelldemo16

str="Hello, Shell Programming!"

#Length of string
echo "String : $str"
echo "Length : ${#str}"

#Substring: ${var:start:length}
echo "Chars 7-11 : ${str:7:5}"

#Convert to uppercase / lowercase
echo "Uppercase : ${str^^}"
echo "Lowercase : ${str,,}"

#Replace first occurrence
echo "Replace first : ${str/l/L}"

#Replace all occurrences
echo "Replace all : ${str//l/L}"

#Strip prefix
path="/home/student/scripts/demo.sh"
echo "Filename : ${path##*/}"

#Strip suffix
echo "Without ext : ${path%.*}"
```

### Expected output
```
String : Hello, Shell Programming!
Length : 25
Chars 7-11 : Shell
Uppercase : HELLO, SHELL PROGRAMMING!
Lowercase : hello, shell programming!
Replace first : HeLlo, Shell Programming!
Replace all : HeLLo, SheLL Programming!
Filename : demo.sh
Without ext : /home/student/scripts/demo
```

### Common mistakes & tips
- `${str:7:5}` counts from **0**.
- `${path##*/}` means “remove the longest prefix ending with `/`”.

---

## Demo Program 17 — Checking string conditions

### What it teaches
- Detect empty string (`-z`)
- Minimum length check (`${#var}`)
- Pattern rules using `case`

### Script
```bash
#!/bin/bash
#Filename: shelldemo17

echo "Enter a username: \c"
read username

#Check if string is empty
if [ -z "$username" ]; then
  echo "Error: Username cannot be empty."
  exit 1
fi

#Check string length is at least 4
if [ ${#username} -lt 4 ]; then
  echo "Error: Username must be at least 4 characters."
  exit 1
fi

#Check if it starts with a letter (using case)
case $username in
  [a-zA-Z]*)
    echo "Valid username: $username";;
  *)
    echo "Error: Username must start with a letter.";;
esac
```

### Expected output (examples)
If user enters nothing:

```
Error: Username cannot be empty.
```

If user enters `12ab`:

```
Error: Username must start with a letter.
```

If user enters `john5`:

```
Valid username: john5
```

### Tips
- Always quote `"$username"` in `[ -z ... ]` checks to avoid syntax problems.
- `case` pattern matching is often easier than complex regex in `grep`.

---

# Section E — File and Directory Tests

Scripts often need to check:
- Does a file exist?
- Is it a file or directory?
- Is it readable/writable/executable?
- Is it empty?

---

## Demo Program 18 — Testing file and directory properties

### Script
```bash
#!/bin/bash
#Filename: shelldemo18

target="$1"

if [ -z "$target" ]; then
  echo "Usage: ./shelldemo18 <filename>"
  exit 1
fi

echo "Inspecting: $target"
echo "────────────────────────────────"

[ -e "$target" ] && echo "[YES] Path exists" || echo "[NO] Path does not exist"
[ -f "$target" ] && echo "[YES] Is a regular file" || echo "[NO] Not a regular file"
[ -d "$target" ] && echo "[YES] Is a directory" || echo "[NO] Not a directory"
[ -r "$target" ] && echo "[YES] Is readable" || echo "[NO] Not readable"
[ -w "$target" ] && echo "[YES] Is writable" || echo "[NO] Not writable"
[ -x "$target" ] && echo "[YES] Is executable" || echo "[NO] Not executable"
[ -s "$target" ] && echo "[YES] File is non-empty" || echo "[NO] File is empty"
[ -L "$target" ] && echo "[YES] Is a symbolic link" || echo "[NO] Not a symbolic link"
```

### Expected output (example)
Command:

```bash
./shelldemo18 shelldemo18
```

Output:

```
Inspecting: shelldemo18
────────────────────────────────
[YES] Path exists
[YES] Is a regular file
[NO] Not a directory
[YES] Is readable
[YES] Is writable
[YES] Is executable
[YES] File is non-empty
[NO] Not a symbolic link
```

### Test flags summary
| Test | Meaning |
|---|---|
| `-e file` | True if file/dir exists |
| `-f file` | True if regular file |
| `-d file` | True if directory |
| `-r file` | True if readable |
| `-w file` | True if writable |
| `-x file` | True if executable |
| `-s file` | True if non-empty |
| `-L file` | True if symbolic link |
| `-z string` | True if string is empty |
| `-n string` | True if string is non-empty |
| `f1 -nt f2` | True if f1 newer than f2 |
| `f1 -ot f2` | True if f1 older than f2 |

### Common mistakes & tips
- Always quote paths: `[ -e "$target" ]` (handles spaces safely).
- `&&` / `||` is compact; for complex logic, prefer `if` blocks.

---

# Section F — Arithmetic and Math Operations

Bash supports **integer arithmetic** natively.

For **floating point**, use `bc`.

---

## Demo Program 19 — Integer arithmetic using `$(( ))`

### Script
```bash
#!/bin/bash
#Filename: shelldemo19

a=36
b=7

echo "a = $a, b = $b"
echo "─────────────────────────────"
echo "Addition : $(( a + b ))"
echo "Subtraction : $(( a - b ))"
echo "Multiplication : $(( a * b ))"
echo "Division : $(( a / b ))"
echo "Modulus : $(( a % b ))"
echo "Exponent (a^2) : $(( a ** 2 ))"

#Compound assignment operators
a=$(( a + 10 ))
echo "a after += 10 : $a"

#Increment and decrement
(( a++ ))
echo "a after a++ : $a"
(( b-- ))
echo "b after b-- : $b"
```

### Expected output
```
a = 36, b = 7
─────────────────────────────
Addition : 43
Subtraction : 29
Multiplication : 252
Division : 5
Modulus : 1
Exponent (a^2) : 1296
a after += 10 : 46
a after a++ : 47
b after b-- : 6
```

### Common mistakes & tips
- Bash integer division truncates: `36/7 = 5` (no decimals).
- Use `(( ... ))` for arithmetic conditions and increments.

---

## Demo Program 20 — Floating-point arithmetic using `bc`

### Concept
`bc` is a command-line calculator.
`scale=N` sets decimal precision.

### Script
```bash
#!/bin/bash
#Filename: shelldemo20
#bc is a command-line calculator. Use "scale" to set decimal places.

a=22
b=7

#Calculate pi (22/7) to 10 decimal places
pi=$(echo "scale=10; $a / $b" | bc)
echo "22 / 7 (10 d.p.) = $pi"

#Square root
root=$(echo "scale=4; sqrt(2)" | bc)
echo "Square root of 2 = $root"

#Area of a circle (r=5)
area=$(echo "scale=3; 3.14159 * 5 * 5" | bc)
echo "Area of circle (r=5) = $area"
```

### Expected output
```
22 / 7 (10 d.p.) = 3.1428571428
Square root of 2 = 1.4142
Area of circle (r=5) = 78.539
```

### Common mistakes & tips
- On some systems, `sqrt()` needs `bc -l` (math library):

```bash
echo "scale=4; sqrt(2)" | bc -l
```

- If `bc` is missing (Ubuntu/Debian):

```bash
sudo apt-get install bc
```

---

# Section H — Putting It All Together (Comprehensive Example)

This final script combines:
- Arrays
- Functions
- Arithmetic
- Output formatting (`printf`)
- Floating point average using `bc`
- Return status (pass/fail check)

---

## Demo Program 27 — Student grade calculator

### What the script does
- Stores student names + marks in **parallel arrays**
- Uses `get_grade()` to assign A/B/C/D/F
- Uses `is_pass()` to check pass/fail using exit status
- Prints a formatted report
- Computes class average and pass count

### Script
```bash
#!/bin/bash
#Filename: shelldemo27 — Student Grade Calculator

#────────────────────────────────────────────────
# Data: Parallel arrays for student name and marks
#────────────────────────────────────────────────
names=("Alice" "Bob" "Charlie" "Diana" "Ethan")
marks=(82 55 91 47 73)

#────────────────────────────────────────────────
# Function: assign a letter grade
#────────────────────────────────────────────────
get_grade() {
  local mark=$1
  if (( mark >= 80 )); then echo "A"
  elif (( mark >= 70 )); then echo "B"
  elif (( mark >= 60 )); then echo "C"
  elif (( mark >= 50 )); then echo "D"
  else echo "F"
  fi
}

#────────────────────────────────────────────────
# Function: check if a student passed
#────────────────────────────────────────────────
is_pass() {
  (( $1 >= 50 )) && return 0 || return 1
}

#────────────────────────────────────────────────
# Main: Print report
#────────────────────────────────────────────────
echo "╔══════════════════════════════════════╗"
echo "║ STUDENT GRADE REPORT ║"
echo "╚══════════════════════════════════════╝"
printf "%-12s %-6s %-6s %-8s\n" "Name" "Mark" "Grade" "Result"
echo "────────────────────────────────────────"

total=0
pass_count=0

for (( i=0; i<${#names[@]}; i++ ))
do
  name=${names[$i]}
  mark=${marks[$i]}
  grade=$(get_grade $mark)
  total=$(( total + mark ))

  if is_pass $mark; then
    result="Pass"
    (( pass_count++ ))
  else
    result="FAIL"
  fi

  printf "%-12s %-6s %-6s %-8s\n" "$name" "$mark" "$grade" "$result"
done

echo "────────────────────────────────────────"
avg=$(echo "scale=1; $total / ${#names[@]}" | bc)
echo "Class Average : $avg"
echo "Students Pass : $pass_count / ${#names[@]}"
```

### Expected output
```
╔══════════════════════════════════════╗
║ STUDENT GRADE REPORT ║
╚══════════════════════════════════════╝
Name         Mark   Grade  Result
────────────────────────────────────────
Alice        82     A      Pass
Bob          55     D      Pass
Charlie      91     A      Pass
Diana        47     F      FAIL
Ethan        73     B      Pass
────────────────────────────────────────
Class Average : 69.6
Students Pass : 4 / 5
```

### Common mistakes & tips
- **Parallel arrays must stay aligned**: `names[i]` must match `marks[i]`.
- If you add a student name, don’t forget to add a corresponding mark.
- `printf` formatting:
  - `%-12s` means “left-align string in width 12”
  - Helps produce clean tables.

---

# Extra Beginner Tips (Related & Useful)

## 1) Make scripts safer with “strict mode” (Bash)
For larger scripts, consider:

```bash
set -u  # error on undefined variables
set -e  # exit on command failure (use carefully)
```

## 2) Quick debugging
Run with tracing:

```bash
bash -x ./yourscript.sh
```

This prints each command as it executes.

---

# Checklist of Common Mistakes (Across the Lab)

- Using `#!/bin/sh` but writing Bash-only features (arrays, `declare -A`, `(( ))`).
- Missing spaces in tests: `[ "$x" -lt 5 ]` must have spaces.
- Forgetting quotes around variables in tests and file paths.
- Expecting `return` to return a real number/string (it only returns exit status).
- Not using `local` inside functions → accidental global variable bugs.
````

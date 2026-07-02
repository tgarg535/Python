# Python Basics — In-Depth Notes

Covers: Predefined Objects, Keywords & Identifiers · Typecasting · Operators · Conditionals · Loops · Mutability & Immutability

---

## 1. Predefined Objects, Keywords, and Identifiers

### 1.1 Variables and Predefined Data Types
A variable is a name that references a value stored in memory. Python automatically detects the data type based on the assigned value — this is called **dynamic typing**.

```python
a = 3
type(a)      # int

b = 4.5
type(b)      # float

c = "Ajay"
type(c)      # str
```

**Core predefined (built-in) data types:**

| Value | Type | Notes |
|---|---|---|
| `3` | `int` | Whole numbers |
| `4.5` | `float` | Decimal numbers |
| `"Ajay"` | `str` | Text, enclosed in quotes |
| `True` / `False` | `bool` | Boolean values |
| `None` | `NoneType` | Represents "no value" / absence of a value |
| `5+6j` | `complex` | Complex numbers; `.real` and `.imag` extract parts |

```python
com = 5 + 6j
type(com)     # complex
com.real      # 5.0
com.imag      # 6.0
```

**Boolean arithmetic** — `True` behaves as `1` and `False` as `0`:
```python
True - False    # 1
True * False    # 0
```

**None** represents the absence of a value (not zero, not empty string):
```python
f = None
type(f)   # NoneType
```

### 1.2 Identifiers (Variable Naming Rules)
An identifier is the name given to variables, functions, classes, etc.

**Rules:**
- Cannot start with a digit (`1name = "Ram"` → `SyntaxError`)
- Cannot be a keyword
- Cannot contain hyphens or spaces — use underscores instead
  - `a-bc = "Ram"` → `SyntaxError: cannot assign to expression`
  - `data-science = "pwskills"` → `SyntaxError`
  - `data_science = "pwskills"` → ✅ valid
- Cannot start with special characters like `@` or `%`
  - `@abc = 2.3` → `SyntaxError`
  - `%abc = "Ram"` → interpreted as an (invalid) IPython magic command
- Can start with an underscore: `_name = "Rahul"` → ✅ valid
- **Cannot assign to a literal** — the left side of `=` must be a name, not a value:
  - `5 = a` → `SyntaxError: cannot assign to literal here`
- **Python is case-sensitive**:
  ```python
  a = 5
  A = 15
  a   # 5
  A   # 15
  ```

### 1.3 Keywords
Keywords are **reserved words** with a predefined meaning and special purpose in Python. They **cannot be used as identifiers/variable names**.

View the full list programmatically:
```python
help('keywords')
```
Output includes: `False, None, True, and, as, assert, async, await, break, class, continue, def, del, elif, else, except, finally, for, from, global, if, import, in, is, lambda, nonlocal, not, or, pass, raise, return, try, while, with, yield`

Since `print` is a built-in function name (not a hard keyword), reassigning it (e.g. `print = "Ajay"`) is technically possible but strongly discouraged — it would overwrite the built-in function.

### 1.4 Indentation
Python uses **indentation** (whitespace) to define blocks of code, rather than braces `{}`. This enforces readability.
```python
if 3 > 2:
    print("greater")   # indented block belongs to the if statement
```

### 1.5 Statements
A **statement** is a fundamental instruction/block of code that the interpreter can execute. Types include:
- **Assignment statement**: `a = 5`
- **Expression statement**: `a + b` (produces a value)
- **Conditional statements**: `if / elif / else`
- **Loop statements**: `for`, `while`

### 1.6 Comments
Used to explain code; ignored by the interpreter.
```python
# Single-line comment

'''
Multi-line comment
using triple single quotes
'''

"""
Multi-line comment
using triple double quotes
"""
```

### 1.7 Input / Output
- `print()` displays output.
- `input()` takes user input — **input always returns a string**, regardless of what the user types.
```python
name = input("please enter your name: ")
type(name)     # str, even if user enters a number like "2"
```
To use the input as a number, it must be explicitly converted (typecast):
```python
name = int(name)
type(name)     # int
```

---

## 2. Typecasting

**Typecasting (type conversion)** is the process of changing the data type of a value/object. It's needed because operators can behave unexpectedly (or raise errors) when applied to mismatched data types.

### 2.1 Why Typecasting Is Needed
```python
"2" + 3     # TypeError: can only concatenate str (not "int") to str
```
Fix using explicit conversion:
```python
int("2") + 3     # 5
```

### 2.2 Implicit vs Explicit Typecasting

**Implicit typecasting** — Python automatically converts data types during an operation (usually promoting to the "larger"/more general type), without any function call:
```python
2 + 3       # 5      (int)
2 + 3.5     # 5.5    (float — int automatically promoted to float)
```

**Explicit typecasting** — the programmer manually converts data types using built-in functions: `int()`, `float()`, `str()`, `bool()`.
```python
a = "2"
int(a)      # 2   (str -> int)
float(a)    # 2.0 (str -> float)
```

### 2.3 Common Conversions

| Conversion | Function | Example | Result |
|---|---|---|---|
| String → Integer | `int()` | `int("2")` | `2` |
| Float → Integer | `int()` | `int(3.4)` | `3` (truncates, does not round) |
| Integer → Float | `float()` | `float(2)` | `2.0` |
| String → Float | `float()` | `float("2.3")` | `2.3` |
| Integer → String | `str()` | `str(5)` | `'5'` |
| Any number → Boolean | `bool()` | `bool(0)` → `False`, `bool(1)` / `bool(100)` → `True` |

**Important gotcha:** `int()` on a float **truncates** (drops the decimal), it does not round:
```python
int(3.4)    # 3
```

### 2.4 Conversion Failures
Non-numeric strings cannot be converted to `int`/`float` — this raises a `ValueError` (not a `TypeError`):
```python
int("Ajay")     # ValueError: invalid literal for int() with base 10: 'Ajay'
float("Ajay")   # ValueError: could not convert string to float: 'Ajay'
```

### 2.5 String Concatenation vs Numeric Addition
The `+` operator behaves differently depending on type — combining strings is called **concatenation**:
```python
"pw" + "skills"        # 'pwskills'   (concatenation)
"Ajay" + " Kumar"       # 'Ajay Kumar'
"Ajay" + 5              # TypeError — cannot mix str and int with +
```

---

## 3. Operators

Operators are special symbols/keywords used to perform operations and manipulate data.

### 3.1 Arithmetic Operators
| Operator | Meaning | Example | Result |
|---|---|---|---|
| `+` | Addition | `5 + 2` | `7` |
| `-` | Subtraction | `20 - 3` | `17` |
| `*` | Multiplication | `3 * 4` | `12` |
| `/` | Division (true division, always returns float) | `20 / 4` | `5.0` |
| `%` | Modulus (remainder) | `17 % 4` | `1` |
| `**` | Exponent (power) | `2 ** 3` | `8` |
| `//` | Floor division (rounds down to nearest whole number) | `4 // 3` | `1` |

Note: regular `/` always returns a float even if divisible evenly (`20/4 = 5.0`), whereas `//` discards the remainder: `4 // 3 = 1` vs `4 / 3 = 1.333...`.

### 3.2 Comparison Operators
Compare two values and return a **boolean**.
| Operator | Meaning | Example | Result |
|---|---|---|---|
| `==` | Equal to | `2 == 2` | `True` |
| `!=` | Not equal to | `2 != 2` | `False` |
| `>` | Greater than | `10 > 2` | `True` |
| `<` | Less than | `10 < 5` | `False` |
| `>=` | Greater than or equal | `10 >= 10` | `True` |
| `<=` | Less than or equal | `10 <= 12` | `True` |

### 3.3 Logical Operators
Combine boolean expressions.
| Operator | Rule | Example | Result |
|---|---|---|---|
| `and` | True only if **both** are True | `True and False` | `False` |
| `or` | True if **at least one** is True | `False or True` | `True` |
| `not` | Inverts the boolean | `not True` | `False` |

Truth table for `and`: `T and T = T`, `T and F = F`, `F and T = F`, `F and F = F`
Truth table for `or`: `T or F = T`, `F or T = T`

### 3.4 Assignment Operators
`=` assigns a value. Compound assignment operators update a variable in place:
```python
a = 10
a += 5     # equivalent to a = a + 5   -> 15
a *= 2     # equivalent to a = a * 2   -> 30 (continuing from above example gives 50 in notebook)
a /= 3     # equivalent to a = a / 3   -> float result, e.g. 16.666...
```
Note: `/=` always yields a `float` result since `/` is true division.

### 3.5 Membership Operators
Check whether a value exists inside a sequence (string, list, etc.) — returns boolean.
```python
"P" in "PWSKILLS"          # True
"data" in "PWSKILLS"       # False
"data" not in "PWSKILLS"   # True
"data" in ["data", "science", "ajay"]   # True
```

### 3.6 Identity Operators
`is` / `is not` compare whether two variables point to the **same object in memory** (not just equal value).
```python
a = 2
b = 3
a is b        # False (different objects)

a = "Pw Skills"
b = a
b is a        # True (b references the same object as a)

a = "pwskills"
b = "data"
a is b        # False
a is not b    # True
```
> Key distinction: `==` checks **value equality**, `is` checks **identity/memory location**.

### 3.7 Bitwise Operators
Operate at the individual bit level of integers (binary representation).
| Operator | Meaning | Example | Result |
|---|---|---|---|
| `&` | Bitwise AND | `18 & 3` | `2` |
| `\|` | Bitwise OR | `3 \| 5` | `7` |
| `~` | Bitwise NOT (negation) | `~3` | `-4` |
| `^` | Bitwise XOR (1 when exactly one operand bit is 1) | `5 ^ 3` | `6` |
| `<<` | Left shift (adds zeros on the right, multiplies by 2 per shift) | `5 << 1` | `10` |
| `>>` | Right shift (removes bits from the right, divides by 2 per shift) | `10 >> 1` | `5` |

Use `bin()` to inspect binary representation, e.g. `bin(10)` → `'0b1010'`.

Examples:
```python
bin(10)      # '0b1010'
bin(18)      # '0b10010'
18 & 3       # 2

35 << 3      # 280   (shifts binary left by 3 places, i.e. * 2**3)
280 >> 3     # 35    (shifts binary right by 3 places, i.e. // 2**3)
```

### 3.8 Order of Precedence
Python evaluates expressions left to right based on operator precedence; **parentheses always take priority** and should be used to make evaluation order explicit.
```python
2 + 3 - 1          # 4   (left to right)
(5 + 3) - 4        # 4
4 - (5 + 3)        # -4
(10 + 5) + (8/2)   # 19.0
```

---

## 4. Conditionals (Flow Control)

Flow control governs the order in which statements execute. Conditional statements let a program make decisions based on preconditions.

**Types of conditional statements:**
- `if`
- `if...else`
- `if...elif...else`
- Nested `if...else`

### 4.1 `if` Statement
Executes a block **only if** the condition evaluates to `True`.
```python
# syntax
# if condition:
#     block of code

a = 100
if a > 0:
    print("The number is greater than 0.")
```
> **Indentation is mandatory** — omitting it raises `IndentationError: expected an indented block`.

Combining multiple conditions with logical operators:
```python
number = 42
if (number % 2 == 0) & (number > 0):
    print("This is the number of interest")

grade = 8
co_curr = True
if (grade >= 8) and (co_curr == True):
    print("You are all rounder")
```

### 4.2 `if...else` Statement
Executes the `if` block when the condition is `True`, otherwise executes the `else` block.
```python
weather = "sunny"
if weather == "sunny":
    print("I will play cricket")
else:
    print("I will watch TV")
```

Example — even/odd check:
```python
num = 4
if num % 2 == 0:
    print("even")
else:
    print("odd")
```

### 4.3 `if...elif...else` Statement
Used when there are **more than two** possible outcomes to check sequentially.
```python
# syntax
# if condition1:
#     ...
# elif condition2:
#     ...
# else:
#     ...

score = 75
if score >= 90:
    print("grade A")
elif 80 <= score < 90:
    print("Grade B")
elif 70 <= score < 80:
    print("Grade C")     # this branch executes since 70 <= 75 < 80
else:
    print("Grade D")
```
> Python supports **chained comparisons** like `80 <= score < 90` — equivalent to `80 <= score and score < 90`.

Important: `elif` branches are checked **in order**, and only the **first** True branch executes:
```python
a = 100000
if a < 100:
    print("This block will be executed if a is lesser than 100")
elif a > 2000:
    print("The number is huge")       # this executes
elif a > 100:
    print("This block will be executed if a is greater than 100")
else:
    print("The number is equals to 100")
```

### 4.4 Nested `if...else`
An `if`/`else` block placed inside another `if`/`else` block — used for multi-level decision-making.
```python
is_vip = False
age = 15
if is_vip:
    if age >= 18:
        if age < 65:
            print("welcome to vip customer")
        else:
            print("You are vip and eligible for senior citizen discount")
    else:
        print("Vip status is only for adult")
else:
    print("You are not a vip customer and will be charged a regular price")
```

### 4.5 Conditionals with `input()`
Since `input()` always returns a string, convert it before using it in numeric comparisons:
```python
a = int(input("Enter a no"))
if a >= 100:
    print("The no is greater than 100")
elif 0 <= a < 100:
    print("The no is greater than 0 and less than 100")
else:
    print("This is a negative no")
```

### 4.6 Practical Example — Nested Validation
```python
name = input("Please enter your name")
email = input("Please enter your email")
password = input("Please enter your password")

if name == "":
    print("Please enter a valid name, cannot be empty")
else:
    if "@" not in email:
        print("Please enter a valid email")
    else:
        if len(password) < 0:
            print("No password entered, please provide a valid password")
        else:
            print("Login/registration is successful")
```
> Note: `len(password) < 0` is a logical bug in this example — length can never be negative; the intended check should likely be `len(password) == 0`.

---

## 5. Loops

Loops allow a block of code to be **executed repeatedly**. Python has two loop types: `while` and `for`.

### 5.1 `while` Loop
Repeatedly executes a block **until a condition becomes False**.
```python
# syntax
# while condition:
#     block of code

n = 7
i = 1
while i < n:
    print(i)
    i = i + 1
# Output: 1 2 3 4 5 6
```

### 5.2 `while...else`
The `else` block executes **only if the loop completes normally** (i.e., without hitting a `break`).
```python
n = 7
i = 1
while i < n:
    print(i)
    i = i + 1
else:
    print("This will be executed when the while runs successfully without any break")
```

### 5.3 `break` Statement
Immediately **terminates/exits** the loop when a condition is met. When a loop is exited via `break`, its `else` block is **skipped**.
```python
n = 7
i = 1
while i < n:
    print(i)
    i = i + 1
    if i == 3:
        break
else:
    print("This will be executed when the while runs successfully without any break")
# Output: 1  2        (else block does NOT run, because of break)
```

### 5.4 `continue` Statement
**Skips the rest of the current iteration** and moves to the next one (loop is not terminated).
```python
n = 7
i = 1
while i < n:
    i = i + 1
    if i == 3:
        continue
    print(i)
else:
    print("This will be executed when the while runs successfully without any break")
# Output: 2 4 5 6 7   (3 is skipped, else DOES run since loop wasn't broken)
```

### 5.5 `for` Loop
Iterates over a sequence of elements (string, list, range, etc.).
```python
for i in "pwskills":
    print(i)
# Output: p w s k i l l s (each on its own line)

l = [1, 2, "Ajay", "pwskills"]
for i in l:
    print(i)
```

`for...else` — the `else` executes only if the loop completes without a `break`:
```python
for i in l:
    print(i)
else:
    print("This will be executed when for loop ends without a break statement")
```

`break` in a `for` loop:
```python
for i in l:
    if i == "Ajay":
        break
    print(i)
else:
    print("This will be executed when for loop ends without a break statement")
# Output: 1 2     (else is skipped due to break)
```

`continue` in a `for` loop:
```python
for i in l:
    if i == "Ajay":
        continue
    print(i)
else:
    print("This will be executed when for loop ends without a break statement")
# Output: 1 2 pwskills, then the else message (loop wasn't broken)
```

### 5.6 `range()` Function
Generates a sequence of numbers, commonly used with `for` loops.
```python
list(range(0, 10))          # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]  -- stop value excluded
list(range(0, 100, 5))      # [0, 5, 10, ..., 95]  -- step of 5
```
Syntax: `range(start, stop, step)`. The `stop` value is **never included**.

`print()` with `end` parameter controls what's printed after each value instead of a newline:
```python
for i in range(10):
    print(i, end=" ")     # 0 1 2 3 4 5 6 7 8 9
```

### 5.7 Nested Loops — Pattern Printing
A loop inside another loop — the inner loop completes all its iterations for each single iteration of the outer loop.

**Right triangle using `while`:**
```python
row = 1
while row <= 4:
    col = 1
    while col <= row:
        print("*", end="")
        col = col + 1
    print()
    row = row + 1
# *
# **
# ***
# ****
```

**Right triangle using `for`:**
```python
for i in range(4):
    for j in range(i + 1):
        print("*", end="")
    print()
```

**Square pattern (homework example):**
```python
side = 1
while side <= 3:
    width = 1
    while width <= 3:
        print("*", end=" ")
        width += 1
    print()
    side += 1
# * * *
# * * *
# * * *
```

### 5.8 Conditionals Inside Loops
Combining `if`/`elif` logic with loop control statements is common for filtering.
```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]

# skip specific values with continue
for num in numbers:
    if (num == 3) or (num == 7):
        continue
    print(num)
# Output: 1 2 4 5 6 8 9

# stop entirely with break
for num in numbers:
    if (num == 3) or (num == 7):
        break
    print(num)
# Output: 1 2
```

**Print even numbers:**
```python
for i in range(10):
    if i % 2 != 0:
        continue
    print(i)
# 0 2 4 6 8
```

**Print odd numbers:**
```python
for i in range(10):
    if i % 2 == 0:
        continue
    print(i)
# 1 3 5 7 9
```

### 5.9 Multiplication Table (Nested Loop + f-strings)
```python
for i in range(1, 6):
    for j in range(1, 11):
        product = i * j
        print(f"{i} * {j} = {product}")
    print()
```
Uses an **f-string** (`f"..."`) to embed variable values directly inside a string.

---

## 6. Mutability and Immutability

### 6.1 Core Concept
- **Mutable objects**: their state/value **can be changed** after creation (they support *item assignment*).
- **Immutable objects**: their state/value **cannot be changed** after creation (they do **not** support item assignment).

| Type | Mutable? |
|---|---|
| `list` | ✅ Mutable |
| `str` | ❌ Immutable |
| `int`, `float`, `bool`, `complex`, `tuple` | ❌ Immutable |
| `dict`, `set` | ✅ Mutable |

### 6.2 Indexing (applies to both mutable and immutable sequences)
Both strings and lists support **indexing** to access individual elements. Indexing starts at `0`, and negative indices count from the end.
```python
b = "Ajay"
b[0]      # 'A'   (first character)
b[2]      # 'a'   (third character)
b[-2]     # 'a'   (second-from-last character)
```

```python
list_cont = [1, 2, 3, 4.3, 3+5j, True, "Ajay"]
list_cont[0]     # 1
list_cont[4]     # (3+5j)
list_cont[6]     # 'Ajay'
list_cont[-1]    # 'Ajay'  (last element)
type(list_cont)  # list
```
> A list can hold **mixed data types** in a single container (int, float, complex, bool, str all together).

### 6.3 Lists Are Mutable
Individual elements of a list can be reassigned using index-based item assignment:
```python
list_cont = [1, 2, 3, 4.3, 3+5j, True, "Ajay"]

list_cont[6] = "Bijay"
list_cont     # [1, 2, 3, 4.3, (3+5j), True, 'Bijay']

list_cont[0] = 100
list_cont     # [100, 2, 3, 4.3, (3+5j), True, 'Bijay']

list_cont[1] = "pwskills"
list_cont     # [100, 'pwskills', 3, 4.3, (3+5j), True, 'Bijay']
```
This works because `list` is a mutable object/container — it **supports item assignment**.

### 6.4 Strings Are Immutable
Attempting the same kind of item assignment on a string raises a `TypeError`:
```python
b = "pwskills"

b[0] = "I"
# TypeError: 'str' object does not support item assignment

b[5] = "Ram"
# TypeError: 'str' object does not support item assignment
```
> To "change" a string, you must create a **new** string object (e.g., `b = "pwskills"` reassigns `b` to point to an entirely new string object — this is reassignment of the variable, not mutation of the original string).

### 6.5 Why This Matters
Understanding mutability affects:
- **Function behavior**: passing a mutable object (list) into a function and modifying it in place affects the original object; passing an immutable object (string, int) does not.
- **Memory & identity**: reassigning an immutable object creates a new object in memory (relevant to the `is` identity operator covered in Operators, §3.6), whereas mutating a mutable object modifies the same object in place.
- **Hashability**: immutable objects (like strings, numbers, tuples) can be used as dictionary keys or set elements; mutable objects (like lists) cannot.

---

## Quick Reference Summary

| Topic | Key Takeaway |
|---|---|
| Predefined objects | `int, float, str, bool, complex, NoneType` are Python's core built-in types; type is inferred dynamically |
| Identifiers | Must start with a letter/underscore, no spaces/hyphens, cannot be keywords, case-sensitive |
| Keywords | Reserved words (`if`, `for`, `while`, `class`, etc.) — cannot be reused as variable names |
| Typecasting | Implicit = automatic by Python; Explicit = manual via `int()`, `float()`, `str()`, `bool()` |
| Operators | Arithmetic, Comparison, Logical, Assignment, Membership (`in`), Identity (`is`), Bitwise |
| Conditionals | `if` / `if-else` / `if-elif-else` / nested `if`; indentation defines the block |
| Loops | `while` (condition-based) and `for` (sequence-based); `break` exits, `continue` skips, `else` runs only if no `break` |
| Mutability | Lists = mutable (support item assignment); Strings/numbers = immutable (do not) |


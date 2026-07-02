# Python Data Structures — In-Depth Interview Notes

Covers: Overview of Data Structures · Strings · Lists · Tuples & Sets · Dictionaries
Each section includes core concepts, code examples, common interview questions, and gotchas interviewers love to probe.

---

## 1. Overview of Data Structures

A **data structure** is a way of organizing and storing data so it can be accessed and manipulated efficiently.

Python's built-in data structures: `str`, `list`, `tuple`, `set`, `dict`, and `array` (from the `array` module).

### 1.1 Quick Comparison Table (★ most commonly asked interview table)

| Structure | Ordered? | Mutable? | Duplicates Allowed? | Indexed Access? | Syntax |
|---|---|---|---|---|---|
| `list` | Yes (insertion order) | ✅ Yes | ✅ Yes | ✅ Yes | `[1, 2, 3]` |
| `tuple` | Yes (insertion order) | ❌ No | ✅ Yes | ✅ Yes | `(1, 2, 3)` |
| `set` | ❌ No (unordered) | ✅ Yes | ❌ No (unique only) | ❌ No | `{1, 2, 3}` |
| `dict` | Yes (insertion order, Python ≥3.7) | ✅ Yes (values); keys must be immutable | Keys: No / Values: Yes | ✅ Yes (by key) | `{"k": "v"}` |
| `str` | Yes | ❌ No | ✅ Yes | ✅ Yes | `"text"` |

> **Interview tip:** "Ordered" for dict means Python guarantees insertion order is preserved since 3.7 (implementation detail in 3.6, language guarantee from 3.7) — but a dict is still **not sortable/indexable by position** the way a list is.

### 1.2 Lists — Quick Intro
An **ordered, mutable, heterogeneous** collection.
```python
num = [1, 2, 3, 4, 5]
type(num)      # list
num[0]         # 1
num[-1]        # 5
num[5]         # IndexError: list index out of range
```
Lists can store **heterogeneous data** (mixed types) in a single container:
```python
a = [1, 2, 3.3, "baingan", "tomato", "banana", True, False, 3+7j]
a.append("orange")     # adds to end
a.remove("baingan")    # removes first matching value
```

**Nested lists** (list of lists) — access with chained indexing:
```python
lis = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
lis[0][1]      # 2
lis[1][1]      # 5
```

Lists are **mutable** — supports item assignment:
```python
shopping_list = ["Baingan", "Apple", 2, 3.4, True]
shopping_list[0] = "Potato"
```

### 1.3 Tuples — Quick Intro
An **ordered, immutable, heterogeneous** collection. Used when data should **not** be modified — e.g., Aadhaar number, ATM PIN, employee ID.
```python
point = (2, 3)
type(point)     # tuple

emp_name = ("Ajay", "Ajay1", "Bijay", "Sanjay")
emp_name[2] = "Rahul"     # TypeError: 'tuple' object does not support item assignment
```
Tuples can be **nested**:
```python
box1 = ("a", "b")
box2 = ("c", "d")
choc_bag = (box1, box2)     # (('a', 'b'), ('c', 'd'))
```

### 1.4 Sets — Quick Intro
An **unordered collection of unique elements** — automatically removes duplicates.
```python
s = {"red", "orange", "red", "blue", "orange"}
s     # {'blue', 'orange', 'red'}   (duplicates auto-removed)

s[0]   # TypeError: 'set' object is not subscriptable  (no indexing — unordered)

s.add("pwskills")      # sets are mutable
s.remove("pwskills")
```
**Classic use case:** de-duplicating a collection.
```python
grocery_list = {"apple", "banana", "banana", "egg", "egg"}
# {'apple', 'banana', 'egg'}
```

### 1.5 Dictionaries — Quick Intro
Stores data as **key-value pairs**.
```python
phonebook = {"Dad": 1234, "Mom": 456}
phonebook["Dad"]        # 1234

# duplicate keys: the LAST value wins, earlier ones are silently overwritten
phonebook = {"Dad": 1234, "Mom": 456, "Dad": 780}
phonebook["Dad"]        # 780

phonebook.keys()        # dict_keys(['Dad', 'Mom'])
phonebook.values()      # dict_values([780, 456])
```

### 1.6 Arrays (`array` module)
Similar to a list, but stores **homogeneous** data only (all elements must be the same type) — more memory-efficient for numeric data than a list.
```python
from array import array
array('i', [1, 2, 3, 4])     # array('i', [1, 2, 3, 4])   -- 'i' = signed int type code
```
> **Interview tip:** Python's built-in `list` is heterogeneous and dynamically typed; `array.array` and NumPy arrays are homogeneous and more memory/performance efficient for numeric workloads. This is a classic "difference between list and array" interview question.

---

## 2. Strings — In Depth

A **string is a sequence of characters** used to represent and manipulate textual data. Internally, each character maps to a unique numeric code.

### 2.1 Character Encoding — ASCII vs Unicode (★ common interview topic)

| Encoding | Bits | Characters Covered | Notes |
|---|---|---|---|
| ASCII | 7 bits | 128 characters | American Standard Code for Information Interchange — covers basic English letters, digits, symbols |
| Unicode | 16 bits (conceptually) | Nearly every character of every language | UTF-8 and UTF-16 are the most widely used encoding schemes implementing Unicode |

```python
ord("A")        # 65   -- ordinal: numeric code of a character
chr(65)         # 'A'  -- character corresponding to a numeric code
ord("a")        # 97
chr(97)         # 'a'
```
Unicode escape sequences (`\uXXXX`) can represent characters from any script:
```python
'\u03A9'     # 'Ω'  (Omega)
'\u03A3'     # 'Σ'  (Sigma)
```

### 2.2 String Creation & Quoting
```python
string1 = "pwskills"       # double quotes
string1 = 'pwskills'       # single quotes — same result

"I'm a student"            # double quotes let you use an apostrophe freely
'''triple quotes'''        # multi-line strings
"""triple double quotes""" # multi-line strings
```
> An unterminated quote (e.g. a newline inside a single-quoted string without closing it) raises `SyntaxError: unterminated string literal`.

### 2.3 Concatenation
`+` combines (concatenates) strings — but only `+` works, not `-`, `*` between two strings arithmetically the way numbers do:
```python
"Hello" + " world"     # 'Hello world'
"Hello" - " world"     # TypeError: unsupported operand type(s) for -: 'str' and 'str'
```

### 2.4 Indexing and Slicing (★ heavily tested in interviews)
Strings are **zero-indexed**; negative indices count from the end.
```python
a = "Ajay"
a[0]    # 'A'
a[-1]   # 'y'
a[4]    # IndexError: string index out of range
```

**Slicing syntax:** `string[start:end:step]` — `start` is inclusive, `end` is **exclusive**, `step` defaults to `1`.
```python
s = "hello world!"
s[0:5]        # 'hello'      (index 5 excluded)
s[0:5:2]      # 'hlo'        (every 2nd char)
s[:]          # 'hello world!'   (full copy)
s[::2]        # 'hlowrd'     (every 2nd char, from start)
s[::-1]       # '!dlrow olleh'   (reversed — classic interview trick)
s[-3:]        # last 3 characters
s[:-3]        # everything except the last 3 characters
```
> **Interview favorite:** `s[::-1]` is the idiomatic one-liner to reverse a string.

### 2.5 Immutability (★ very common interview question)
Strings **cannot be modified in place**.
```python
s = "pwskills"
s[0] = "a"     # TypeError: 'str' object does not support item assignment
```
Any "modification" method (`.replace()`, `.upper()`, etc.) **returns a new string** — it does not alter the original.

### 2.6 Common String Methods

| Method | Purpose | Example |
|---|---|---|
| `len(s)` | Length of string | `len("student")` → `7` |
| `.replace(old, new)` | Replace substring (returns new string) | `"I am a student".replace("student", "teacher")` |
| `.lower()` / `.upper()` | Case conversion | `"Hello".lower()` → `'hello'` |
| `.title()` | Capitalize each word | `"hello world".title()` → `'Hello World'` |
| `.capitalize()` | Capitalize only first letter | `"i am a student".capitalize()` → `'I am a student'` |
| `.swapcase()` | Swap upper/lower case | `"Iam".swapcase()` → `'iAM'` |
| `.strip()` | Remove leading/trailing whitespace | `"  pwskills  ".strip()` → `'pwskills'` |
| `.split(sep)` | Split into a list by separator | `"a,b,c".split(",")` → `['a','b','c']` |
| `.startswith(x)` / `.endswith(x)` | Prefix / suffix check | `"pwskills".startswith("p")` → `True` |
| `.isalnum()` | True if all chars are alphanumeric | `"123abc".isalnum()` → `True` |
| `sorted(list_of_strings)` | Alphabetical sort (returns new list) | `sorted(["Ajay","Bijay"])` |

Method chaining is common:
```python
text = "BRB, TTYL, LOL"
text.replace("BRB", "Be right back").replace("TTYL", "talk to you later").replace("LOL", "Laughing out loud")
```

### 2.7 String Searching / Membership
```python
"am" in "I am a student"          # True
if "@" in email:                  # basic email validation pattern
    print("valid email")
if ".pdf" in document:            # file-extension check pattern
    print("Its a pdf file")
```

### 2.8 String Comparison
`==` compares value (case-sensitive):
```python
"Hello" == "hello"                # False
"Hello".lower() == "hello".lower()  # True — case-insensitive comparison pattern
```

### 2.9 Escape Sequences (★ common interview topic)

| Escape | Meaning |
|---|---|
| `\n` | Newline |
| `\t` | Tab |
| `\\` | Literal backslash |
| `\'` | Literal single quote |
| `\r` | Carriage return — moves cursor to start of line (can overwrite prior text when printed) |

```python
print("Name\tAge\tGrade\nAjay\t21\tA")     # tab and newline formatting

file_path = "C:\\USERS\\DOWNLOADS\\FILES.txt"   # escaped backslash for Windows paths

print("Hello, I am \rAjay, I am going to delhi")
# Output: "Ajay, I am going to delhi"  -- \r resets cursor to line start, overwriting "Hello, I am "
```

### 2.10 String Formatting (★ common interview topic — know all 3 methods)

**1. % formatting (old style):**
```python
name = "PW Skills"
"Hello %s, wish you a wonderful day" % name
```

**2. `.format()` method:**
```python
template = "Hello, {} welcome to {}"
template.format("Ajay", "Data Science")
```

**3. f-strings (formatted string literals — modern, preferred, Python 3.6+):**
```python
name = "Bijay"
f"hello {name}, how are you?"

celsius = 30
f"{celsius} is equals to {(celsius * 9/5)+32}F"    # f-strings can hold expressions, not just variables
```
> **Interview tip:** f-strings are the fastest and most readable; recommended in modern Python code.

### 2.11 Strings Are Iterable
```python
s = "pwskills"
for i in s:
    print(i)     # prints each character on its own line

count = 0
for i in s:
    count += 1
print(count)      # 8 — equivalent to len(s)
```

### 2.12 Common String Interview Questions
- **Reverse a string** → `s[::-1]`
- **Check palindrome** → `s == s[::-1]`
- **Count vowels/characters** → iterate + conditional counting, or use `.count()`
- **Check if two strings are anagrams** → `sorted(s1) == sorted(s2)`
- **Why are strings immutable in Python?** → For hashability (usable as dict keys/set elements), thread-safety, and memory optimization via string interning.

---

## 3. Lists — In Depth

Lists are an **ordered, mutable collection** — like "a shopping bag that can hold anything."

### 3.1 Creation & Basic Access
```python
grocery_list = ["Milk", "Orange", 1, 2.2, True, 3+5j]
grocery_list[0]      # 'Milk'
grocery_list[-1]     # (3+5j)
```

### 3.2 Slicing
Same `[start:end:step]` rules as strings — `end` exclusive:
```python
grocery_list[1:]       # everything from index 1 onward
grocery_list[1:4]      # indices 1, 2, 3 (4 excluded)
pages[-1]               # last element
pages[-2]               # second-to-last element
```

### 3.3 Adding Elements

| Method | Behavior |
|---|---|
| `.append(x)` | Adds `x` as a single new element at the end |
| `.insert(index, x)` | Inserts `x` **before** the given index |
| `.extend(iterable)` | Appends **each element** of another iterable to the list (in place) |
| `list1 + list2` | Concatenates and **returns a new list** (originals unchanged) |

```python
lis = ["Apple", "Orange"]
lis.append("Banana")           # ['Apple', 'Orange', 'Banana']

lis.insert(1, "potato")        # inserts BEFORE index 1

my_list = ["Apple", "Banana", "orange"]
brothers_list = ["brinjal", "potato"]
my_list.extend(brothers_list)  # my_list becomes ['Apple','Banana','orange','brinjal','potato']

# vs concatenation (does not mutate either list):
my_list + brothers_list        # returns a NEW combined list
```
> **Interview trap:** `.append(list2)` adds the *entire list* as a single nested element, whereas `.extend(list2)` unpacks and adds each element individually. This is a classic gotcha question.

### 3.4 Repetition Operator (`*`)
```python
[0] * 10             # [0,0,0,0,0,0,0,0,0,0]
[1, 2, 3] * 5         # repeats the whole sequence 5 times
"*" * 15              # '***************'
```

### 3.5 Membership
```python
"Milk" in grocery_list         # True
"POTATO" not in grocery_list   # True
```

### 3.6 Shallow Copy vs Deep Copy (★ one of the most important interview topics)

```python
grocery_list = ['Milk', 'Orange', 1, 2.2, True, (3+5j)]

a = grocery_list          # NOT a copy — 'a' just points to the SAME object in memory
a[0] = "potato"
grocery_list              # ['potato', 'Orange', 1, 2.2, True, (3+5j)]  -- original also changed!

b = grocery_list.copy()   # a genuine (shallow) copy — a new object
b[0] = "Milk"
grocery_list              # unaffected — still shows 'potato'
```
> **Key distinction for interviews:**
> - `a = grocery_list` → both names reference the **same object** (this is *aliasing*, not copying — sometimes loosely called "shallow" in casual conversation, but technically not a copy at all).
> - `b = grocery_list.copy()` (or `list(grocery_list)` / `grocery_list[:]`) → creates a **new outer list** (a true shallow copy) — changing top-level elements in `b` does NOT affect `grocery_list`.
> - **True shallow copy caveat**: if the list contains *nested* mutable objects (e.g. a list of lists), `.copy()` only copies the outer list — the inner nested lists are still shared references. To fully duplicate nested structures, use `copy.deepcopy()` from the `copy` module.

### 3.7 Sorting, Searching, Counting

| Method | Purpose |
|---|---|
| `sorted(lst)` | Returns a **new sorted list**, original unchanged |
| `lst.sort()` | Sorts the list **in place**, returns `None` |
| `lst.index(x)` | Returns index of the **first occurrence** of `x` |
| `lst.count(x)` | Counts occurrences of `x` |
| `len(lst)` | Number of elements |

```python
book_list = ["Data Str", "Algorithm", "web", "Algorithm"]
sorted(book_list)              # new sorted list
book_list.index("Algorithm")   # 1 (first occurrence)
book_list.count("Algorithm")   # 2
```

### 3.8 Removing Elements

| Method | Behavior |
|---|---|
| `.remove(x)` | Removes the **first matching value** `x` (raises `ValueError` if not found) |
| `.pop()` | Removes and **returns** the **last** element |
| `.pop(i)` | Removes and returns the element at index `i` |
| `.clear()` | Empties the list, but the variable/list object still exists |
| `del lst` | Deletes the variable entirely — using it afterward raises `NameError` |

```python
book_list.remove("Data Str")   # removes by value
book_list.pop()                 # removes & returns last element
book_list.pop(1)                # removes & returns element at index 1
book_list.clear()               # [] -- list still exists, just empty
del book_list                   # variable no longer exists
```
> **Interview distinction:** `.clear()` empties the list but the variable remains valid (`book_list` still `[]`); `del` removes the variable binding itself (`NameError` if accessed).

### 3.9 List Comprehension (★ frequently asked to write live in interviews)
Concise syntax to build a new list from an iterable, optionally filtered.

**Basic pattern:** `[expression for item in iterable]`
```python
prices = [10, 20, 30, 40, 50]
doubled_price = [price * 2 for price in prices]     # [20, 40, 60, 80, 100]

# equivalent to:
doubled_price = []
for i in prices:
    doubled_price.append(i * 2)
```

**With method calls:**
```python
names = ["ajay", "bijay", "sanjay"]
[name.capitalize() for name in names]     # ['Ajay', 'Bijay', 'Sanjay']
```

**Conditional list comprehension:** `[expr for item in iterable if condition]`
```python
email_address = ["aj@gmail.com", "sj@yahoo.com", "rj@yahoo.com", "mj@gmail.com"]
[email for email in email_address if email.endswith("@yahoo.com")]
# ['sj@yahoo.com', 'rj@yahoo.com']
```

**Nested list comprehension** (equivalent to nested `for` loops):
```python
[[x, y] for x in [1, 2, 3] for y in [4, 5, 6]]
# [[1, 4], [1, 5], [1, 6], [2, 4], [2, 5], [2, 6], [3, 4], [3, 5], [3, 6]]
```

### 3.10 Lists as Stack and Queue (★ classic DSA interview topic)

**Stack (LIFO — Last In, First Out):** use `.append()` to push, `.pop()` to pop — both O(1) at the end of a list.
```python
stack_of_plates = []
stack_of_plates.append("plate1")
stack_of_plates.append("plate2")
stack_of_plates.pop()      # removes & returns 'plate2' (the most recently added)
```

**Queue (FIFO — First In, First Out):** using a plain list with `.pop(0)` is **inefficient (O(n))** because all remaining elements must shift. The recommended structure is `collections.deque`, which gives O(1) append/pop from both ends.
```python
from collections import deque
checkout = deque()
checkout.append("cus1")
checkout.append("cus2")
# ...
while checkout:
    customer = checkout.popleft()   # removes from the FRONT — O(1)
    print("serving", customer)
```

Python also offers `queue.Queue` — a thread-safe FIFO queue, commonly used in producer-consumer / multithreading scenarios:
```python
from queue import Queue
print_queue = Queue()
print_queue.put("job1")      # push  (like .append)
print_queue.get()            # pop from front (like .popleft)
```
> **Interview tip:** `list.pop(0)` is O(n) — avoid it for queue-like behavior at scale. `deque.popleft()` is O(1). This is a very common "why not use a list as a queue" question.

### 3.11 List — Common Interview Questions
- **Difference between list and tuple?** → Mutability; tuples are also slightly faster and hashable (can be dict keys / set elements), lists cannot.
- **Difference between `.remove()`, `.pop()`, `.del`, `.clear()`?** → See table in §3.8.
- **Difference between `append()` and `extend()`?** → See §3.3.
- **How to remove duplicates from a list?** → `list(set(my_list))` (loses order, unless you use `dict.fromkeys(my_list)` to preserve order).
- **Time complexity of common list operations:**

| Operation | Time Complexity |
|---|---|
| Indexing `lst[i]` | O(1) |
| Append `lst.append()` | O(1) amortized |
| Insert `lst.insert(i, x)` | O(n) |
| Delete/pop from end `lst.pop()` | O(1) |
| Delete/pop from front `lst.pop(0)` | O(n) |
| Search `x in lst` | O(n) |
| Sort `lst.sort()` | O(n log n) |

---

## 4. Tuples and Sets — In Depth

### 4.1 Tuples
An **ordered, heterogeneous, immutable** collection.
```python
t = ("pwskills", "Ajay", 2, 2.3, True, 3+5j)
t[0]          # 'pwskills'
t[0] = 100    # TypeError: 'tuple' object does not support item assignment
```
**Use case:** any data that should stay constant and unmodifiable — e.g., employee ID, coordinates, function return values that shouldn't be altered.

**Common tuple operations:**
```python
tuple1 = ("pwskills", 1, 1, 1, 2, 2, "pwskills", 2.2)
tuple1.count(1)          # 3
tuple1.index("pwskills") # 0  (first occurrence)
tuple1 * 2                # repeats the tuple
tuple1[0:4]                # slicing works, same rules as lists
tuple1[::-1]                # reversed tuple
```
`max()` / `min()` / `len()` / `in` / `not in` all work on tuples just like lists:
```python
t1 = (1, 2, 100, 0, 200)
max(t1)     # 200
min(t1)     # 0
```
**Concatenation** combines two tuples into a new tuple (tuples themselves remain immutable, but you can create a brand-new tuple from two):
```python
t1 + t2     # (1, 2, 100, 0, 200, 101, 201)
```
**Nested tuples:**
```python
t3 = (t1, t2)     # tuple containing two tuples
```
`del` removes the tuple variable entirely (tuples have no `.remove()`/`.pop()` since they're immutable).

> **Interview tip — why use a tuple over a list?**
> 1. Immutability = data integrity/safety (accidental modification prevented).
> 2. Tuples are **hashable** (if all elements are hashable) → can be used as dictionary keys or set elements; lists cannot.
> 3. Slightly more memory-efficient and faster to iterate than lists.

### 4.2 Sets
An **unordered collection of unique elements** — automatically eliminates duplicates, does **not** support indexing.

```python
s = {1, 2, 1, 2, 2, "Ajay", "Ajay", "ajay"}
s      # {1, 2, 'Ajay', 'ajay'}   -- duplicates removed, case-sensitive so 'Ajay' != 'ajay'

s[0]   # TypeError: 'set' object is not subscriptable
```
> **Gotcha:** `{}` creates an **empty dict**, not an empty set! An empty set must be created with `set()`.
```python
s = {}
type(s)     # dict  -- NOT set!
s = set()
type(s)     # set
```

**Converting list ↔ set (common de-duplication use case):**
```python
list1 = [1, 2, 3, "Brinjal", "Brinjal", "Apple", "Apple"]
s = set(list1)          # {1, 2, 3, 'Apple', 'Brinjal'}
list1 = list(s)          # back to a list, duplicates gone
```

### 4.3 Hashability Rule (★ frequently tested)
Only **hashable (immutable)** objects can be elements of a set (or keys of a dict). Mutable objects like `list`, `set`, and `dict` are **unhashable** and raise `TypeError`.
```python
{1, 2, 3, [4, 5, 6]}      # TypeError: unhashable type: 'list'
{1, 2, 3, {4, 5, 6}}      # TypeError: unhashable type: 'set'
{1, 2, 3, (4, 5, 6)}      # ✅ works — tuple is immutable/hashable
```

### 4.4 Set Mutability & Methods

| Method | Behavior |
|---|---|
| `.add(x)` | Adds a single element |
| `.update(iterable)` | Adds **each element** of an iterable (like `extend` for lists) |
| `.remove(x)` | Removes `x`; **raises `KeyError`** if not present |
| `.discard(x)` | Removes `x`; **does NOT error** if not present (safer than `.remove()`) |
| `.pop()` | Removes and returns an **arbitrary** element (sets are unordered, so there's no "last" element) |
| `.clear()` | Empties the set |

```python
s.add(100)
s.update([2, 3])          # adds each element from the list individually
s.remove(3)                # KeyError if 3 not in s
s.discard(110)              # no error even though 110 isn't present
s.pop()                     # removes some arbitrary element, not necessarily the "first" or "last"
```
> **Interview trap:** `s.update("Ajay")` treats the string as an **iterable of characters** and adds each letter individually (`'A', 'j', 'a', 'y'`) — not the whole string as one element. Use `s.update(["Ajay"])` to add the whole string as a single element.

### 4.5 Set Operations (Mathematical Set Theory — ★ very common interview topic)

| Operator | Method equivalent | Meaning | Example Result |
|---|---|---|---|
| `\|` | `.union()` | All elements from both sets (no duplicates) | `s1 \| s2` |
| `&` | `.intersection()` | Only elements common to both sets | `s1 & s2` |
| `-` | `.difference()` | In first set but NOT in second | `s1 - s2` |
| `^` | `.symmetric_difference()` | In either set but NOT in both | `s1 ^ s2` |

```python
s1 = {"hiking", "reading", "coding"}
s2 = {"coding", "photography", "travelling"}

s1 | s2   # {'coding', 'hiking', 'photography', 'reading', 'travelling'}  -- union
s1 & s2   # {'coding'}                                                     -- intersection
s1 - s2   # {'hiking', 'reading'}                                          -- difference
s1 ^ s2   # {'hiking', 'photography', 'reading', 'travelling'}             -- symmetric difference
```

### 4.6 Frozenset
An **immutable version of a set** — supports all read operations (membership, union, intersection, etc.) but no `.add()`/`.remove()`/`.pop()`.
```python
my_fs = frozenset([1, 2, 2, 2, 3, 3, 4, 5])   # frozenset({1, 2, 3, 4, 5})
my_fs.add(5)      # AttributeError: 'frozenset' object has no attribute 'add'
```
> **Use case:** since frozensets are hashable, they can be used as dictionary keys or nested inside other sets — regular sets cannot.

### 4.7 Tuples & Sets — Common Interview Questions
- **Why are tuples faster than lists?** → Fixed size and immutability let Python optimize memory allocation and access.
- **Can a tuple be used as a dictionary key?** → Yes, if all its elements are themselves hashable/immutable.
- **Difference between `remove()` and `discard()` in sets?** → `remove()` raises `KeyError` on missing element; `discard()` silently does nothing.
- **How to remove duplicates while preserving order?** → `set()` does NOT preserve order; use `list(dict.fromkeys(my_list))` instead.
- **Time complexity of set operations** — average case:

| Operation | Time Complexity (avg) |
|---|---|
| `x in s` (membership test) | O(1) |
| `.add()` / `.remove()` | O(1) |
| Union / Intersection / Difference | O(len(s1) + len(s2)) |

> Compare this to `x in list` which is **O(n)** — a very common reason interviewers ask "why use a set over a list for membership checks."

---

## 5. Dictionaries — In Depth

A dictionary stores data as **key-value pairs**. Keys must be **unique and hashable (immutable)**; values can be anything (including mutable types).

> **Note on ordering:** Dictionaries are insertion-ordered as a guaranteed language feature since Python 3.7 (it was a CPython implementation detail in 3.6). Iterating a dict returns items in the order they were added.

### 5.1 Creation & Access
```python
d = {"name": "Ajay", "email": "aj@gmail.com", "contact": 1234}
d["name"]          # 'Ajay'  -- access by key
d[1]                # KeyError -- you can only access values by KEY, not by position/value
```

### 5.2 Valid Dictionary Keys (★ frequently tested rule)
**Keys must be hashable (immutable).** Numbers, strings, booleans, and tuples (of hashable elements) are all valid keys. **Lists, sets, and dicts are NOT valid keys** (unhashable/mutable).
```python
d = {1: "abc"}            # int key ✅
d = {1.2: "abc"}          # float key ✅
d = {True: "any value"}   # bool key ✅ (True behaves as 1 internally)
d = {(1, 2, 3): "abc"}    # tuple key ✅ (immutable)

d = {[1, 2, 3]: "shyam"}  # TypeError: unhashable type: 'list'
d = {{1, 2, 3}: "Ajay"}   # TypeError: unhashable type: 'set'
d = {{"key": 123}: "ajay"} # TypeError: unhashable type: 'dict'
```
> **Rule of thumb for interviews:** only **strings, numbers, booleans, and tuples of hashable items** can be dictionary keys.

### 5.3 Duplicate Keys
If a dictionary literal repeats a key, **the last value wins** — earlier duplicate entries are silently discarded:
```python
d = {"name": "Ajay", "email": "aj@gmail.com", "contact": 1234, "name": "Bijay"}
d["name"]      # 'Bijay'
```

### 5.4 Values Can Be Any Type (including nested structures)
```python
d = {'name': ['Sanjay', "Ajay", 1, 2], 'email': 'aj@gmail.com', 'contact': (1234, 678)}
d['name'][1]     # 'Ajay'  -- chained indexing into a list value
```

### 5.5 Modifying a Dictionary
```python
d['name'] = "Bikay"          # update existing key's value
d["address"] = "Bangalore"   # creates a NEW key-value pair if key doesn't exist
del d["address"]              # deletes a specific key-value pair
del d                         # deletes the ENTIRE dictionary variable
d.clear()                     # empties the dict, but the variable still exists as {}
```

### 5.6 Common Dictionary Methods

| Method | Purpose |
|---|---|
| `.keys()` | Returns a view of all keys |
| `.values()` | Returns a view of all values |
| `.items()` | Returns a view of `(key, value)` tuples |
| `.get(key)` | Returns value for `key`; returns `None` (or a default) instead of raising `KeyError` if missing |
| `.copy()` | Returns a shallow copy of the dict |
| `.pop(key)` | Removes `key` and **returns its value** |
| `.update(other_dict)` | Merges another dict into this one (overwrites matching keys) |
| `.fromkeys(keys, value)` | Creates a new dict from an iterable of keys, all sharing the same value |

```python
d.get("name")            # safer than d["name"] -- no KeyError if key missing
d.pop("contact")          # removes 'contact' key, returns its value: (1234, 678)

d1 = {"course": "ds"}
d.update(d1)               # merges d1 into d

d.fromkeys((1, 2, 3), ("a", "b", "c"))
# {1: ('a', 'b', 'c'), 2: ('a', 'b', 'c'), 3: ('a', 'b', 'c')}
```
> **Interview tip:** `d["key"]` raises `KeyError` if the key doesn't exist; `d.get("key")` returns `None` (or a specified default) instead — always prefer `.get()` when the key's presence isn't guaranteed.

### 5.7 Dictionary Comprehension (★ commonly asked to write live)
**Basic pattern:** `{key_expr: value_expr for item in iterable}`

Using `zip()` to pair two lists into a dict:
```python
students = ["Arun", "Ajay", "Bijay"]
marks = [80, 95, 100]

{student: mark for student, mark in zip(students, marks)}
# {'Arun': 80, 'Ajay': 95, 'Bijay': 100}
```
Equivalent manual loop version:
```python
students_marks = {}
for student, mark in zip(students, marks):
    students_marks[student] = mark
```

### 5.8 Nested Dictionaries
A very common real-world pattern — dictionary of dictionaries:
```python
employee_records = {
    'E001': {'name': 'John Doe', 'department': 'Engineering', 'salary': 75000},
    'E002': {'name': 'Jane Smith', 'department': 'Marketing', 'salary': 65000},
}
employee_records["E001"]              # returns the inner dict
employee_records.get("E001")          # same, but safer if key might not exist
```

### 5.9 Dictionary View Objects
`.keys()`, `.values()`, and `.items()` return **view objects** — these are dynamic "windows" into the dict (they update automatically if the dict changes), not static copies.
```python
restaurant_menu.items()
# dict_items([('Dish1', {...}), ('Dish2', {...}), ('Dish3', {...})])
```

### 5.10 Dictionary — Common Interview Questions
- **Why must dictionary keys be immutable?** → Keys are hashed to determine storage location; a mutable key could change its hash after insertion, breaking the internal hash table and making the entry unfindable.
- **Difference between `d[key]` and `d.get(key)`?** → See §5.6 — `KeyError` vs `None`/default.
- **How to merge two dictionaries?** → `d1.update(d2)` (in place) or `{**d1, **d2}` (returns new dict, Python 3.5+) or `d1 | d2` (Python 3.9+).
- **How to iterate over key-value pairs?** → `for key, value in d.items():`
- **Time complexity of dict operations:**

| Operation | Time Complexity (avg) |
|---|---|
| Access `d[key]` | O(1) |
| Insert / Update | O(1) |
| Delete | O(1) |
| Search by key (`key in d`) | O(1) |
| Search by value | O(n) — requires scanning all values |

> Dictionaries (and sets) use **hash tables** internally — this is why key-based lookup is O(1) on average, versus O(n) for a list's linear search. This is one of the single most common "why choose X over Y" interview questions in Python.

---

## 6. Master Comparison Table — Interview Cheat Sheet

| Feature | `list` | `tuple` | `set` | `dict` | `str` |
|---|---|---|---|---|---|
| Mutable | ✅ | ❌ | ✅ | ✅ (values) | ❌ |
| Ordered | ✅ | ✅ | ❌ | ✅ (3.7+) | ✅ |
| Allows duplicates | ✅ | ✅ | ❌ | Keys: ❌ / Values: ✅ | ✅ |
| Indexed access | ✅ | ✅ | ❌ | By key only | ✅ |
| Hashable (usable as dict key/set element) | ❌ | ✅ (if elements are hashable) | ❌ | ❌ | ✅ |
| Underlying implementation | Dynamic array | Fixed-size array | Hash table | Hash table | Immutable char array |
| Typical use case | General-purpose, ordered, changeable data | Fixed records, function returns, dict keys | Uniqueness, fast membership tests, set algebra | Fast key-based lookup, structured records | Textual data |

---

## 7. High-Frequency Interview Questions (Cross-Topic)

1. **What's the difference between `is` and `==`?** → `==` checks value equality; `is` checks identity (same object in memory). (Ties back to Operators notes — relevant across all these structures.)
2. **What is a shallow copy vs a deep copy?** → Shallow copy (`.copy()`, `[:]`) duplicates the outer container only; nested mutable objects remain shared. Deep copy (`copy.deepcopy()`) recursively duplicates everything.
3. **Why is a list unhashable but a tuple is hashable?** → Hashability requires immutability; a list's contents can change after creation (changing its "identity" for hashing purposes), a tuple's cannot (as long as its elements are also immutable).
4. **When would you choose a set over a list?** → When you need uniqueness and/or fast O(1) membership testing, and don't care about order.
5. **When would you choose a dict over a list of tuples?** → When you need fast O(1) lookup by a unique key, rather than scanning linearly.
6. **How does Python's dict achieve O(1) lookup?** → Internally implemented as a hash table: the key is hashed to compute a bucket/slot index directly, avoiding the need to scan all entries.
7. **What happens if you use a mutable default argument (like a list) in a function?** → It's created once and shared across all calls (a classic Python gotcha, closely related to mutability concepts covered here) — not something this notebook covers directly, but commonly asked alongside these topics.
8. **Explain string immutability's benefit.** → Enables safe hashing (dict keys/set elements), string interning for memory efficiency, and thread-safety.
9. **List comprehension vs generator expression?** → List comprehension builds the entire list in memory immediately (`[x for x in y]`); a generator expression (`(x for x in y)`) is lazy and produces items one at a time — more memory-efficient for large datasets. (Not covered in the source notebooks but a very likely follow-up question.)
10. **`.append()` vs `.extend()` vs `+`?** → See §3.3 — single element vs unpacked iterable vs new concatenated list.


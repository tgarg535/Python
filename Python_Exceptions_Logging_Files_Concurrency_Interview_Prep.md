# Python Exceptions, Logging, File Handling & Concurrency — Interview Prep Notes

Consolidated from: Exception Handling, Exception Best Practices, Logging & Debugging, File Handling Basics, File Reading/Writing (text/CSV/JSON/binary/buffered), Multithreading, Multiprocessing.

---

## 1. Exception Handling

### What is an Exception?
- An **exception** is an event that occurs during program execution that disrupts the normal flow of code (e.g., `ZeroDivisionError`, `TypeError`).
- Once an unhandled exception is raised, **all code after it in that block stops executing** — e.g., `print(6/0)` followed by more `print()` statements means those later statements never run.

### try / except / else / finally
```python
try:
    # suspicious code
except SomeException as e:
    # runs only if that specific exception occurs
else:
    # runs only if try block completed with NO exception
finally:
    # ALWAYS runs, regardless of whether an exception occurred or not
```
- **`try`**: wraps code that might fail.
- **`except`**: catches and handles a raised exception; without it, the program crashes with a traceback.
- **`else`**: executes only if the `try` block succeeds with no exception — useful for code that should only run on success (e.g., closing a file only if it was opened/written successfully).
- **`finally`**: executes **no matter what** (exception or not, `else` executed or not) — used for guaranteed cleanup (closing files/connections/resources), similar to just placing code after the try/except but guaranteed to run even if the exception propagates further up.
- Key distinction: `else` runs only on the "happy path" (no exception); `finally` runs unconditionally, always, even after an exception occurred and was caught (or not caught).

### Common Built-in Exceptions
| Exception | Triggered by |
|---|---|
| `ZeroDivisionError` | Division by zero |
| `TypeError` | Operation on incompatible types (e.g., dividing a number by a string) |
| `ValueError` | Correct type but invalid value (e.g., `int("pwskills")`) |
| `ModuleNotFoundError` | Importing a module that doesn't exist |
| `NameError` | Using a variable/function that isn't defined |
| `KeyError` | Accessing a dict key that doesn't exist |
| `IndexError` | Accessing a list index out of range |
| `FileNotFoundError` | Opening a non-existent file in read mode |

### Handling Multiple Exceptions
```python
# Option 1: group into one except (same handling logic)
try:
    10/"3"
except (ZeroDivisionError, TypeError) as e:
    print("Division not possible:", e)

# Option 2: separate blocks (different handling logic per exception)
try:
    10/0
except ZeroDivisionError as e:
    print("Zero division:", e)
except TypeError as e:
    print("Type error:", e)
```
- **Order matters**: put the **most specific** exceptions first, and the **generic `Exception`** class last. Since `Exception` is the parent class of nearly all built-in exceptions, if placed first it will swallow everything before more specific handlers ever get a chance to run.
```python
try:
    f = open("example.txt", "r")
    f.read()
except FileNotFoundError as e:      # specific — checked first
    print("File not found:", e)
except Exception as e:              # generic catch-all — checked last
    print("Some other error:", e)
```

### Custom Exceptions
- Create by subclassing the built-in `Exception` class, then `raise` your custom exception when a business rule is violated.
```python
class ValidateSalary(Exception):
    def __init__(self, msg):
        self.msg = msg

def validate_salary(salary):
    if salary <= 0:
        raise ValidateSalary("Negative salary is not possible")
    elif salary >= 30000000:
        raise ValidateSalary("Salary is unexpected")
    else:
        print("Salary is valid")

try:
    validate_salary(-500)
except ValidateSalary as e:
    print(e)
```

### Best Practices (frequently asked as "how do you handle exceptions properly?")
1. **Catch specific exceptions**, not just a bare/generic `except:` — this way you know exactly what's expected, and anything unexpected still surfaces clearly rather than being silently masked.
2. **Order exception blocks from most specific to most generic** (`Exception` last) — otherwise the generic block intercepts everything first.
3. **Don't handle irrelevant exceptions** — e.g., don't catch `FileNotFoundError` inside a division function; it adds unnecessary complexity and hides real bugs.
4. **Always log errors** (using the `logging` module) instead of relying only on `print()`, especially in production code — logs persist and can be filtered/searched, unlike console output.
5. **Document functions properly** (docstrings describing purpose, `Args`, expected exceptions) so other developers understand what can go wrong.
6. **Clean up resources in `finally`** (e.g., `f.close()`) to guarantee resources are released even if an exception occurs mid-operation.
7. Wrap exception-prone logic inside reusable **functions** so handling logic isn't duplicated everywhere.

### Interview Q&A
**Q: What's the difference between `else` and `finally` in a try/except block?**
A: `else` executes only if no exception occurred in `try`; `finally` executes unconditionally — whether or not an exception was raised or caught.

**Q: Why is it bad practice to use a bare `except:` clause?**
A: It catches **every** exception (including ones you didn't anticipate, and even things like `KeyboardInterrupt`), hiding bugs and making debugging harder. Specific exception classes make intent clear and let unexpected errors surface.

**Q: How do you create a custom exception in Python?**
A: Subclass `Exception` (directly or indirectly), optionally override `__init__` to store a custom message, then `raise CustomException("message")` when the condition is violated.

**Q: What is the exception hierarchy relevance when ordering except blocks?**
A: `Exception` is the base class most specific exceptions (like `ValueError`, `KeyError`) inherit from. If you list `except Exception` before a more specific one, the specific block becomes unreachable (dead code) since the generic one will always match first.

**Q: What happens to code after an unhandled exception?**
A: Execution stops immediately at that point and the program terminates (or the exception propagates up the call stack) — none of the remaining code in that scope runs.

---

## 2. Logging & Debugging

### Debugging
- **Debugging**: the process of identifying and fixing bugs by setting checkpoints, executing code, and inspecting variable values.
- Simple approach: sprinkle `print()` statements to see variable values at each stage (e.g., printing `a, b` inside a function to find which inputs caused a `ZeroDivisionError`).
- Limitation of print-based debugging: not scalable for complex/production scripts — print output isn't persisted, isn't leveled by severity, and clutters console output. This motivates the use of the **`logging`** module.

### Logging
- **Logging** records the state and flow of a program over time — useful for monitoring, understanding behavior, and debugging without stopping execution or relying on ephemeral print statements. (Analogy: like keeping a diary — reviewing entries over time reveals how something evolved.)
- Logging supports **severity levels**, letting you categorize messages (analogous to categorizing emergencies by severity).

### Logging Levels (lowest → highest severity)
| Level | Method | Typical Use |
|---|---|---|
| DEBUG | `logging.debug()` | Detailed diagnostic info, useful only while developing |
| INFO | `logging.info()` | Confirmation that things are working as expected |
| WARNING | `logging.warning()` | Something unexpected happened, but program still works |
| ERROR | `logging.error()` | A more serious problem — some functionality failed |
| CRITICAL | `logging.critical()` | A very serious error — program may be unable to continue |

- Setting `level=logging.DEBUG` in config captures **all** levels (DEBUG and above); setting `level=logging.WARNING` captures only WARNING, ERROR, CRITICAL (lower-severity messages like INFO/DEBUG are suppressed).

### Basic Configuration
```python
import logging
logging.basicConfig(
    filename="test.log",
    level=logging.DEBUG,
    format='%(asctime)s %(levelname)s %(message)s'
)

logging.debug("This msg is for debugging")
logging.info("This is my info msg")
logging.warning("This is my warning msg")
logging.error("Some error has happened")
logging.critical("The software has stopped running")
logging.shutdown()  # flushes and closes log handlers
```
- `format` string customizes what appears in each log line: `%(asctime)s` = timestamp, `%(levelname)s` = severity level name, `%(message)s` = the actual message.
- Logging can be sprinkled inside loops/functions to trace exactly which element/iteration caused an issue (e.g., logging each element while separating integers and strings from a nested list) — far more useful than one-off print statements for tracing execution history after the fact.
- Best practice: log inside `except` blocks (`logging.error(f"My file was not found {e}")`) instead of, or in addition to, printing — this creates a persistent audit trail of failures.

### Interview Q&A
**Q: What is the difference between debugging with `print()` and using the `logging` module?**
A: `print()` output isn't persisted, isn't categorized by severity, and is generally not practical for large/production codebases. `logging` persists messages to files, supports severity levels (so you can filter noise), and includes contextual metadata like timestamps.

**Q: Name the five standard logging levels in increasing order of severity.**
A: DEBUG < INFO < WARNING < ERROR < CRITICAL.

**Q: If you set `level=logging.WARNING`, will `logging.info()` calls be recorded?**
A: No — only messages at the configured level or higher are recorded; INFO is lower severity than WARNING, so it's suppressed.

**Q: Why prefer logging over print statements in production code?**
A: Logging is persistent (written to files), timestamped, filterable by severity, and doesn't clutter/get lost in console output — essential for diagnosing issues after the fact in real systems.

---

## 3. File Handling

### Modes
| Mode | Meaning |
|---|---|
| `r` | Read (file must exist, else `FileNotFoundError`) |
| `w` | Write (creates file if absent; **truncates/overwrites** existing content) |
| `a` | Append (adds to end of file without deleting existing content) |
| `r+` | Read and write |
| `wb` / `rb` | Write/Read in **binary** mode |

- Opening in `'r'` mode on a non-existent file immediately raises `FileNotFoundError`.
- Opening in `'w'` mode **always truncates** the file first — even if you just want to write, all previous content is lost. Use `'a'` if you want to preserve existing content and add more.
- Writing to a file doesn't make content visible/persisted until the file is **closed** (or flushed) — `file.write(...)` followed immediately by inspecting the file externally may show nothing until `file.close()`.
- Attempting to write to a file opened in read mode, or opened in write mode after it's already been closed, raises an error.

### File Object Attributes/Methods
- `file.closed` → boolean, whether the file handle has been closed.
- `file.name`, `file.mode` → metadata about the opened file.
- `file.tell()` → current cursor position (byte offset).
- `file.seek(0)` → move cursor back to start of file (needed to re-read from the beginning after a prior read exhausted the cursor).
- `file.read()` → reads whole remaining content from current cursor position (returns empty string if cursor is already at end).
- `file.readline()` → reads only the next single line.
- `file.readlines()` → returns a **list**, each element being one line.
- Iterating directly over a file object (`for line in file:`) yields lines one at a time — memory-efficient for large files vs. `read()` which loads everything at once.

### The `with` Statement
```python
with open("file.txt", "w") as f:
    f.write("This is my first line")
```
- Ensures the file is **automatically closed** once the block exits — even if an exception occurs inside the block. Prevents resource leaks, data corruption, or file locks caused by forgetting to call `.close()` manually. This is the **recommended, idiomatic** way to handle files in Python (preferred over manual `open()`/`close()` pairs).

### CSV Files
```python
import csv
data = [["Name", "Course", "Fee"], ["Ajay", "DS", "20000"]]

with open("example_csv.csv", "w") as f:
    w = csv.writer(f)
    for row in data:
        w.writerow(row)

with open("example_csv.csv", "r") as f:
    r = csv.reader(f)
    for row in r:
        print(row)   # each row returned as a list of strings
```

### JSON Files
```python
import json
data = {"name": "Ajay", "course": "DS", "fee": 2000}

with open("file_json.json", "w") as f:
    json.dump(data, f)      # write a Python dict as JSON

with open("file_json.json", "r") as f:
    loaded = json.load(f)   # read JSON back into a Python dict
```
- `json.dump`/`json.load` work with file objects; `json.dumps`/`json.loads` (not shown, but standard) work with in-memory strings.

### Binary Files
```python
with open("test_bin.bin", "wb") as f:
    f.write(b"\x48\x65\x6c\x6c\x6f")   # raw bytes

with open("test_bin.bin", "rb") as f:
    print(f.read())   # returns a bytes object, e.g. b'Hello'
```
- Binary mode (`wb`/`rb`) is required for non-text data (images, executables, arbitrary byte sequences) — text mode would attempt encoding/decoding that corrupts raw bytes.

### Buffered Reading/Writing (`io` module)
- **Buffering** lets a program read/write data in **chunks** rather than all at once, improving performance/efficiency for large files.
```python
import io
with open("test_buf.txt", "wb") as f:
    buffered = io.BufferedWriter(f)
    buffered.write(b"large amount of byte data...")
    buffered.flush()   # force-write buffered data to disk

with open("test_buf.txt", "rb") as f:
    buffered = io.BufferedReader(f)
    chunk = buffered.read(150)   # read only the first 150 bytes
```
- `flush()` forces any data sitting in the buffer to actually be written out immediately, rather than waiting for the buffer to fill or the file to close.

### OS & Shutil Operations
| Operation | Function |
|---|---|
| Get current directory | `os.getcwd()` |
| List directory contents | `os.listdir()` |
| Make one directory | `os.mkdir("test")` (fails if parent path doesn't exist) |
| Make nested directories | `os.makedirs("1/2/3/4")` (creates all intermediate dirs) |
| Remove an **empty** directory | `os.rmdir("test")` (fails if directory has contents) |
| Remove a directory **and all contents** | `shutil.rmtree("test")` |
| Get file size | `os.path.getsize("file.txt")` |
| Copy a file | `shutil.copy(src, dest)` (keeps original) |
| Move a file | `shutil.move(src, dest)` (removes from original location) |

### Interview Q&A
**Q: Difference between `'w'` and `'a'` mode?**
A: `'w'` truncates (erases) existing content before writing; `'a'` appends new content to the end, preserving what's already there.

**Q: Why use the `with` statement instead of manually calling `open()`/`close()`?**
A: `with` guarantees the file is closed automatically when the block ends, even if an exception occurs — avoiding resource leaks or file corruption from a forgotten `close()`.

**Q: What's the difference between `read()`, `readline()`, and `readlines()`?**
A: `read()` returns the entire remaining content as one string; `readline()` returns just the next single line; `readlines()` returns a list where each element is one line.

**Q: What does `file.seek(0)` do and when would you need it?**
A: Moves the file cursor back to the beginning. Needed when you've already read through a file (cursor now at the end) and want to re-read it from the start without reopening it.

**Q: Difference between `os.rmdir()` and `shutil.rmtree()`?**
A: `os.rmdir()` only removes an **empty** directory (errors if it has contents); `shutil.rmtree()` recursively deletes a directory and everything inside it.

**Q: Why is binary mode (`'rb'`/`'wb'`) necessary for some files?**
A: Text mode applies encoding/decoding (e.g., converting bytes to str using a character encoding) which corrupts non-text data like images or arbitrary byte sequences; binary mode reads/writes raw bytes unmodified.

---

## 4. Multithreading

### Core Concepts
- A **process** is an instance of a running program.
- **Multiprocessing**: multiple processes run in **parallel** across multiple CPU cores/processors.
- **Multithreading**: multiple threads run **concurrently** within a single process (can share the same process's memory space), often on a single core, by interleaving execution — especially useful when tasks spend time **waiting** (I/O).

### When to Use Multithreading
- Best suited for **I/O-bound tasks** — where the program spends time waiting on external operations rather than doing CPU computation. Examples: reading/writing files, network requests, database queries, downloading URLs.
- Because of the **GIL (Global Interpreter Lock)** in CPython, threads don't achieve true CPU-parallel execution for compute-heavy work — but I/O waits release the GIL, so multiple threads can overlap their "waiting" time effectively.

### Sequential vs Threaded Execution
```python
import time, threading

def test_func():
    print("do something")
    time.sleep(1)
    print("done with sleeping")

# Sequential: calling test_func() 5 times takes ~5 seconds (one after another)

# Threaded: running via threads lets the sleeps overlap
t1 = threading.Thread(target=test_func)
t2 = threading.Thread(target=test_func)
t1.start()
t2.start()
t1.join()   # wait for t1 to finish
t2.join()   # wait for t2 to finish
# total time ≈ 1 second, not 2 — because both threads sleep concurrently
```
- `.start()` begins a thread's execution; `.join()` blocks the **main thread** until that thread finishes. Calling `.join()` on all threads after starting them all (rather than starting+joining one at a time) is what actually achieves concurrency — starting all first lets them all run concurrently, then you wait for all of them together.
- Passing arguments to the target function: `threading.Thread(target=test_func, args=[2])` (or a tuple `args=(2,)`).

### Real Use Case — Concurrent Downloads
```python
import threading, urllib.request

def file_download(url, filename):
    urllib.request.urlretrieve(url, filename)

threads = []
for url, filename in zip(url_list, data_list):
    t = threading.Thread(target=file_download, args=(url, filename))
    t.start()
    threads.append(t)
for t in threads:
    t.join()
```
- Downloading multiple files sequentially would take the sum of each download's time; doing it via threads overlaps the network wait time, drastically reducing total wall-clock time — a textbook I/O-bound speedup case.

### `concurrent.futures` — Cleaner Threading API
```python
import concurrent.futures

with concurrent.futures.ThreadPoolExecutor() as executor:
    executor.map(file_download, url_list, data_list)
```
- `ThreadPoolExecutor` manages a pool of worker threads for you — no manual `.start()`/`.join()` bookkeeping; `executor.map()` applies the function across all argument pairs and the `with` block automatically waits for all tasks to finish (analogous to joining all threads) before exiting.

### Shared State & Race Conditions
- When multiple threads modify a **shared variable**, you risk race conditions (inconsistent/incorrect updates if two threads read-modify-write simultaneously).
- **`threading.Lock()`** ensures only one thread can execute the critical section (the code that modifies shared state) at a time.
```python
shared_counter = 0
counter_lock = threading.Lock()

def increment_shared_counter(x):
    global shared_counter
    with counter_lock:              # only one thread inside this block at a time
        shared_counter += 1
        print(f"Thread {x}: incremented to {shared_counter}")
        time.sleep(1)
```
- Because the lock serializes access to the critical section, this example (6 threads, each sleeping 1 sec while holding the lock) takes ~6 seconds total — the lock forces sequential execution of that specific block, trading some concurrency for correctness/consistency.

### Interview Q&A
**Q: What's the difference between multithreading and multiprocessing?**
A: Multithreading runs multiple threads within a single process (concurrently, often on one core, sharing memory); multiprocessing runs multiple independent processes (in true parallel across cores, each with separate memory space).

**Q: When should you prefer multithreading over multiprocessing?**
A: For I/O-bound tasks (file I/O, network calls, DB queries) — where most of the time is spent waiting rather than computing, and threads can overlap that waiting time cheaply.

**Q: What is the GIL and how does it affect multithreading in Python?**
A: The Global Interpreter Lock ensures only one thread executes Python bytecode at a time in CPython, which limits true parallel CPU-bound speedup via threads — but I/O operations release the GIL, so threads still help significantly for I/O-bound work.

**Q: Why use `threading.Lock()`?**
A: To prevent race conditions when multiple threads access/modify shared mutable state — the lock ensures only one thread executes the critical section at a time, keeping the shared variable consistent.

**Q: What does `.join()` do?**
A: Blocks the calling (typically main) thread until the target thread has finished execution — used to wait for background threads to complete before continuing.

---

## 5. Multiprocessing

### Core Concept
- Multiprocessing runs code across **multiple processes**, which can execute truly in **parallel** on multiple CPU cores — unlike threads, each process has its own independent memory space (no GIL bottleneck for CPU-bound work).
- Best suited for **CPU-bound / computation-heavy tasks** (unlike multithreading, which favors I/O-bound tasks).

### Basic Usage
```python
import multiprocessing, time

def test_func():
    print("do something")
    time.sleep(1)
    print("done with sleeping")

p1 = multiprocessing.Process(target=test_func)
p2 = multiprocessing.Process(target=test_func)
p1.start()
p2.start()
p1.join()
p2.join()
# total time ≈ 1 second instead of 2, because both run in parallel
```
- Same `.start()`/`.join()` pattern as threading, but each `Process` is a fully separate OS-level process, not a lightweight thread.
- Interleaved/garbled print output across processes (e.g., `"sleep for 1 secdo something"` merged text) is a visible symptom of **true parallel execution** — multiple processes writing to stdout simultaneously without any ordering guarantee.

### Sharing Data Across Processes
- Since processes **don't share memory** by default, ordinary shared variables don't work as in threading. Multiprocessing provides special shared data structures:
```python
arr = multiprocessing.Array('i', [1, 2, 5, 3, 40000000000])  # shared array of ints

def square(index, value):
    value[index] = value[index] ** 2

processes = []
for i in range(5):
    p = multiprocessing.Process(target=square, args=(i, arr))
    p.start()
    processes.append(p)
for p in processes:
    p.join()

print(list(arr))  # values squared, computed in parallel across processes
```
- `multiprocessing.Array` and `multiprocessing.Queue` are process-safe data structures designed specifically for **inter-process communication (IPC)**, since regular Python objects aren't automatically shared across separate process memory spaces.

### `multiprocessing.Pool`
```python
def square(no):
    print(f"The square of {no} is {no*no}")

with multiprocessing.Pool() as pool:
    pool.map(square, [1, 2, 3, 4, 6000])
```
- `Pool` manages a fixed group of worker processes and distributes tasks (`pool.map`) across them automatically — simpler than manually creating/joining individual `Process` objects.
- Output order is **not guaranteed** to match input order — a clear sign multiple cores are processing items in parallel, not sequentially.

### Producer-Consumer Pattern with `multiprocessing.Queue`
```python
def enroll_students(queue):
    for student in ["Rahul", "Rohit", "Aman", "Ajay"]:
        queue.put(f"enroll request for {student}")

def register_students(queue):
    while True:
        req = queue.get()
        if req is None:
            break
        print(f"Register the enrollment request: {req}")

student_queue = multiprocessing.Queue()
p1 = multiprocessing.Process(target=enroll_students, args=(student_queue,))
p2 = multiprocessing.Process(target=register_students, args=(student_queue,))
p1.start(); p2.start()
p1.join(); p2.join()
```
- Real-world use case: one process **produces** work items (enrollment requests) into a queue while another **consumes**/processes them concurrently — the two stages overlap in time rather than running strictly one after another.

### `concurrent.futures.ProcessPoolExecutor`
```python
import concurrent.futures

with concurrent.futures.ProcessPoolExecutor() as executor:
    executor.map(test_func, range(10))
```
- Same clean pooling interface as `ThreadPoolExecutor`, but backed by separate processes instead of threads — appropriate when the workload is CPU-bound.

### Interview Q&A
**Q: Why can't multiple processes share a regular Python variable the way threads can?**
A: Each process has its own separate memory space (no shared address space), unlike threads which share the memory of their parent process. You need explicit IPC mechanisms (`multiprocessing.Array`, `multiprocessing.Queue`, `Manager`, pipes) to share data across processes.

**Q: When would you choose multiprocessing over multithreading?**
A: For CPU-bound/computation-heavy tasks that need true parallel execution across multiple cores — the GIL prevents threads from achieving this for CPU-bound work, but separate processes bypass the GIL entirely since each has its own interpreter/memory space.

**Q: What is `multiprocessing.Pool` used for?**
A: It manages a fixed pool of worker processes and distributes a function across an iterable of inputs (`pool.map(func, iterable)`) automatically, without manually creating/starting/joining each `Process`.

**Q: Why might output from parallel processes appear interleaved/out of order?**
A: Because processes run truly concurrently on separate cores with no built-in ordering guarantee for things like `print()` — whichever process's I/O gets scheduled/flushed first appears first, and this genuinely varies run to run.

**Q: Describe a producer-consumer example using multiprocessing.**
A: One process pushes tasks into a `multiprocessing.Queue` (e.g., enrollment requests) while a second process concurrently pulls and processes them (e.g., registering students) — the queue coordinates work between the two independent processes running in parallel.

---

## Quick-Fire Conceptual Recap (rapid interview review)

- **try/except/else/finally**: `else` = runs only on success; `finally` = always runs (cleanup).
- **Exception ordering rule**: specific exceptions before generic `Exception`.
- **Custom exceptions**: subclass `Exception`, `raise` when a rule is violated.
- **Logging levels** (low→high): DEBUG < INFO < WARNING < ERROR < CRITICAL.
- **Logging vs print**: persistent, leveled, filterable, production-appropriate.
- **File modes**: `r` read / `w` write (truncates) / `a` append / `rb`,`wb` binary.
- **`with` statement**: guarantees file closure even on exceptions — always prefer it.
- **`read()` vs `readline()` vs `readlines()`**: whole content vs one line vs list-of-lines.
- **CSV/JSON**: `csv.writer/reader`; `json.dump/load` (files) vs `dumps/loads` (strings).
- **Buffering**: reads/writes data in chunks for performance; `flush()` forces write-through.
- **Multithreading**: concurrent, shared memory, best for I/O-bound tasks, limited by the GIL for CPU work.
- **Multiprocessing**: true parallelism across cores, separate memory (needs `Array`/`Queue` for IPC), best for CPU-bound tasks.
- **`Lock()`**: prevents race conditions on shared mutable state between threads.
- **`Pool` / `ThreadPoolExecutor` / `ProcessPoolExecutor`**: higher-level, cleaner APIs over manually managing threads/processes.

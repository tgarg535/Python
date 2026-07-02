# Python Functions & OOP — Interview Prep Notes

Consolidated from: Functions, Iterators & Generators, Lambda/Map/Reduce/Filter, OOP Basics, Inheritance & Abstraction, Polymorphism & Encapsulation, Dunder Methods, Decorators, Class/Static Methods, Property Decorators.

---

## 1. Functions

### Core Concepts
- A **function** is a reusable block of code that performs a specific task. Benefits: reusability, cleaner code, easier maintenance, avoids repetition.
- Types: **built-in** (`print`, `len`, `type`, `math.ceil`, `math.floor`) and **user-defined** (`def func():`).
- `return` sends a value back to the caller; without it, the function returns `None`. A function without `return` cannot be used in an expression (e.g., concatenated with a string) — doing so raises `TypeError`.
- A function can `return` **multiple values**; Python packs them into a **tuple**, which can be unpacked directly into variables (`a, b, c = func()`).

### Types of Arguments
| Type | Example | Notes |
|---|---|---|
| Positional | `def greet(name):` | Order matters |
| Default | `def sum1(a, b, c=0):` | Non-default args must come **before** default args in the definition |
| Keyword | `addition(b="skills", a="pw")` | Order doesn't matter when called by name |
| Variable-length positional | `*args` | Collects extra positional args into a **tuple** |
| Variable-length keyword | `**kwargs` | Collects extra keyword args into a **dict** |
| Combined | `def test1(*args, **kwargs):` | `*args` must precede `**kwargs` |

- `*args` and a required keyword-only arg can coexist: `def test(*args, a): ...` — but `a` **must** be passed as a keyword.

### Scope / Namespace
- **Local variable**: declared inside a function, accessible only inside it.
- **Global variable**: declared outside any function, accessible everywhere (including inside functions, read-only unless declared `global`).
- Trying to access a local variable outside its function raises `NameError`.

### Nested Functions
- A function can be defined inside another function; the inner function is only accessible within the outer function's scope (unless returned/exposed).

### Docstrings
- Triple-quoted string right after `def` describing purpose, `Args`, and `Returns`. Accessed via `func.__doc__` or `help(func)`.

### Interview Q&A
**Q: Difference between `*args` and `**kwargs`?**
A: `*args` collects unnamed positional arguments as a tuple; `**kwargs` collects named/keyword arguments as a dictionary. Order in signature: positional → `*args` → keyword-only → `**kwargs`.

**Q: Can a function return multiple values? How?**
A: Yes — Python returns them as a single tuple; you can unpack them into multiple variables on assignment.

**Q: What happens if you call a function without providing a required positional argument?**
A: `TypeError: missing required positional argument`.

**Q: Why use default arguments and what's the rule around ordering?**
A: They let you skip commonly unchanged arguments. Rule: parameters with defaults must appear after parameters without defaults in the function signature.

**Q: What is variable scope / namespace in Python?**
A: It defines where a variable is visible — local (inside a function) vs global (module-level). Local variables die once the function exits.

---

## 2. Iterators & Generators

### Iterable vs Iterator vs Iteration
- **Iterable**: an object capable of returning its elements one at a time (e.g., list, tuple, string, set, dict). It implements `__iter__`.
- **Iterator**: the object actually produced by `iter(iterable)`; it "remembers state" and produces the next value via `next()`. It implements both `__iter__` and `__next__`.
- **Iteration**: the process of looping through elements one by one (what a `for` loop does under the hood).
- Analogy used: an iterable is like a raw potato; converting it into an iterator (`iter()`) is like washing/chopping it — now it's "ready" to be consumed one piece at a time (iteration = the cooking).
- `for i in 5:` fails — plain integers are **not iterable**.
- Under the hood, a `for` loop calls `iter(obj)` once, then repeatedly calls `next()` until `StopIteration` is raised.
- Once an iterator is exhausted, calling `next()` again raises `StopIteration` — it cannot be reset/reused; you'd need to call `iter()` again on the original iterable.

### Generators
- A **generator function** uses `yield` instead of `return`. Calling it does **not** run the body — it returns a generator object (a special iterator) immediately.
- Each call to `next()` resumes execution right after the last `yield`, runs until the next `yield`, and pauses again — this is why generators are **memory efficient**: values are produced **lazily**, one at a time, instead of building the entire result list in memory up front.
- Classic example: Fibonacci generator can conceptually generate values for a huge `n` without blowing up memory, because only one value exists in memory at a time.
- Regular function (`return`) computes everything eagerly and returns a full list; generator (`yield`) computes on demand.

### Interview Q&A
**Q: Difference between iterable and iterator?**
A: An iterable can produce an iterator (via `__iter__`); an iterator actually keeps track of state and yields the next value via `__next__`. All iterators are iterables, not vice versa.

**Q: What is a generator and why is it memory-efficient?**
A: A function containing `yield`. It pauses/resumes state instead of computing and storing the full result set — values are generated one at a time, on demand (lazy evaluation).

**Q: What exception signals that an iterator is exhausted?**
A: `StopIteration`.

**Q: `return` vs `yield`?**
A: `return` ends function execution and sends back one final value; `yield` pauses the function, returns a value, and preserves local state to resume later on the next `next()` call.

**Q: Can you convert a generator into a list?**
A: Yes, `list(generator)` — but this defeats the memory-saving purpose since it forces full evaluation.

---

## 3. Lambda, Map, Reduce, Filter

### Lambda
- Anonymous, single-expression function: `lambda arguments: expression`. No `def`, no `return`, no name (though it can be assigned to a variable).
- Commonly used inline as the `key` argument to `sorted()`, or inside `map`/`filter`/`reduce`.
- Can be recursive if assigned to a name: `fact = lambda n: 1 if n==0 else n*fact(n-1)`.
- Limitation: body must be a single expression — no statements, loops, or multiple lines.

### map()
- `map(function, iterable, ...)` — applies `function` to every element of `iterable` and returns a **map object** (must wrap with `list()` to view). Can take multiple iterables (function must accept that many args).
- Works with named functions, lambdas, or built-in methods (`str.capitalize`, `str.upper`).

### reduce()
- From `functools`. `reduce(function, iterable)` — cumulatively applies a **two-argument** function to the iterable, "folding" it into a single value (e.g., running sum, running max, factorial).
- Common uses: sum, product, factorial, concatenating strings, finding max.
- `reduce` on an empty iterable with no initial value raises `TypeError`; with one element, it returns that element without calling the function.

### filter()
- `filter(function, iterable)` — keeps only elements where `function` returns `True`. Function must return a boolean (predicate).
- Common with lambdas for conditions like even/odd, positive/negative, string length.

### Interview Q&A
**Q: When would you use `map` vs a list comprehension?**
A: Functionally similar; `map` is often used when applying an existing/named function, while comprehensions are typically more readable for simple inline transformations. Both are common in interviews — know both.

**Q: Why does `reduce` need a two-argument function?**
A: Because it repeatedly combines an accumulated result with the next element — it needs exactly two inputs (accumulator, current item) at each step.

**Q: Can a lambda have default arguments or multiple statements?**
A: Default arguments — yes (`lambda x=5: x*2`). Multiple statements — no, only a single expression.

**Q: Difference between `map`, `filter`, and `reduce`?**
A: `map` transforms every element; `filter` selects a subset based on a condition; `reduce` folds all elements into a single accumulated value.

---

## 4. OOP Fundamentals

### Why OOP?
- Functional code scattered across many standalone functions becomes hard to manage as a project grows (e.g., separate backend/frontend/data-pipeline functions). OOP lets you **group related functions and data into classes**, making large codebases organized and maintainable by different teams/roles.

### Class & Object
- **Class**: blueprint/template (naming convention: `UpperCamelCase`).
- **Object/Instance**: a concrete realization of a class, created by calling the class like a function: `c1 = Car()`.
- `self` refers to the current instance — it lets each object's methods operate on **that object's own data**. It's not a reserved keyword (any name works), but `self` is the universal convention.
- A method without `self` as the first parameter throws an error when called on an instance, because Python automatically passes the instance as the first argument.

### Attributes
- Can be defined at class level (shared/fixed across instances unless reassigned per-instance) or inside `__init__` via `self.attr = value` (unique per instance).
- Class-level mutable defaults are a common trap: if an attribute is only set once at class level (not inside `__init__`), all instances that don't override it will share/reference the same fixed value.

### Interview Q&A
**Q: What's the difference between a class attribute and an instance attribute?**
A: A class attribute is shared across all instances (defined directly in the class body); an instance attribute is specific to one object (typically assigned via `self.x = x` inside `__init__`).

**Q: Why do instance methods need `self`?**
A: So Python knows which object's data/state the method should operate on. Python auto-passes the calling instance as the first argument.

**Q: Is `self` a keyword?**
A: No, it's just a convention. Any parameter name would work as the first parameter of an instance method.

---

## 5. Class Methods & Static Methods

| Feature | Instance method | Class method (`@classmethod`) | Static method (`@staticmethod`) |
|---|---|---|---|
| First parameter | `self` | `cls` | none |
| Bound to | instance | class | neither (behaves like a plain function namespaced inside the class) |
| Can access/modify instance state | Yes | No | No |
| Can access/modify class state | Indirectly | Yes | No |
| Call without creating an object? | No | Yes | Yes |

- **`@classmethod`**: takes `cls` as first arg, referring to the class itself. Commonly used as an **alternative/overloaded constructor** (a factory method) — e.g., `cls(name)` inside a classmethod is equivalent to calling `Student(name)`.
- Class methods can read/modify **class-level variables** that are shared across all instances (e.g., a running `total_students` counter incremented in `__init__` and exposed via a classmethod).
- You can attach an external function to a class dynamically: `Student.course_details = classmethod(external_func)`, and remove it with `del Student.course_details` or `delattr(Student, "name")`.
- **`@staticmethod`**: doesn't take `self` or `cls`; behaves like a regular function that's just logically grouped inside the class (e.g., a `Calculator` class with `add`/`subtract`). Doesn't read or modify any class/instance state.
- Rule of thumb: use `@classmethod` when you need to touch class-level data; use `@staticmethod` when the method is logically related to the class but doesn't need class or instance data at all.

### Interview Q&A
**Q: When would you choose a classmethod over `__init__` overloading?**
A: When you want multiple ways to construct an object (alternative constructors) — e.g., `from_string()`, `from_dict()` — since Python doesn't support true constructor overloading.

**Q: Can a static method access class variables?**
A: Not directly (no `cls` reference), though it could reference `ClassName.variable` explicitly if hardcoded — but that defeats the purpose.

**Q: Give a real interview-style example of `@classmethod` as factory.**
A:
```python
class Student:
    def __init__(self, name):
        self.name = name
    @classmethod
    def from_full_name(cls, full_name):
        first = full_name.split()[0]
        return cls(first)
```

---

## 6. Property Decorators (`@property`)

- Lets you access a **method as if it were an attribute** (no parentheses needed) — used to safely expose "private" (name-mangled, `__attr`) data.
- **Getter**: `@property` above a method — read-only access, e.g. `stud.access_price` instead of `stud.access_price()`.
- **Setter**: define another method with the **same name**, decorated `@access_price.setter` — enables `stud.access_price = 1500`. Note: if the setter method has a *different name* than the getter, it becomes its own separate settable attribute rather than truly linked to the property (a common gotcha shown in the notebook — best practice is to give getter/setter/deleter the **same method name**).
- **Deleter**: `@access_price.deleter` — enables `del stud.access_price`; after deletion, trying to access the property again raises `AttributeError` because the underlying private attribute no longer exists.
- Common real use case: validation in the setter (e.g., a `Circle.radius` setter that raises `ValueError` if the value is `<= 0`), keeping computed properties like `area()` consistent with validated data.

```python
class Circle:
    def __init__(self, radius):
        self.__radius = radius

    @property
    def radius(self):
        return self.__radius

    @radius.setter
    def radius(self, value):
        if value <= 0:
            raise ValueError("Radius is not positive")
        self.__radius = value

    @radius.deleter
    def radius(self):
        del self.__radius

    def area(self):
        return 3.14 * self.__radius ** 2
```

### Interview Q&A
**Q: Why use `@property` instead of a plain public attribute?**
A: It lets you add validation/logic on get/set while keeping the clean attribute-style syntax (`obj.value` instead of `obj.get_value()`), and lets you later add constraints without breaking existing code that used the attribute directly.

**Q: What happens if the setter method name doesn't match the getter/property name?**
A: It won't be linked as the setter for that property — it becomes an unrelated separate attribute, so `obj.property_name = x` won't actually invoke your intended validation logic.

**Q: How do you make an attribute read-only using `@property`?**
A: Define only the getter (`@property`) and no `@x.setter` — assignment attempts will raise `AttributeError: can't set attribute`.

---

## 7. Inheritance & Abstraction

### Inheritance
- A **derived/child class** inherits attributes/methods from a **base/parent class**: `class Son(Father):`.
- A child has access to both its own and its parent's members; the parent **cannot** access the child's members.
- **Method overriding**: redefining a parent's method in the child with the same name — the child's version takes precedence when called on a child instance (child class is "more powerful").

### Types of Inheritance
| Type | Structure |
|---|---|
| Single | One child ← one parent |
| Multi-level | Grandparent → Parent → Child (chain) |
| Multiple | One child ← two or more parents |
| Hierarchical | One parent → multiple children |
| Hybrid | Combination of the above |

- **Multiple inheritance**: `class ChildClass(ParentClass1, ParentClass2):` — child gets methods from both parents.
- **Diamond problem**: arises when a class inherits (directly/indirectly) from two classes that both derive from a common ancestor, creating ambiguity about which method to use. Python resolves this using **MRO (Method Resolution Order)** via the **C3 linearization algorithm** — broadly, the class listed **first** in the inheritance list takes priority (`class D(B, C)` → B's method wins if both define it and neither overrides further).
- Use `ClassName.__mro__` or `ClassName.mro()` to inspect resolution order in practice.

### Abstraction
- **Abstraction** hides implementation details and exposes only the necessary interface (e.g., you interact with an app's UI without seeing backend code).
- Achieved in Python via the `abc` module (Abstract Base Classes) and `@abc.abstractmethod`.
- A class with abstract methods acts as a **template/contract**: subclasses are expected to implement all abstract methods, but — important nuance from the notebook — Python does **not strictly enforce** this unless the base class also inherits from `abc.ABC` (or uses `ABCMeta` as metaclass). In the plain `abc.abstractmethod`-without-`ABC` example shown, subclasses that skip implementing a method don't error out immediately; the proper enforcement pattern is:
```python
from abc import ABC, abstractmethod
class Shape(ABC):
    @abstractmethod
    def calculate_area(self):
        pass
```
With `ABC` as base, instantiating a subclass that hasn't implemented **all** abstract methods raises `TypeError: Can't instantiate abstract class ... with abstract method ...`.

### Interview Q&A
**Q: What is method overriding and how is it different from overloading?**
A: Overriding happens **across parent-child classes** — same method signature, child's version replaces parent's. Overloading (multiple same-named methods with different signatures in the same class) is **not truly supported** in Python — the last-defined version simply replaces earlier ones in the same class.

**Q: How does Python resolve the diamond problem?**
A: Using MRO (Method Resolution Order), computed via the C3 linearization algorithm — you can inspect it via `ClassName.__mro__`.

**Q: How do you enforce that a subclass implements all abstract methods?**
A: Inherit the abstract base class from `abc.ABC` and decorate the required methods with `@abstractmethod`. Attempting to instantiate a subclass missing any of them raises `TypeError`.

**Q: What's the practical benefit of abstraction?**
A: It defines a consistent interface/contract across multiple related classes (e.g., every course type must implement `student_details`, `student_marks`) while hiding how each one is implemented internally.

---

## 8. Polymorphism & Encapsulation

### Polymorphism
- "Poly" = many, "morphism" = forms. The same interface (function/operator/method name) behaves differently depending on the object/data type.
- Built-in example: `len()` works on strings, lists, tuples; `+` means addition for numbers but concatenation for strings/lists.
- **Method overloading**: Python doesn't truly support it — if you define the same method name multiple times in a class (with different signatures), only the **last definition** survives; earlier ones are simply overwritten, not "chosen based on args" as in languages like Java/C++.
- **Method overriding**: occurs **between parent and child classes** — same method name/signature, child version executes when called on a child instance. This is Python's real mechanism for polymorphic behavior across a class hierarchy (e.g., iterating over different objects and calling the same method name, each executing its own class-specific version — sometimes called "duck typing" polymorphism).

### Encapsulation
- Bundling data + methods together inside a class, and controlling access via **access modifiers**:

| Modifier | Syntax | Accessible from |
|---|---|---|
| Public | `self.name` | Anywhere |
| Protected | `self._name` | Class + subclasses (convention only, not enforced) |
| Private | `self.__name` | Only within the defining class (via **name mangling**) |

- **Name mangling**: `self.__degree` inside class `Student` is internally renamed to `self._Student__degree`. You *can* still access it from outside via `obj._Student__degree`, but this breaks the intended encapsulation and is discouraged — it's a "you can, but shouldn't" mechanism, not true security.
- Private **methods** work the same way (`__private_method`) — accessible internally, or externally only via mangled name `obj._ClassName__private_method()`. Best practice: expose a **public wrapper method** that internally calls the private method, rather than relying on name-mangled external access.
- Common real-world encapsulation pattern: getters/setters that validate input before mutating private state (e.g., `set_speed` clamps negative values to 0; a `Bank` class only allows `withdraw()` to succeed if `balance >= amount`, otherwise returns `False`).
- **Protected members** (single underscore `_`) are accessible in subclasses directly, and the convention signals "internal use, but subclasses may need it" — e.g., a `College` base class exposing `_college_name`, accessed directly by a `Student(College)` subclass.
- Accessing parent members from a child: either `ParentClass.__init__(self)` (explicit) or `super().__init__()` (preferred, especially with multiple inheritance/MRO).

### Interview Q&A
**Q: Does Python support method overloading like Java?**
A: Not natively — defining multiple methods with the same name just overwrites earlier ones. You simulate it with default arguments, `*args`/`**kwargs`, or `functools.singledispatch`.

**Q: How does Python enforce "private" variables?**
A: Via **name mangling** (`__var` → `_ClassName__var`), which discourages accidental external access but doesn't make it impossible — Python relies on convention, not strict access control.

**Q: Difference between protected and private?**
A: Protected (`_var`) is a convention meaning "internal/subclass use" but freely accessible; private (`__var`) is name-mangled, making accidental external access harder (though still technically possible).

**Q: Give a practical encapsulation example.**
A: A `Bank` class where `__balance` is private, and `deposit()`/`withdraw()` are the only sanctioned ways to modify it — `withdraw()` also enforces a business rule (sufficient balance) before allowing mutation.

---

## 9. Dunder (Magic) Methods

- "Dunder" = **d**ouble **under**score. These are special methods surrounded by `__` that Python calls implicitly to implement built-in behavior (operator overloading, object construction, string representation, etc.). Inspect all of a type's dunders via `dir(str)`, `dir(int)`, etc.

| Dunder | Purpose | Triggered by |
|---|---|---|
| `__init__` | Initializes a new instance (sets initial state) | `ClassName(args)` |
| `__new__` | Actually **creates** the instance (runs *before* `__init__`) | `ClassName(args)` |
| `__str__` | Human-readable string representation | `print(obj)`, `str(obj)` |
| `__repr__` | Unambiguous representation, ideally one that could recreate the object | `repr(obj)`, interactive shell echo |
| `__eq__` | Defines behavior of `==` | `obj1 == obj2` |
| `__add__` | Defines behavior of `+` | `obj1 + obj2` |
| `__call__` | Makes an instance callable like a function | `obj()` |

- Without `__str__`/`__repr__` defined, `print(obj)` shows a default hex-memory-address representation.
- `__eq__` example — customizing equality for a `Point` class to compare `x` and `y` rather than object identity:
```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y
```
- `__add__` example — enables the `+` operator on custom objects (operator overloading):
```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y
    def __add__(self, other):
        return Point(self.x + other.x, self.y + other.y)
```
- `__call__` is what makes **class-based decorators** possible — when you use a class as a decorator (`@MyDecorator`), calling the decorated "function" actually calls `MyDecorator.__call__`.

### Interview Q&A
**Q: Difference between `__str__` and `__repr__`?**
A: `__str__` is meant for a readable, user-facing description (`print(obj)`); `__repr__` is meant to be unambiguous/developer-facing, ideally something that could recreate the object (shown in the shell/REPL and used as fallback if `__str__` isn't defined).

**Q: What's the difference between `__new__` and `__init__`?**
A: `__new__` creates and returns the new (uninitialized) instance; `__init__` then initializes that instance's attributes. `__new__` runs first.

**Q: How does Python implement operator overloading?**
A: Via dunder methods — e.g. `a + b` internally calls `a.__add__(b)`; `a == b` calls `a.__eq__(b)`. You customize operator behavior for your own classes by overriding these.

**Q: What makes an object "callable"?**
A: Defining `__call__` on its class — then `obj()` invokes `obj.__call__()`.

---

## 10. Decorators

- A decorator **modifies or extends the behavior of a function/class without changing its original source code** — analogy: decorating a room (lights, posters) enhances it without altering its structure.
- Common use cases: logging, timing/execution measurement, caching, input validation, access control, code reuse.

### Function Decorators
- A decorator is a function that takes another function as an argument and returns a new (usually nested "wrapper") function that adds behavior before/after calling the original.
```python
def my_decorator(func):
    def wrapper():
        print("Before")
        func()
        print("After")
    return wrapper

@my_decorator
def say_hello():
    print("Hello")

say_hello()  # equivalent to: say_hello = my_decorator(say_hello); say_hello()
```
- `@decorator` syntax is just syntactic sugar for `func = decorator(func)`.
- Practical example: a **timer decorator** that wraps a function, records `time.time()` before and after, and prints the elapsed duration.

### Class Decorators
- A class can act as a decorator if it implements `__init__` (to store the original function) and `__call__` (to define what happens when the "decorated function" is actually invoked).
```python
class MyDecorator:
    def __init__(self, func):
        self.func = func
    def __call__(self):
        print("Before")
        self.func()
        print("After")

@MyDecorator
def say_hello():
    print("Hello")

say_hello()
```
- Execution order: `@MyDecorator` on `say_hello` triggers `MyDecorator.__init__(func=say_hello)` immediately at decoration time; then calling `say_hello()` actually triggers `__call__`.

### Built-in Decorators Recap
- `@classmethod`, `@staticmethod`, `@property` (all covered above) are also decorators — they just modify how a method is bound/accessed rather than wrapping extra logic around it.

### Interview Q&A
**Q: What problem do decorators solve?**
A: They let you add cross-cutting behavior (timing, logging, validation, caching) to multiple functions **without duplicating that logic inside each function** — separating "what a function does" from "how it's invoked/monitored."

**Q: What does `@my_decorator` actually do under the hood?**
A: It's shorthand for `func = my_decorator(func)` — replacing the original function with whatever the decorator returns (commonly a wrapper function).

**Q: Can a class be used as a decorator? How?**
A: Yes — the class needs `__init__` (to capture the function being decorated) and `__call__` (to define the enhanced behavior when the decorated name is invoked).

**Q: Why do decorators typically use a nested `wrapper` function?**
A: The decorator itself runs once (at decoration time) to set things up and return the wrapper; the `wrapper` function is what actually executes each time the decorated function is called, allowing "before" and "after" logic around the original call.

---

## Quick-Fire Conceptual Recap (rapid interview review)

- **Class vs Object**: blueprint vs instance.
- **`self` vs `cls`**: instance reference vs class reference.
- **`__init__` vs `__new__`**: initializes vs creates.
- **Encapsulation**: hides data (public/protected/private via naming convention + name mangling).
- **Inheritance**: reuse of parent behavior in child classes; single/multiple/multilevel/hierarchical/hybrid.
- **Polymorphism**: same interface, different behavior — via overriding (real) or fake "overloading" (last definition wins).
- **Abstraction**: expose interface, hide implementation — via `abc.ABC` + `@abstractmethod`.
- **Iterator protocol**: `__iter__` + `__next__`, raises `StopIteration` when exhausted.
- **Generators**: `yield`-based, lazy, memory-efficient, resumable functions.
- **Decorators**: wrap/extend function or class behavior without modifying source, via `@decorator` syntax (functions or `__call__`-based classes).
- **`map`/`filter`/`reduce`**: transform / select / fold an iterable respectively.
- **Property decorators**: expose private attributes safely with optional validation via getter/setter/deleter, keeping attribute-style access syntax.

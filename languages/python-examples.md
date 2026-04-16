# Common AI Pitfalls in Python

During my evaluations on the IntelliForge RLHF Studio, I've identified several recurring mistakes AI models make when generating Python code. Below are the most frequent issues and my standard evaluation approach for each.

## 1. Mutable Default Arguments
**The Problem:** AI models frequently use lists or dictionaries as default parameters in functions.
```python
# Bad AI Code
def append_to_list(value, my_list=[]):
    my_list.append(value)
    return my_list
```
**The Implication:** In Python, default arguments are evaluated *once* at function definition, not at invocation. This means `my_list` is shared across all calls that don't provide a list, leading to severe data leakage between function calls.
**My Corrective Feedback:** Models must be penalized for this and directed to use `None`, instantiating the mutable type inside the function body.
```python
# Expected Standard
def append_to_list(value, my_list=None):
    if my_list is None:
        my_list = []
    my_list.append(value)
    return my_list
```

## 2. Resource Exhaustion (File/DB handling)
**The Problem:** AI often opens files or database connections and relies on the garbage collector to close them.
```python
# Bad AI Code
def read_config():
    f = open('config.json', 'r')
    return json.load(f)
```
**The Implication:** If an exception occurs during `json.load`, or simply over thousands of iterations, this leaks file descriptors and will crash the OS process.
**My Corrective Feedback:** Always enforce `with` context managers for determinism.

## 3. SQL Injection (f-strings in queries)
**The Problem:** With the rise of formatted strings, models incorrectly apply them to SQL queries.
```python
# Bad AI Code
cursor.execute(f"UPDATE users SET age = {new_age} WHERE name = '{name}'")
```
**The Implication:** This is a zero-day vulnerability out of the box.
**My Corrective Feedback:** Ensure strict separation of query structure and data using parameterized inputs (e.g., `?` or `%s` depending on the driver).

## Summary
Python's forgiving syntax often masks these severe architectural flaws. A high-quality AI code reviewer must ensure the model respects Python's memory lifecycle and standard PEP-8 enterprise security patterns.

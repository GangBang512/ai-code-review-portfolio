# Sample: Python Evaluation (Security & Refactoring)

## Objective
Evaluate two AI-generated responses refactoring a Python application logging function. Determine which model provides the most secure and idiomatically robust solution.

## The Prompt
> "Refactor the following Python snippet. It is supposed to connect to a local sqlite database and retrieve a user by username."
> 
> ```python
> import sqlite3
> def get_user(username):
>     conn = sqlite3.connect('users.db')
>     cursor = conn.cursor()
>     query = f"SELECT * FROM users WHERE username = '{username}'"
>     cursor.execute(query)
>     user = cursor.fetchone()
>     conn.close()
>     return user
> ```

---

## AI Responses

### Model A
```python
import sqlite3

def get_user(username: str) -> tuple | None:
    try:
        conn = sqlite3.connect('users.db')
        cursor = conn.cursor()
        query = "SELECT * FROM users WHERE username = ?"
        cursor.execute(query, (username,))
        user = cursor.fetchone()
        return user
    except sqlite3.Error as e:
        print(f"Database error: {e}")
        return None
    finally:
        if 'conn' in locals():
            conn.close()
```

### Model B
```python
import sqlite3

def get_user(username):
    # Fixed SQL injection issue using parameters
    with sqlite3.connect('users.db') as conn:
        cursor = conn.cursor()
        query = "SELECT * FROM users WHERE username = %s"
        cursor.execute(query, username)
        return cursor.fetchone()
```

---

## My Evaluation & Justification

**Ranking:** Model A > Model B

**Justification Overview:**
Model A successfully mitigates the critical SQL injection vulnerability while properly handling the database connection lifecycle natively. Model B introduces a breaking syntax error specific to SQLite. 

### Claim, Evidence, Implication & Actionable Insights

**1. Claim (Security & Correctness):** Model A correctly mitigates the SQL Injection vulnerability using the proper SQLite parameterization, whereas Model B uses the wrong parameterization syntax for `sqlite3`.
*   **Evidence:** Model A uses `cursor.execute("SELECT... username = ?", (username,))` which is correct. Model B uses `%s` which is valid for `psycopg2` or `mysql-connector` but throws a syntax error in bare Python `sqlite3`.
*   **Implication:** Model B's code will crash immediately in a production environment due to an incorrect binding token, introducing a hard service failure despite solving the injection vulnerability conceptually.
*   **Actionable:** Model B needs to be penalized heavily for code that does not run, and must be retrained to recognize `?` as standard `sqlite3` driver parameterized binding.

**2. Claim (Resource Management):** Model A uses a `try/except/finally` block that manages resources reasonably but is slightly verbose, while Model B attempts a cleaner context manager (`with sqlite3.connect(...)`).
*   **Evidence:** Model B uses `with sqlite3.connect('users.db') as conn:`.
*   **Implication:** Model B's context manager usage on the `Connection` object only manages *transactions* (commit/rollback on exit), but it actually **does not close the connection** in the Python `sqlite3` library. This is a common AI hallucination that leads to connection pool exhaustion.
*   **Actionable:** Model A's explicit `.close()` in a `finally` block is safer here, but contextually, `contextlib.closing` would be the most idiomatic.

**Conclusion:** 
Model A is heavily preferred as the code executes safely, correctly secures the query, adds type hinting (`str -> tuple | None`), and accurately closes the connection.

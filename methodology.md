# AI Code Review Methodology

Evaluating AI-generated code requires a structured, multi-dimensional approach. While functional correctness is baseline, an expert must also consider security vulnerabilities, idiomatic practices, long-term maintainability, and educational value for human readers.

I apply a consistent **3-Pass Review Strategy** to every evaluation.

---

## The 3-Pass Review Strategy

### Pass 1: The General Scan (Correctness & Requirements)
*   **Prompt Alignment:** Did the model follow *all* explicitly stated constraints? (e.g., using a specific library, preventing breaking changes, adhering to API signatures).
*   **Functional Soundness:** Does the code actually work under standard conditions? Are there obvious logic errors or compilation issues?
*   **Edge Cases:** Are boundaries handled correctly? Will the code fail defensively if given null inputs, empty collections, or excessively large data?

### Pass 2: The Deep-Dive (Security & Architecture)
*   **Security Posture:** Is the application vulnerable to OWASP Top 10 flaws? (e.g., SQL Injection, Cross-Site Scripting, improper encryption, weak authentication, or revealing stack traces).
*   **Resource Management:** Are file handles closed, database connections released, and memory leaks avoided?
*   **Concurrency:** Are shared resources accessed safely? Are race conditions, deadlocks, and `ConcurrentModificationExceptions` preemptively addressed?

### Pass 3: The Polish (Performance & Idiomatic Nuance)
*   **Language Idioms:** Does the Python code look "Pythonic"? Does the Java code adhere to standard naming conventions and standard library utilities?
*   **Performance Bottlenecks:** Are there excessive re-renders (in React), N+1 queries, or inefficient `O(N^2)` loops that could be natively optimized?
*   **Readability & Maintainability:** Are documentation docstrings appropriate? Is the code inherently readable without relying on heavily verbose internal comments?

---

## Evaluation Formatting

To provide high-quality feedback to researchers or RLHF teams, my evaluations are structured on the **[Claim → Evidence → Implication → Actionable]** model.

1.  **Claim:** A direct statement about the state of the code. (e.g., *"Model A introduces a severe security vulnerability."*)
2.  **Evidence:** The exact code snippet or line reference proving the claim. (e.g., *"Line 12 formats user input directly into the query template `f'SELECT * FROM users WHERE id = {user_id}'`."*)
3.  **Implication:** Why this matters in a highly impactful way. (e.g., *"An attacker can pass `' OR 1=1;--` as the `user_id` to bypass authentication and dump the users table."*)
4.  **Actionable Feedback / Fix:** What needs to be done. (e.g., *"Model A must utilize parameterized queries using `cursor.execute('SELECT * FROM users WHERE id = %s', (user_id,))` to safely bind the variable."*)

Through this standardized rigorous approach, I ensure reviews elevate safety, stability, and aesthetic quality.

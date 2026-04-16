# AI Code Reviewer Portfolio

Welcome to my AI Code Review portfolio! I am **Gangabai Bhutada**, a trained AI Code Reviewer with a strong foundation in evaluating, ranking, and improving AI-generated code.

I practice and perform evaluations on the [IntelliForge RLHF Studio](https://rlhf-studio.intelliforge.tech/), rigorously applying standard coding best practices, security principles, and idiomatic correctness.

## Structure of this Repository

This repository showcases my methodology and various sample tasks.

*   [**`methodology.md`**](./methodology.md): Outlines my standard 3-pass review approach (General scan, Security & Correctness Deep-dive, Performance & Idiomatic Practices).
*   [**`samples/`**](./samples/): Contains step-by-step breakdowns of RLHF reviews I conduct, including prompts, AI responses, and my comprehensive justification using the Claim → Evidence → Implication → Actionable structure.
*   [**`languages/`**](./languages/): Highlights common mistakes made by AI models in specific languages (Python, JavaScript, Java) and my standard corrective feedback.

## Highlighted Samples

1.  **[Python Security & Refactoring (Ranking Task)](./samples/ranking-task-1-python-security.md)**: Mitigating SQL Injection and handling missing error paths.
2.  **[Java Authentication Bug (Scoring Task)](./samples/scoring-task-1-java-auth.md)**: Debugging string comparison issues (`==` vs `.equals()`) and concurrency exceptions.
3.  **[C# Resource Management (Correction Task)](./samples/correction-task-1-csharp-memory.md)**: Fixing memory leaks and async boundaries.
4.  **[C# Configuration Loader (Scoring Task)](./samples/scoring-task-2-csharp-config.md)**: Identifying `IDisposable` leaks and null-safety issues.
5.  **[C# Async & LINQ (Ranking Task)](./samples/ranking-task-3-csharp-async.md)**: Troubleshooting `async void` crashes and deferred execution performance traps.
6.  **[React Component Performance (Ranking Task)](./samples/ranking-task-2-react-performance.md)**: Eliminating excessive re-renders and preventing unstable function references over multiple rendering cycles.

## Structured Data Samples (JSON)

I also provide structured versions of my evaluations for integration with RLHF pipelines:

*   **[Ranking Task (Python) JSON](./samples/json/ranking.json)**
*   **[Ranking Task (C# Async) JSON](./samples/json/ranking-csharp-async.json)**
*   **[Scoring Task (Java) JSON](./samples/json/scoring.json)**
*   **[Scoring Task (C#) JSON](./samples/json/scoring-csharp.json)**
*   **[Correction Task JSON](./samples/json/correction.json)**

## Core Review Competencies

-   **Security First:** Identifying OWASP top 10 vulnerabilities, unauthorized resource access, and injection flaws.
-   **Idiomatic Consistency:** Ensuring code looks like it was written by an expert in that specific language ecosystem.
-   **Constructive Nuance:** Going beyond "this is wrong" to providing clear implications and actionable next steps.

---

*Thank you for reviewing my portfolio.*
